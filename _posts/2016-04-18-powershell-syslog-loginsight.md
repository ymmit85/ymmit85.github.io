---
layout: post
title: PowerShell, Syslog & LogInsight
date: 2016-04-18 22:55
author: ymmit85
comments: true
categories: [LogInsight, Posts, PowerCLI, PowerShell, Tools, vSphere]
---
As part of a planning process to deploy LogInsight and looking at different use cases, we thought it might be good to be able to send log data from PowerShell scripts to LogInsight, this could be purely info messages or detailed errors that inline with the rest of the data visible in LogInsight would make troubleshooting that bit easier.

I found <a href="https://www.sgc-univ.net/powershell-udp-client-and-syslog-messages/" target="_blank">this</a> PowerShell script that sends Syslog messages to a destination and have modified it slightly so the message category is in caps and the source of the message can be specified (eg: this can be the name of the script or the scheduled task name).
<pre class="brush:powershell">Function SyslogSender ()
{
&lt;#
.SYNOPSIS
UDP client creation to send message to a syslog server.
This script was sourced from https://www.sgc-univ.net/powershell-udp-client-and-syslog-messages/.


.DESCRIPTION
Basic usage:
$Obj = ./SyslogSender 192.168.2.4
$Obj.Send("string message1")
$Obj.Send("string message2")
$Obj.Send($message)

This uses the following defaults:
- Source : SyslogSender
- Facility : user
- Severity : info
- Timestamp : now
- Computername : name of the computer on which the script is executed.
- Syslog Port: 514

Advanced usage:
$Obj = ./SyslogSender 192.168.231.3 432
This defines a custom port when setting up the object

$Obj.Send("String Message", "String Facility", "String Severity", "String Timestamp", "String Hostname")
This sends a message with a custom facility, severity, timestamp and hostname.
i.e. $obj.Send("Script Error", "local7", "alert", $(Get-Date), $env:COMPUTERNAME)

$Obj.Send("String Message" , "PowerShell Script.ps1", "alert", "user")
This sends a message with the Source being defined in the message as "PowerShell Script.ps1", the message is also an "alert".
#&gt;

Param
(
[String]$Destination = $(throw "ERROR: SYSLOG Host Required..."),
[Int32]$Port = 514
)

$ObjSyslogSender = New-Object PsObject
$ObjSyslogSender.PsObject.TypeNames.Insert(0, "SyslogSender")

# Initialize the udp 'connection'
$ObjSyslogSender | Add-Member -MemberType NoteProperty -Name UDPClient -Value $(New-Object System.Net.Sockets.UdpClient)
$ObjSyslogSender.UDPClient.Connect($Destination, $Port)

# Add the Send method:
$ObjSyslogSender | Add-Member -MemberType ScriptMethod -Name Send -Value {

Param
(
[String]$Data = $(throw "Error SyslogSender: No data to send!"),
[String]$Source = "SyslogSender",
[String]$Severity = "info",
[String]$Facility = "user",
[String]$Timestamp = $(Get-Date),
[String]$Hostname = $env:COMPUTERNAME
)

# Maps used to translate string to corresponding decimal value
$FacilityMap = @{
"kern" = 0;"user" = 1;"mail" = 2;"daemon" = 3;"security" = 4;"auth" = 4;"syslog" = 5;
"lpr" = 6;"news" = 7;"uucp" = 8;"cron" = 9;"authpriv" = 10;"ftp" = 11;"ntp" = 12;
"logaudit" = 13;"logalert" = 14;"clock" = 15;"local0" = 16;"local1" = 17;"local2" = 18;
"local3" = 19;"local4" = 20;"local5" = 21;"local6" = 21;"local7" = 23;
}
$SeverityMap = @{
"emerg" = 0;"panic" = 0;"alert" = 1;"crit" = 2;"error" = 3;"err" = 3;"warning" = 4;
"warn" = 4;"notice" = 5;"info" = 6;"debug" = 7;
}
# Map facility, default to user
$FacilityDec = 1
if ($FacilityMap.ContainsKey($Facility))
{
$FacilityDec = $FacilityMap[$Facility]
}

# Map severity, default to info
$SeverityDec = 6
if ($SeverityMap.ContainsKey($Severity))
{
$SeverityDec = $SeverityMap[$Severity]
}

# Calculate PRI code
$PRI = ($FacilityDec * 8) + $SeverityDec

#Format message content to include severity
$Content = "$($Severity.Substring(0).ToUpper()) $data"
#write-host "$content"

#Build message content
$Message = "&lt;$PRI&gt; $Timestamp $Hostname $Content - $Source"
#write-host $Message

#Format the data, recommended is a maximum length of 1kb
$Message = $([System.Text.Encoding]::ASCII).GetBytes($message)

#write-host $Message
if ($Message.Length -gt 1024)
{
$Message = $Message.Substring(0, 1024)
}
# Send the message
$this.UDPClient.Send($Message, $Message.Length) | Out-Null

}
$ObjSyslogSender
}

</pre>
Once you have this function loaded up in you script/library you can simply send a message to LogInsight as follows:

<img class="alignnone size-full wp-image-153" src="https://ymmitsblog.files.wordpress.com/2016/04/screen-shot-2016-04-18-at-10-26-32-pm.png" alt="Screen Shot 2016-04-18 at 10.26.32 PM" width="651" height="96" />

<img class="alignnone size-full wp-image-152" src="https://ymmitsblog.files.wordpress.com/2016/04/screen-shot-2016-04-18-at-10-26-05-pm.png" alt="Screen Shot 2016-04-18 at 10.26.05 PM" width="1091" height="451" />

We can then change the message so the name of the script is shown and we have increased the category to alert.

<img class="alignnone size-full wp-image-155" src="https://ymmitsblog.files.wordpress.com/2016/04/screen-shot-2016-04-18-at-10-28-07-pm.png" alt="Screen Shot 2016-04-18 at 10.28.07 PM" width="657" height="98" />

<img class="alignnone size-full wp-image-154" src="https://ymmitsblog.files.wordpress.com/2016/04/screen-shot-2016-04-18-at-10-27-55-pm.png" alt="Screen Shot 2016-04-18 at 10.27.55 PM" width="1091" height="485" />

So from here you can create queries/alerts based on criteria in the messages being received. Also I did also find <a href="http://blog.igics.com/2016/04/esxi-host-vcpupcpu-reporting-via.html" target="_blank">this post by David Pasek</a> showing how you can also send messages into LogInsight via the REST API. However there may be the odd situation where your source may not have access to the API (eg: in DMZ) and only syslog data is allowed in.

&nbsp;

&nbsp;

&nbsp;
