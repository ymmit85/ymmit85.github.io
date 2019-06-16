---
layout: post
title: Updating firmware on Micron PCI SSD
date: 2015-02-11 12:35
author: ymmit85
comments: true
categories: [Dell, Micron, Posts, Uncategorized, VSAN, vSphere]
---
<span style="font-family:Arial, Helvetica, sans-serif;">Recently we had some new Dell R720s with Micron P420m SSDs arrive that are due to be installed in a VSAN cluster, during the process we needed to update the firmware on the cards before we commissioned the environment.</span>

<span style="font-family:Arial, Helvetica, sans-serif;">Before you go through this process make sure you read all the readme files and understand the whole process, follow this process at your own risk. </span>

<span style="font-family:Arial, Helvetica, sans-serif;">Firstly go to <a href="http://micron.com/dell">Micron.com/dell</a>. You want to download the three files shown below, the first is the firmware file, second is the utility to gather data from the SSD and update the firmware and lastly is the ESXi driver. </span>
<span style="font-family:Arial, Helvetica, sans-serif;">
</span>
<div class="separator" style="clear:both;text-align:center;"><a style="margin-left:1em;margin-right:1em;" href="https://ymmitsblog.files.wordpress.com/2015/02/49d7c-2015-02-11_11-44-25.png"><span style="font-family:Arial, Helvetica, sans-serif;"><img src="https://ymmitsblog.files.wordpress.com/2015/02/49d7c-2015-02-11_11-44-25.png" alt="" width="400" height="315" border="0" /></span></a></div>
<span style="font-family:Arial, Helvetica, sans-serif;">
</span><span style="font-family:Arial, Helvetica, sans-serif;">Once you have these unzip them all and copy the following files to your host. </span><span style="font-family:Arial, Helvetica, sans-serif;">
</span><span style="font-family:Arial, Helvetica, sans-serif;">
</span><span style="font-family:Arial, Helvetica, sans-serif;">vmw-esx55-micron-rssdm-2.20.11180.00.vib</span>
<span style="font-family:Arial, Helvetica, sans-serif;">Binary.ubi</span>
<span style="font-family:Arial, Helvetica, sans-serif;">MICRON_bootbank_mtip32xx-native_3.9.4-1OEM.550.0.0.1331820.vib</span>
<span style="font-family:Arial, Helvetica, sans-serif;">
</span><span style="font-family:Arial, Helvetica, sans-serif;">Then SSH to your host and run the following command to install RSSDM</span>
<span style="font-family:Arial, Helvetica, sans-serif;">
</span><strong><span style="font-family:Arial, Helvetica, sans-serif;">esxcli software vib install --viburl=vmw-esx55-micron-rssdm-2.20.11180.00.vib</span></strong>
<span style="font-family:Arial, Helvetica, sans-serif;">
</span><span style="font-family:Arial, Helvetica, sans-serif;">Once installed you can use the RSSDM app to get stats from your SSDs.</span>
<div><strong><span style="font-family:Arial, Helvetica, sans-serif;">/opt/micron/bin # ./rssdm -L</span></strong></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Drive Id             : 0</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Device Name          : mtip_rssd0</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Model No             : Micron P420m-MTFDGAR700MAX</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Serial No            : 00000000</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">FW-Rev               : B2085108</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Total Size           : 700.15GB</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Drive Status         : Drive is in good health</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">PCI Path (B:D.F)     : 04:00.0</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Vendor               : Micron</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Temp(C)              : 47</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Drive Id             : 1</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Device Name          : mtip_rssd1</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Model No             : Micron P420m-MTFDGAR700MAX</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Serial No            : 00000000</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">FW-Rev               : B2085108</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Total Size           : 700.15GB</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Drive Status         : Drive is in good health</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">PCI Path (B:D.F)     : 44:00.0</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Vendor               : Micron</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Temp(C)              : 51</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Drive information is retrieved successfully</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">CMD_STATUS   : Success</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">STATUS_CODE  : 0</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Copyright (C) 2014 Micron Technology, Inc.</span></div>
</div>
<div><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">From this info we can see that the cards are running firmware version B2085108, as we are using these hosts with VSAN we need to have them on B210 or greater for it to be supported.</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Next we will install the newer ESXi driver, run the following command.</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div>
<div><strong><span style="font-family:Arial, Helvetica, sans-serif;">esxcli software vib install --viburl=MICRON_bootbank_mtip32xx-native_3.9.4-1OEM.550.0.0.1331820.vib  --force</span></strong></div>
</div>
<div><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">This will require a reboot of the host to take effect. Once your host is rebooted you can check that the latest driver is installed.</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div>
<div><strong><span style="font-family:Arial, Helvetica, sans-serif;">esxcli software vib list | grep mtip32xx</span></strong></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">mtip32xx-native                3.9.4-1OEM.550.0.0.1331820             MICRON    VMwareCertified   2015-02-05</span></div>
</div>
<div><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Now all that's left to do is to update the firmware on both cards. Use RSSDM -L to determine the drive IDs of your cards, for mine it's 0 and 1.</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div>
<div><strong><span style="font-family:Arial, Helvetica, sans-serif;">/opt/micron/bin # ./rssdm -T /tmp/SP\ 145.03.08\ P420m\ FW\ Binary.ubi -n 1</span></strong></div>
<div></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Trying to update for drive 1, from current firmware B208.51.08 to B212.05.08.</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Are you sure you want to continue(Y|N):Y</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Unified Image update for drive 1 will take a few seconds to complete.</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Please wait</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">........................................................................</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Drive Id     : 1</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Unified image update operation completed successfully.</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Power cycle the server for the downloaded Unified Image to take effect.</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">CMD_STATUS   : Success</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">STATUS_CODE  : 0</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Copyright (C) 2014 Micron Technology, Inc</span></div>
</div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Post reboot check the status of the cards.</span></div>
<div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">/opt/micron/bin # ./rssdm -L</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Drive Id             : 0</span></div>
<div>Device Name          : mtip_rssd0</div>
<div>Model No             : Micron P420m-MTFDGAR700MAX</div>
<div>Serial No            : 00000000</div>
<div>FW-Rev               : B2120508</div>
<div>Total Size           : 700.15GB</div>
<div>Drive Status         : Drive is in good health</div>
<div>PCI Path (B:D.F)     : 04:00.0</div>
<div>Vendor               : Micron</div>
<div>Temp(C)              : 46</div>
<div></div>
<div>Drive Id             : 1</div>
<div>Device Name          : mtip_rssd1</div>
<div>Model No             : Micron P420m-MTFDGAR700MAX</div>
<div>Serial No            : 00000000</div>
<div>FW-Rev               : B2120508</div>
<div>Total Size           : 700.15GB</div>
<div>Drive Status         : Drive is in good health</div>
<div>PCI Path (B:D.F)     : 44:00.0</div>
<div>Vendor               : Micron</div>
<div>Temp(C)              : 49</div>
<div></div>
<div>Drive information is retrieved successfully</div>
<div>CMD_STATUS   : Success</div>
<div>STATUS_CODE  : 0</div>
<div></div>
<div>Copyright (C) 2014 Micron Technology, Inc.</div>
</div>
<div><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">Once you have done this for both cards, give your host a reboot. After the hosts have rebooted according to the documentation you will need to 'secure erase' the cards. This bit is a PIA if you have data on the drives (you will need to migrate it off or in the case of VSAN - delete the disk group). Again, SSH to your hosts and run the following command, (RSSDM will give you the drive number and the password is ffff.</span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div><strong><span style="font-family:Arial, Helvetica, sans-serif;">rssdm –X –n -p </span></strong></div>
<div></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;">After the cards have been erased your good to go. Hopefully the process of erasing the cards wont be required again in the future as it will add some time to the overall process but at the end of the day I'm sure it needs to be done for a reason. </span></div>
<div><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
