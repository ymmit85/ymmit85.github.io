---
layout: post
title: Design considerations for EVC mode
date: 2014-09-01 21:56
author: ymmit85
comments: true
categories: [Posts, Uncategorized]
---
<span style="font-family:Arial, Helvetica, sans-serif;"> So the other day over our last day at VMworld 2014, the topic of EVC (Enhanced vMotion Compatibility) popped up with the following questions; </span>
<div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;">When should it be enabled?</span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;">What level should it be set to?</span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;">And finally what is the 'Best Practice'..?</span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;">I won’t go into to much detail about EVC, there are tons of blogs/whitepapers out there about it. But at the end of the day EVC will allow vMotion operations to complete across hosts with different CPU generations (but not across manufacturers).</div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;">It does this by masking features of the CPU equal to the EVC level selected in your vSphere cluster e.g; Intel Nehalem or Sandy Bridge. Recently <a href="http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&amp;cmd=displayKC&amp;externalId=1005764">updated FAQ from VMware here</a>. </span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;">So, why would you enable it? Firstly it comes back to the requirements of your environment and what hardware or operational procedures your constrained by. Here’s some examples;</span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;">Company X requires additional compute capacity to be added to it’s current vSphere cluster while keeping existing hosts until they are out of vendor support. Existing hosts have Intel Sandy Bridge CPUs installed while the new hosts have Intel Ivy Bridge CPUs. No applications within the environment require specific CPU features.</span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;">Assuming that EVC is currently disabled, it will first need to be enabled on the vSphere cluster. But remember, by just enabling it won’t immediately put it into effect - every VM within the cluster needs to be power cycled (power off &gt; power on). </span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;">Once this is done your good to go - new hosts can be added to the cluster VMs can vMotion to them without issue. However this process of power cycling the VMs could have been mitigated if when the cluster was first created EVC was set to the appropriate level.</span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;">Moving forward a year the older hosts have reached end of life, they can be placed into maintenance and removed from the cluster. </span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;">As we no longer have these hosts with Sandy Bridge CPUs installed we can increase the EVC level exposing the features of the Ivy Bridge hosts once the VMs have again been power cycled.</span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;">Another example is based on a project that I’ve currently been working on.</span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;">The business recently purchased some new Dell M620s with Ivy Bridge CPUs, they will be added to a cluster with hosts Sandy Bridge CPUs, but there is one small requirement that I was given while designing the environment - in the event of multiple hardware failures we are allowed to remove hosts from our “Test / Dev” environment and add them to “Production” to restore services, the thing is these hosts have Nehalem CPUs.</span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;">For this to work I need to set the EVC mode of this cluster to Nehalem.</span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;">This will ensure in the rare case we need to add one of these hosts to the cluster VMs can be migrated across without issue.</span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;">Then there is the 'Best Practice'... well that depends, there isn't anything saying that it is best to enable it, because you need to understand your workloads, or you may not be planning on adding hosts with a different generation CPUs. But it is widely recommended to enable it to the level of CPU that may get added to your cluster just to make life easier down the track and to avoid potentially significant outages due to a rather trivial task. </span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;">At the end of the day EVC is great for making your environment easier to manage. The only thing I would make sure of is if you need to have different family CPUs in your cluster try have them at the same speed. If all of a sudden your VMs moves from an older host running at 2.6Ghz to one running at 1.6Ghz you may start getting phone calls at 2am asking why whatever application or process is running slow - I don’t know about you but I’m not a fan of that…</span></div>
<div style="orphans:2;text-align:-webkit-auto;widows:2;"><span style="font-family:Arial, Helvetica, sans-serif;"> </span></div>
</div>
