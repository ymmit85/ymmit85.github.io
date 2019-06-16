---
layout: post
title: Dell OpenManage Integration for VMware vCentre
date: 2014-09-08 20:04
author: ymmit85
comments: true
categories: [Dell, Posts, Review, Tools, Uncategorized, vSphere]
---
<span style="font-family:Tahoma;orphans:2;text-align:-webkit-auto;widows:2;">Recently as part of a vSphere 5.5 upgrade we deployed v2 of the Dell vCentre Integration tool to allow low level monitoring of the hosts and to also simplify the process of firmware upgrade and device inventory. This is just a bit of an overview of what it looks like and how it can make life easier for those with Dell hardware in their environment.</span>

Dell released v2 of this tool a few months back which had full support for the vSphere Web Client and other improvements in the deployment of firmware updates.
<div style="font-family:Tahoma;orphans:2;text-align:-webkit-auto;widows:2;">It’s pretty straightforward to setup (although the process of acquiring the software and licences after the purchase could be significantly streamlined) just download the ovf package and deploy it where required, configure management IP address, set the root password, upload the licence file and register it to your vCentre server/s.</div>
<div style="font-family:Tahoma;orphans:2;text-align:-webkit-auto;widows:2;"></div>
<div style="font-family:Tahoma;orphans:2;text-align:-webkit-auto;widows:2;">Once it’s registered and deployed its plugin you will see the following views within the vCentre client:</div>
<table class="tr-caption-container" style="margin-left:auto;margin-right:auto;text-align:center;" cellspacing="0" cellpadding="0" align="center">
<tbody>
<tr>
<td style="text-align:center;"><a style="margin-left:auto;margin-right:auto;" href="https://ymmitsblog.files.wordpress.com/2014/09/da633-2014-09-05_21-57-47.png"><img src="https://ymmitsblog.files.wordpress.com/2014/09/da633-2014-09-05_21-57-47.png" alt="" width="640" height="120" border="0" /></a></td>
</tr>
<tr>
<td class="tr-caption" style="text-align:center;">List of all M1000e chassis within your environment.</td>
</tr>
</tbody>
</table>
<div class="separator" style="clear:both;text-align:center;"></div>
<table class="tr-caption-container" style="margin-left:auto;margin-right:auto;text-align:center;" cellspacing="0" cellpadding="0" align="center">
<tbody>
<tr>
<td style="text-align:center;"><a style="margin-left:auto;margin-right:auto;" href="https://ymmitsblog.files.wordpress.com/2014/09/004d6-2014-09-05_21-54-48.png"><img src="https://ymmitsblog.files.wordpress.com/2014/09/004d6-2014-09-05_21-54-48.png" alt="" width="640" height="328" border="0" /></a></td>
</tr>
<tr>
<td class="tr-caption" style="text-align:center;">Quick links to the management consoles and service tags.</td>
</tr>
</tbody>
</table>
<table class="tr-caption-container" style="margin-left:auto;margin-right:auto;text-align:center;" cellspacing="0" cellpadding="0" align="center">
<tbody>
<tr>
<td style="text-align:center;"><a style="margin-left:auto;margin-right:auto;" href="https://ymmitsblog.files.wordpress.com/2014/09/f33a2-2014-09-05_21-53-44.png"><img src="https://ymmitsblog.files.wordpress.com/2014/09/f33a2-2014-09-05_21-53-44.png" alt="" width="640" height="256" border="0" /></a></td>
</tr>
<tr>
<td class="tr-caption" style="text-align:center;">Firmware versions of all components within an M620.</td>
</tr>
</tbody>
</table>
<div style="font-family:Tahoma;orphans:2;text-align:-webkit-auto;widows:2;"><span style="text-align:-webkit-auto;">Once it has completed it's first inventory job you will see detailed information about the hosts, which chassis &amp; slot it is located in and what firmware is installed on the various components. </span></div>
<div style="font-family:Tahoma;orphans:2;text-align:-webkit-auto;widows:2;"></div>
<div style="font-family:Tahoma;orphans:2;text-align:-webkit-auto;widows:2;">Also many additional alarms are added into vCentre, allowing you to for example, configure an alarm to place a host into maintenance mode in the event a predictive memory failure has been detected or a device has failed. Personally this is probably one of the best features of the tool.</div>
<table class="tr-caption-container" style="margin-left:auto;margin-right:auto;text-align:center;" cellspacing="0" cellpadding="0" align="center">
<tbody>
<tr>
<td style="text-align:center;"><a style="margin-left:auto;margin-right:auto;" href="https://ymmitsblog.files.wordpress.com/2014/09/fe440-2014-09-05_21-53-23.png"><img src="https://ymmitsblog.files.wordpress.com/2014/09/fe440-2014-09-05_21-53-23.png" alt="" width="606" height="640" border="0" /></a></td>
</tr>
<tr>
<td class="tr-caption" style="text-align:center;">Pre-configured Dell vCentre alarms</td>
</tr>
</tbody>
</table>
<span style="text-align:-webkit-auto;">Another feature I’m keen on is the simplicity of updating firmware, this in the past has been a PIA. T</span><span style="text-align:-webkit-auto;">his is the process of installing new firmware on the Broadcom NIC in an M620 downloading the latest firmware files from Dell's online repository (if you can't get interweb access you can download the files by other means and upload them through the wizard from your local PC).</span>
<div style="font-family:Tahoma;orphans:2;text-align:-webkit-auto;widows:2;"></div>
<div style="font-family:Tahoma;orphans:2;text-align:-webkit-auto;widows:2;">
<table class="tr-caption-container" style="margin-left:auto;margin-right:auto;text-align:center;" cellspacing="0" cellpadding="0" align="center">
<tbody>
<tr>
<td style="text-align:center;"><a style="margin-left:auto;margin-right:auto;" href="https://ymmitsblog.files.wordpress.com/2014/09/e3d70-2014-09-05_21-51-47.png"><img src="https://ymmitsblog.files.wordpress.com/2014/09/e3d70-2014-09-05_21-51-47.png" alt="" width="614" height="640" border="0" /></a></td>
</tr>
<tr>
<td class="tr-caption" style="text-align:center;">1. Select the host that requires the firmware upgrade</td>
</tr>
</tbody>
</table>
<table class="tr-caption-container" style="margin-left:auto;margin-right:auto;text-align:center;" cellspacing="0" cellpadding="0" align="center">
<tbody>
<tr>
<td style="text-align:center;"><a style="margin-left:auto;margin-right:auto;" href="https://ymmitsblog.files.wordpress.com/2014/09/84a78-2014-09-05_21-51-38.png"><img src="https://ymmitsblog.files.wordpress.com/2014/09/84a78-2014-09-05_21-51-38.png" alt="" width="640" height="350" border="0" /></a></td>
</tr>
<tr>
<td class="tr-caption" style="text-align:center;">2. Ignore all this and just click next.</td>
</tr>
</tbody>
</table>
<table class="tr-caption-container" style="margin-left:auto;margin-right:auto;text-align:center;" cellspacing="0" cellpadding="0" align="center">
<tbody>
<tr>
<td style="text-align:center;"><a style="margin-left:auto;margin-right:auto;" href="https://ymmitsblog.files.wordpress.com/2014/09/8a899-2014-09-05_21-51-29.png"><img src="https://ymmitsblog.files.wordpress.com/2014/09/8a899-2014-09-05_21-51-29.png" alt="" width="640" height="352" border="0" /></a></td>
</tr>
<tr>
<td class="tr-caption" style="text-align:center;">3. Select the update bundle to apply from the online repository.</td>
</tr>
</tbody>
</table>
<table class="tr-caption-container" style="margin-left:auto;margin-right:auto;text-align:center;" cellspacing="0" cellpadding="0" align="center">
<tbody>
<tr>
<td style="text-align:center;"><a style="margin-left:auto;margin-right:auto;" href="https://ymmitsblog.files.wordpress.com/2014/09/04297-2014-09-05_21-50-51.png"><img src="https://ymmitsblog.files.wordpress.com/2014/09/04297-2014-09-05_21-50-51.png" alt="" width="640" height="242" border="0" /></a></td>
</tr>
<tr>
<td class="tr-caption" style="text-align:center;">4. Select the device to update, also choose if you want the previous firmware backed up incase you need to restore.</td>
</tr>
</tbody>
</table>
<table class="tr-caption-container" style="margin-left:auto;margin-right:auto;text-align:center;" cellspacing="0" cellpadding="0" align="center">
<tbody>
<tr>
<td style="text-align:center;"><a style="margin-left:auto;margin-right:auto;" href="https://ymmitsblog.files.wordpress.com/2014/09/c8077-2014-09-05_21-50-38.png"><img src="https://ymmitsblog.files.wordpress.com/2014/09/c8077-2014-09-05_21-50-38.png" alt="" width="640" height="242" border="0" /></a></td>
</tr>
<tr>
<td class="tr-caption" style="text-align:center;">5. Choose when you want the update to occur.</td>
</tr>
</tbody>
</table>
<table class="tr-caption-container" style="margin-left:auto;margin-right:auto;text-align:center;" cellspacing="0" cellpadding="0" align="center">
<tbody>
<tr>
<td style="text-align:center;"><a style="margin-left:auto;margin-right:auto;" href="https://ymmitsblog.files.wordpress.com/2014/09/474a3-2014-09-05_21-49-13.png"><img src="https://ymmitsblog.files.wordpress.com/2014/09/474a3-2014-09-05_21-49-13.png" alt="" width="640" height="244" border="0" /></a></td>
</tr>
<tr>
<td class="tr-caption" style="text-align:center;">6. Hit finish and your done!</td>
</tr>
</tbody>
</table>
<span style="text-align:-webkit-auto;">This whole process took minutes to complete and is easily repeatable. I’m using 12th gen servers here which have the newer lifecycle controllers in them which also allow rollback to the previous firmware in the event things don’t go to plan. </span>

</div>
<div style="font-family:Tahoma;orphans:2;text-align:-webkit-auto;widows:2;">If you use this and know of any other cool features thats helped you out let me know, I’ll be keen to check them out, at the moment I’ve only looked at the features that we needed straight away as a deliverable of a project.</div>
When I get it deployed and setup I'll give an overview of this integrated with Dell OpenManage and Dell Support Assist. But in the meantime you can download the vCentre Integration and try it out from <a href="http://www.dell.com/learn/us/en/555/virtualization/management-plug-in-for-vmware-vcenter?c=us&amp;l=en&amp;s=biz" target="_blank">here</a>.
<div style="font-family:Tahoma;orphans:2;text-align:-webkit-auto;widows:2;"></div>
