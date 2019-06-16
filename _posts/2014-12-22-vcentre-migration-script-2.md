---
layout: post
title: vCentre Migration Script
date: 2014-12-22 17:09
author: ymmit85
comments: true
categories: [Posts, PowerCLI, Tools, Uncategorized, vSphere]
---
<div class="p1"><span class="s1">Here is a little script that I put together recently to make the process of migrating VMs between two vCentre servers easier and to also make some changes to the VMs during the process, for us this was to disable CPU/MEM HotAdd and to set disks that were used for page files to independent persistent mode to stop them being backed up via VADP.</span></div>
<div class="p1"></div>
<div class="p1">The way we approached this vSphere 5.5 upgrade was to create a new vCentre server which had a bunch of new ESXi hosts on it (which also had access to the same storage as our 4.1 hosts and then during certain outage windows migrate VMs across).</div>
<div class="p1"><span class="s1">The migration is pretty easy in theory.. Power off the VM &gt; Unregister from source host &gt; register on destination host &gt; disable CPU/MEM HotAdd &gt; make the change to the required disk &gt; reconfigure the NIC to the vDS and then power it back on.</span>
<span class="s1">But this would take ages if this process were not automated and it also removes the risk of anyone accidentally clicking 'Delete from disk' which is right below the menu option to 'Unregister Virtual Machine'.</span></div>
<div class="p2"></div>
<div class="p1"><span class="s1">So to use this script make a csv file with this format:</span></div>
<div class="p2"></div>
<div class="p1"><span class="s1">VMName,DestinationCluster,DestinationvDSPG,IDiskSet,IDiskName</span></div>
<div class="p1"><span class="s1">xTest3,host2.lab.local,Dev-Test,y,Hard disk 2</span></div>
<div class="p1"><span class="s1">xTest2,host1.lab.local,Prod_v1,n,,</span></div>
<div class="p2"></div>
<div class="p1"><span class="s1">Where </span></div>
<div class="p1" style="padding-left:30px;"><span class="s1">- <strong>VMName</strong> is the name of the VM as it appears in vCentre</span></div>
<div class="p1" style="padding-left:30px;">- <strong>DestinationCluster</strong> is the name of the cluster you want the VM moved into</div>
<div class="p1" style="padding-left:30px;">- <strong>DestinationvDSPG</strong> is the name of the vDS portgroup to attach the VM to (this script  doesn't support VMs with multiple NICs)</div>
<div class="p1" style="padding-left:30px;">- <strong>IDiskSet</strong> set to 'y' if you want a disk set to independent mode</div>
<div class="p1" style="padding-left:30px;">- <strong>IDiskName</strong> the name of the hard disk to set to independent mode (eg: Hard Disk 2)</div>
<div class="p2"></div>
<div class="p1"><span class="s1">Connect your PowerCLI session to both vCentre servers, run the script, select the csv file and off it goes. It's a pretty quick process, once the VM is moved you could kick off another script to bulk upgrade VMtools or anything else you need done. </span>
<span class="s1">
</span><span class="s1">Note: Make sure you test this out in a test environment, I can't take responsibility for anything going sour as a result of you running this script.</span>
<span class="s1">
</span></div>
<pre class="brush:powershell">&lt;#
.DESCRIPTION
    Migrates powered off VMs betten vCentre Hosts, disables cpu/memory hotadd, reconfigures for vDS &amp; if required sets specified disk to independent persistantmode.
        
.USAGE
    The following Parameters are required;
        $VM = Name of VM as lsited in vCentre inventory
        $DestinationHost = Name of destination vCentre Cluster
        $DestinationvDSPG = Name of vDS portgroup
        $IDiskSet = Y or N value to set a disk to Independent persistent mode
        $IDiskName = Hard disk to set 

    Source CSV file to be in following format;

VMName,DestinationCluster,DestinationvDSPG,IDiskSet,IDiskName
xTest3,host2.lab.local,Dev-Test,y,Hard disk 2
xTest2,host1.lab.local,Prod_v1,n,,


.NOTES
    File Name     : Migrate_VM_Between_Clusters_v2.0.ps1
    Prerequisite  : .Net FrameWork 4.5.x
                  : Windows Management FrameWork 4.0
                  : VMware PowerCLI 5.5 R2
#&gt;

# Exit if PowerShell version not equal to 4
If($PSVersionTable.PSVersion.Major -ne 4)
    {
        Write-Host "Incorrect Version of PowerShell"
        Write-Host "Update here http://www.microsoft.com/en-us/download/details.aspx?id=40855"
        Start-Sleep 30
        Exit-PSSession 
    } 

Function Disable-MemHotAdd($vm)
    {
        $vmview = Get-VM $vm | Get-View 
        $vmConfigSpec = New-Object VMware.Vim.VirtualMachineConfigSpec
        $extra = New-Object VMware.Vim.optionvalue
        $extra.Key="mem.hotadd"
        $extra.Value="false"
        $vmConfigSpec.extraconfig += $extra
        $vmview.ReconfigVM($vmConfigSpec)
    }

Function Disable-vCpuHotAdd($vm)
    {
        $vmview = Get-vm $vm | Get-View 
        $vmConfigSpec = New-Object VMware.Vim.VirtualMachineConfigSpec
        $extra = New-Object VMware.Vim.optionvalue
        $extra.Key="vcpu.hotadd"
        $extra.Value="false"
        $vmConfigSpec.extraconfig += $extra
        $vmview.ReconfigVM($vmConfigSpec)
    }

$OutputCSV = @()
$gObjSrc = @()

# Open CSV file
    [System.Reflection.Assembly]::LoadWithPartialName("System.windows.forms") | Out-Null
    $gSourceCsv = New-Object system.windows.forms.openfiledialog
    $gSourceCsv.InitialDirectory = 'c:\'
    $gSourceCsv.Filter = "csv files (*.csv)|*.csv|All files (*.*)|*.*"
    $gSourceCsv.MultiSelect = $false
    $gSourceCsv.showdialog()
    $gSourceCsv.filenames 

$gObjSrc = import-csv -Path $gSourceCsv.fileName

$gObjSrc  | ForEach-Object {
        $VM = $_.VMName
        $DestinationCluster = $_.DestinationCluster
        $DestinationvDSPG = $_.DestinationvDSPG
        $IDiskSet = $_.IDiskSet
        $IDiskName = $_.IDiskName
        
    #Shutdown VMs &amp; get virtual nic info
    ForEach ($MigrateVM in $VM) {
            Write-Host "Shutting down $MigrateVM"
            Get-VM -Name $VM | Shutdown-VMGuest -Confirm:$false | Out-Null
            while ((Get-VM $VM).PowerState -ne "PoweredOff") { Start-Sleep -Seconds 2 }
            Write-Host "Getting NIC details of $MigrateVM"
            $NA = Get-NetworkAdapter -VM $MigrateVM
            }

    # Migration of VM's
    ForEach ($MigrateVM in $VM) {
            Write-Host "Migrating $MigrateVM"
            Get-VM -Name $VM | Move-VM -Destination $DestinationCluster -Confirm:$false | Out-Null
            Write-Host "Migrating $MigrateVM to dVS"
            Set-NetworkAdapter -NetworkAdapter $NA -NetworkName $DestinationvDSPG -Confirm:$false | Out-Null
            Write-Host "Disabling HotAdd on $MigrateVM"
            Disable-vCpuHotAdd $MigrateVM
            Disable-MemHotAdd $MigrateVM
                If ($IDiskSet -eq "Y" -or $IDiskSet -eq "y") {
                    write-host "Setting $IDiskName to IndependentPersistent"
                    get-vm -Name $MigrateVM | Get-HardDisk -Name $IDiskName | Set-HardDisk -Persistence IndependentPersistent -Confirm:$false | Out-Null
                    Write-Host "Starting up $MigrateVM"
                    Start-VM -VM $MigrateVM | Out-Null
                    }
                If ($IDiskSet -eq "N" -or $IDiskSet -eq"n") {
                    Write-Host "Starting up $MigrateVM - No disk changes required"
                    Start-VM -VM $MigrateVM | Out-Null
                    }
                }
            }</pre>
