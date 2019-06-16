---
layout: post
title: PowerCLI NUMA Report
date: 2017-09-06 13:56
author: ymmit85
comments: true
categories: [Posts, PowerCLI, PowerShell, Scripts, Tools, vSphere]
---
Seeing at the moment there is a bit of a focus around VM performance and NUMA sizing thanks to the new <a href="https://www.amazon.com/VMware-vSphere-Host-Resources-Deep/dp/1540873064" target="_blank" rel="noopener">Host Deepdive book</a> that was released recently I thought it would be good to see if there was a way to quickly audit an environment to find VMs that exceeded NUMA nodes.

PowerCLI happens to have the info we need buried within and with some some basic math we can filter out VMs that are too large for NUMA node based on vCPU, Memory or both in addition I've written the script so that it caters for heterogeneous clusters and finds the host/s with the smallest NUMA node size any compares the VMs within the cluster to it.

<img class="alignnone size-full wp-image-374" src="https://ymmitsblog.files.wordpress.com/2017/09/numa_report.png" alt="numa_report" width="1203" height="492" />

With the above example we can see that within this cluster there are some hosts with dual 8 core CPUs and some dual 10 core CPUs and they all have 128GB RAM per NUMA node. There are also two VMs that exceed the amount of CPUs per socket (both VMs have 12 vCPU with Hot Plug enabled and a single VM that exceeds a NUMA node based on RAM as it has 132GB of RAM and the NUMA node only has 128GB.

From this we can look to resize the VMs to fit within the NUMA nodes on the cluster, potentially disable CPU Hot Plug or and also after resizing create some DRS groups to ensure these VMs stay on the hosts with larger CPUs.

To use the script just connect to your vCenter server/s with PowerCLI and run the script.

<a href="https://github.com/ymmit85/vSphere/blob/master/Numa_Report_v1.ps1">Download the script.</a>

I'd also suggest checking out these videos that show in great detail the advantages of tuning your VMs correctly.

<a href="https://www.youtube.com/watch?v=EYggYAwjz3g&amp;t=3057s" target="_blank" rel="noopener">VMworld 2017 SER2724BU Extreme Performance Series: Performance Best Practices</a>
<a href="https://www.youtube.com/watch?v=5EJu2ER-aLI" target="_blank" rel="noopener">VMworld 2017 VIRT1430BU Performance Tuning and Monitoring for Virtualized Database Servers</a>
<a href="https://www.youtube.com/watch?v=sXbOoRo_Wn4" target="_blank" rel="noopener">VMworld 2017 VIRT1309BU Monster VMs (Database Virtualization) with VMware vSphere 6.5</a>
<div id="title-wrapper" class="style-scope ytd-video-renderer"></div>
<div id="title-wrapper" class="style-scope ytd-video-renderer"></div>
<div id="metadata" class="style-scope ytd-video-meta-block"></div>
