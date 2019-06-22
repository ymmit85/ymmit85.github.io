---
layout: post
title: Bulk Reports via vROps API
date: 2019-06-22
author: ymmit85
categories: [vROps, API, PowerShell]
---

One of the issues within vROps is the ability to run a report against multiple objects at a time, this could be a performance report against VMs, capacity reports for Datastores or configuration reports for Hosts. Running the report one at a time does become pretty cumbersome after about 2 minutes, however the API does come to the rescue here.

If you not familiar with API log in via https://vROpsHost>/suite-api and have a look around, it's pretty well documented and most functions in the UI can be accessed here.
There are a ton of ways to use the API, but given I use PowerShell most for ad-hoc scripts I've been adding API functions into the [PowervROps Module](https://github.com/ymmit85/PowervROps), some of these allow you to work with the reports, request them for an object then download the report.

[This script](https://github.com/ymmit85/vROps/tree/master/Reports) is quite simple, you can request a report for a single object or multiple via a parameter input, or provide a CSV list of objects to run reports against. Just import the [PowervROps Module](https://github.com/ymmit85/PowervROps) first then run the script. Check out the README for both scripts for instructions and feel free to make any changes to the [PowervROps Module](https://github.com/ymmit85/PowervROps) or the [report script](https://github.com/ymmit85/vROps/tree/master/Reports.

Over the next few weeks I'll publish some more scripts that I use day to day.