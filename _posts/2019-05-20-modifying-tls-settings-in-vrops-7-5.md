---
layout: post
title: Modifying TLS settings in vROps 7.5
date: 2019-05-20 23:02
author: ymmit85
comments: true
categories: [Posts, Scripts, vROps]
---

If you've recently upgraded vROps to 7.5 you may wonder post upgrade why your vCenter 5.5 Adapters no longer work (and you see the below error message when testing Adapters....) or if you wrap up REST API calls with PowerShell (like done with <a href="https://github.com/ymmit85/PowervROps" target="_blank" rel="noopener">PowervROps</a>) and are no longer able to connect in some cases then this might be helpful...

<img class="  wp-image-395 aligncenter" src="https://ymmitsblog.files.wordpress.com/2019/05/vrops_connection_test_err.png" alt="vrops_connection_test_err" width="375" height="180" />

First check the following log for something similar to the entry below when you test your Adapters.

Administration > Support > Logs > {node} > Collector > collector.log

``
[46859] 2019-05-20 23:03:10,356 ERROR [Collector worker thread 11]  com.vmware.vcops.vlsi.VimClientFactory.createVimClientImpl - Exception occurred while creating VIM client.
 com.vmware.vim.vmomi.client.exception.SslException: javax.net.ssl.SSLHandshakeException: Server chose TLSv1, but that protocol version is not enabled or not supported by the client.
``

<a href="https://kb.vmware.com/s/article/67108" target="_blank" rel="noopener">KB67108</a> will most likely help you (also the <a href="https://docs.vmware.com/en/vRealize-Operations-Manager/7.5/rn/vRealize-Operations-Manager-75.html" target="_blank" rel="noopener">Release Notes</a> do also point to this..), but this process is a little cumbersome so here is a set of commands you can run on <span style="text-decoration:underline;">all</span> nodes in your vROps cluster to resolve this issue.

### Part 1
#### Backup vcops-apache.conf.bak
``
cp $VCOPS_BASE/../vmware-vcopssuite/utilities/conf/vcops-apache.conf $VCOPS_BASE/../vmware-vcopssuite/utilities/conf/vcops-apache.conf.bak
``

#### View current config
``
grep SSLProtocol $VCOPS_BASE/../vmware-vcopssuite/utilities/conf/vcops-apache.conf
``

#### Update config
``
sed -i '/SSLProtocol All -SSLv2 -SSLv3 -TLSv1 -TLSv1.1/c\SSLProtocol All -SSLv2 -SSLv3' $VCOPS_BASE/../vmware-vcopssuite/utilities/conf/vcops-apache.conf
``
#### View new config
``
grep SSLProtocol $VCOPS_BASE/../vmware-vcopssuite/utilities/conf/vcops-apache.conf
``

### Part 2
#### Backup java.security.bak
``
cp $VMWARE_JAVA_HOME/lib/security/java.security $VMWARE_JAVA_HOME/lib/security/java.security.bak
``

#### View current config
``
grep 'EC keySize &lt; 224,' $VMWARE_JAVA_HOME/lib/security/java.security
``

#### Update config
``
sed -i '/ EC keySize &lt; 224, TLSv1, TLSv1.1, DES40_CBC, RC4_40, 3DES_EDE_CBC/c\ EC keySize &lt; 224, DES40_CBC, RC4_40, 3DES_EDE_CBC' $VMWARE_JAVA_HOME/lib/security/java.security
``

# View new config
``
grep 'EC keySize &lt; 224,' $VMWARE_JAVA_HOME/lib/security/java.security

cp $VCOPS_BASE/../vmware-vcopssuite/utilities/conf/vcops-apache.conf $VCOPS_BASE/../vmware-vcopssuite/utilities/conf/vcops-apache.conf.bak

grep SSLProtocol $VCOPS_BASE/../vmware-vcopssuite/utilities/conf/vcops-apache.conf

sed -i '/SSLProtocol All -SSLv2 -SSLv3 -TLSv1 -TLSv1.1/c\SSLProtocol All -SSLv2 -SSLv3' $VCOPS_BASE/../vmware-vcopssuite/utilities/conf/vcops-apache.conf

grep SSLProtocol $VCOPS_BASE/../vmware-vcopssuite/utilities/conf/vcops-apache.conf

cp $VMWARE_JAVA_HOME/lib/security/java.security $VMWARE_JAVA_HOME/lib/security/java.security.bak

grep 'EC keySize &lt; 224,' $VMWARE_JAVA_HOME/lib/security/java.security

sed -i '/ EC keySize &lt; 224, TLSv1, TLSv1.1, DES40_CBC, RC4_40, 3DES_EDE_CBC/c\ EC keySize &lt; 224, DES40_CBC, RC4_40, 3DES_EDE_CBC' $VMWARE_JAVA_HOME/lib/security/java.security

grep 'EC keySize &lt; 224,' $VMWARE_JAVA_HOME/lib/security/java.security
``

Once done and the cluster nodes have restarted then as per the <a href="https://kb.vmware.com/s/article/67108" target="_blank" rel="noopener">KB </a>run the following to test that everything is working (or just test the Adapter from the UI).

``
$VCOPS_BASE/../vmware-vcopssuite/openssl/bin/openssl s_client -connect node-FQDN-or-IP-address:443 -tls1
$VCOPS_BASE/../vmware-vcopssuite/openssl/bin/openssl s_client -connect node-FQDN-or-IP-address:443 -tls1_1
``
