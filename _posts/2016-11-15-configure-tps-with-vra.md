---
layout: post
title: Configure TPS with vRA & vRO
date: 2016-11-15 22:41
author: ymmit85
comments: true
categories: [Posts, vRA, vRO, vSphere]
---
One topic that seems to come up occasionally since it was disabled by default (<a href="https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&amp;cmd=displayKC&amp;externalId=2097593" target="_blank">KB2097593</a>) is what people are doing with TPS. It seems to becoming common for companies to want to sweat their assets more now so making use of features to maximize the resources within an environment makes sense. But there are cases where it&nbsp;may not be desirable to be enabled for a specific VMs, or just enable certain ones in a certain "zone" to be able to share.

Since getting more hands on with vRA/vRO over the past few months I've been looking quite a lot at the "Resource Actions", these are great for those repeatable tasks you may perform as part of your 'Day 2' operations on a VM so why not make a action here to allow a user to select the type of page sharing for their workload..?

<img class="alignnone size-full wp-image-194" src="https://ymmitsblog.files.wordpress.com/2016/11/tps1.jpg" alt="tps1" width="205" height="274"><img class="alignnone size-full wp-image-221" src="https://ymmitsblog.files.wordpress.com/2016/11/tps3.jpg" alt="tps3" width="331" height="142">

So you can see here you can have multiple "Zones" and once selected the vRO workflow will run, apply the VM advanced setting "sched.mem.pshare.salt" (with custom salt values) or in this scenario if "Isolated" is selected the&nbsp;"sched.mem.pshare.salt" value will be the VM UUID (if per the <a href="https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&amp;cmd=displayKC&amp;externalId=2097593" target="_blank">KB article</a> you have your host TPS Salting settings are set to "2" then you wont need to worry about this).

<img class="alignnone size-full wp-image-195" src="https://ymmitsblog.files.wordpress.com/2016/11/tps2.jpg" alt="tps2" width="874" height="105">

The workflow is pretty straight forward, (1) it determines the salt value based on the "Zone" selected and obtains the VM UUID and the advanced setting is applied to the VM, (2) vCenter task monitored, (3) the host of the VM and Cluster is obtained, (4) a random host in the cluster is calculated and lastly (5) the VM is migrated to another host for the new TPS setting to take effect (I haven't had &nbsp;chance to test this, so this step might need to be changed to a full power-cycle, it mentions to power off in the <a href="https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&amp;cmd=displayKC&amp;externalId=2097593" target="_blank">KB </a>but I think this is just so the advanced setting can be applied).

<a href="https://github.com/ymmit85/vRO" target="_blank">Download workflow.</a>

I borrowed some vRO scripts from these sites to make the above workflow work. In addition the main script in the workflow was based off a script William Lam had on Flowgrab that hardened VMs.

<a href="https://virtualdatacave.com/2015/07/get-vm-hostsystem-and-resourcepool-orchestrator-workflow/" target="_blank">“Get VM HostSystem and ResourcePool” Orchestrator Workflow</a>

<a href="https://github.com/get-vm/vRO_Actions/blob/master/randomHostInCluster.action" target="_blank">randomHostInCluster</a>&nbsp;

&nbsp;
