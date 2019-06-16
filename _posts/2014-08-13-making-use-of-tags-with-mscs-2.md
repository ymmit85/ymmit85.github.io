---
layout: post
title: Making use of tags with MSCS
date: 2014-08-13 20:17
author: ymmit85
comments: true
categories: [Posts, PowerCLI, Tools, Uncategorized, vSphere]
---
<span style="font-family:Arial, Helvetica, sans-serif;">After configuring a bunch of Microsoft Clusters recently I thought it might be handy to see in the vCentre client (web) what resources were on the VMs or better yet be able to search for the cluster resource - because at the end of the day that's what the application guys will be referring to.</span>

So this script is nothing flash, by using task scheduler to run it however often you want the data updated it will configure Categories/Tags in the vCentre client as follows;

<span style="font-family:Arial, Helvetica, sans-serif;">Category Name &gt; Cluster Name</span>
<span style="font-family:Arial, Helvetica, sans-serif;">Tag Name &gt;  Cluster Resource</span>
<span style="font-family:Arial, Helvetica, sans-serif;">Tag Description &gt; Cluster Resource State</span>
<div class="separator" style="clear:both;text-align:center;"><a style="margin-left:1em;margin-right:1em;" href="https://ymmitsblog.files.wordpress.com/2014/08/8ebbd-4.jpg"><img src="https://ymmitsblog.files.wordpress.com/2014/08/8ebbd-4.jpg" alt="" width="640" height="120" border="0" /></a></div>
<div class="separator" style="clear:both;text-align:center;"><a style="margin-left:1em;margin-right:1em;" href="https://ymmitsblog.files.wordpress.com/2014/08/adaf0-5.jpg"><img src="https://ymmitsblog.files.wordpress.com/2014/08/adaf0-5.jpg" alt="" width="640" height="164" border="0" /></a></div>
<div class="separator" style="clear:both;text-align:center;"><a style="margin-left:1em;margin-right:1em;" href="https://ymmitsblog.files.wordpress.com/2014/08/bdc32-3.png"><img src="https://ymmitsblog.files.wordpress.com/2014/08/bdc32-3.png" alt="" width="640" height="154" border="0" /></a></div>
<span style="font-family:Arial, Helvetica, sans-serif;">I've broken the script into two parts, the first will collect the information from the clusters and put a .csv file where the second script can pick it up and create the Tags.</span>
<span style="font-family:Arial, Helvetica, sans-serif;"><strong>Part 1</strong> - This script needs to run under as a user that has access to the clusters to monitor and if your outputting to a share it's handy to give it write access there also. (This needs the <a href="http://technet.microsoft.com/en-us/library/ee461009.aspx">failovercluster</a> module to work)</span>
<pre class="brush: powershell;">#List of clusters to interrogate
$ClusterList = "c:\temp\input\clusters.csv"
#Directory to output csv file with results (can be a network share) - EG: $Output = "c:\filename.csv" or "\\computer\share\filename.csv"
$Output = "c:\temp\cluster-info.csv"

#Import CSV file 
$ObjSrc = import-csv -Path $ClusterList
$Result = @()
    $ObjSrc  | ForEach-Object {
    $Clusters = $_.ClusterName
        foreach ($clname in $Clusters) {
        #Query each Cluster and get Cluster Group information
        $ClusterDetails = Get-ClusterGroup -Cluster $clname | Select @{N="Category";E={$_.Cluster}},@{N="Tag";E={$_.Name}},@{N="VM";E={$_.OwnerNode}},@{N="Description";E={$_.State}}
        $Result += $ClusterDetails
        }
    }
$Result | Export-Csv -Path $Output -NoTypeInformation</pre>
<span style="font-family:Arial, Helvetica, sans-serif;"><strong>Part 2</strong> - This script will read all the .csv files in the folder you specify and then do the following;</span>
<span style="font-family:Arial, Helvetica, sans-serif;">
</span><span style="font-family:Arial, Helvetica, sans-serif;">1. Create a Category for each cluster being monitored</span>
<span style="font-family:Arial, Helvetica, sans-serif;">2. Remove the Category if it's no longer being monitored</span>
<span style="font-family:Arial, Helvetica, sans-serif;">3. Remove all Tags the script has previously created</span>
<span style="font-family:Arial, Helvetica, sans-serif;">4. Recreate all Tags with names of cluster resources and the status</span>
<span style="font-family:Arial, Helvetica, sans-serif;">5. Assign Tags to VMs</span>
<pre class="brush: powershell;">$vCentre = "vcentre.local"
#$Category Description is used to identify the categories this script has created and will only modify these
$CategoryDescription = "Cluster_Status_Script"
$CSVSourceDir = "c:\temp\"
$CSVData = @()
$DataFiles = @()
$DataFiles = Get-ChildItem $CSVSourceDir -Filter *.csv

Connect-VIServer $vCentre

ForEach ($DataFilesItem in $DataFiles)
    {
        $CSVData += Import-Csv -Path $CSVSourceDir\$DataFilesItem
      }

 #Create new Tag categories from input
    foreach ($NewCategory in $CSVData.Category) {
        $CurrentCategory = Get-TagCategory | select Name
            if ($CurrentCategory.name -notcontains $NewCategory) {
                New-TagCategory -Name $NewCategory -Cardinality Multiple -EntityType VirtualMachine -Description $CategoryDescription -ErrorAction Ignore | Out-Null 
            }
    }
    
# Remove Tag category if no longer in input - removes all associated tags
    $CurrentCategory = Get-TagCategory | Where-Object {$_.Description -like $CategoryDescription} | select Name
        foreach ($cc in $CurrentCategory) {
            if ($CSVData.Category -notcontains $cc.name) {
                Remove-TagCategory -Category $cc.name -Confirm:$false
            }
        }

#Remove Tags
    $CurrentCategory = Get-TagCategory | Where-Object {$_.Description -like $CategoryDescription}
    foreach ($rc in $CurrentCategory) {
        Get-tag -Category $rc | Remove-Tag -Confirm:$false
    } 

#Create Tags
    foreach ($NewTag in $CSVData) {
        New-Tag -Name $NewTag.Tag -Category $NewTag.Category -Description $NewTag.Description | Out-Null
    }

#Assign Tags to VMs
    foreach ($NewAssignment in $CSVData) {
        foreach ($VM in $NewAssignment.VM) {
            $NTA = Get-Tag -name $NewAssignment.Tag -Category $NewAssignment.Category
            New-TagAssignment -tag $NTA -Entity $VM | Out-Null
        }
    }</pre>
