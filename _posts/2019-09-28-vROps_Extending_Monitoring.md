---
layout: post
title: Extending Monitoring with vROps
date: 2016-09-28 22:00
author: ymmit85
categories: [vROPs, PowerShell, PowervROps, API]
---

vROps is a great tool for observing performance of a vSphere environment, investigating potential contention issues and with more recent versions capacity planning. However since vCOPs 5.x it has lacked somewhat in validating/reporting/alerting on configuration (there have been other products but not everyone has access to these), some will argue that it does, which is somewhat correct but it only accounts for some settings that are related to regulatory compliance (PCI etc) or in the vSphere Hardening Guide... What about those that want to ensure all their hosts are built correctly and want a single plane of glass for this visibility? PowerCLI can be used, however staff need to read a report or have custom integration into other toolsets, ultimately they may only run once every 24 hours therefore missing the chance to prevent an incident.

I’ve been looking to report on advanced configuration settings on hosts for sometime (eg: NetApp recommended settings, TPS state and productlocker config for VMtools). This started a bit of a trip down a rabbit hole, I started looking at reporting on Host Profiles, VUM and generally all advanced settings/properties of a host. Considering that I’ve been leveraging [PowervROps](https://github.com/ymmit85/PowervROps) lately I've been able to put together the below scripts that allow virtually (no pun intended) any data for any object to be put into vROps.

This opens up some huge possibilities with this data, we can now create Reports/Views showing this information, a Dashboard showing the amount of hosts with a configuration parameter set or which Host Profile is applied and is it compliant or better yet - are the hosts compliant with a VUM Baseline. Additionally Alerts can be configured for misconfiguration and the new compliance dashboards can feed off this data.
Hopefully this type of data can be ingested natively with vROps in the future as it would save significant effort for admins to find configuration data of their environment and quickly see if the impact of a change had an unexpected performance impact.

These scripts are all currently targeted at Hosts, but they can very easily be changed to support all other objects (I have tested this, but just wanted to get Host Reporting sorted first) keep an eye on GitHub for updates and feel free to contribute to them.

[Advanced setting Properties.](https://github.com/ymmit85/vROps/tree/master/Advanced%20Setting%20Properties)

[Host Profile compliance.](https://github.com/ymmit85/vROps/tree/master/Host%20Profile%20Properties)

[VUM Baseline Compliance.](https://github.com/ymmit85/vROps/tree/master/VUM%20Properties)

[Licence Key Reporting.](https://github.com/ymmit85/vROps/tree/master/Licence%20Key%20Properties)