---
layout: post
title: rsync迁移zimbra
date: 2013-01-08 16:13
author: abanger
comments: true
categories: zimbra
---
    本次迁移是从物理机迁移到虚拟机，主要环境大概一致，从64位的Centos6.2迁移到Centos6.3，迁移过程没有遇到操作系统的问题。
当然物理机迁移到虚拟机有一种办法是用VMware的converter直接整体物理机迁移也是可行的；另外一迁移方法见另文（http://aba.qzu.cn/archives/247.html）；本次迁移要主采用rsync，直接覆盖的方法，新旧两个系统安装同样zimbra版本，然后数据直接rsync同步。

1、环境

新系统Centos6.3 64 最小化安装，简称为OSnew
<pre>
less /etc/redhat-release 
</pre>
CentOS release 6.3 (Final)
<pre>
uname -a
</pre>
Linux zcs.qzalab.cn 2.6.32-279.19.1.el6.x86_64 #1 SMP Wed Dec 19 07:05:20 UTC 2012 x86_64 x86_64 x86_64 GNU/Linux

旧系统Centos6.2 64 最小化安装，简称为OSold
<pre>
less /etc/redhat-release 
</pre>
CentOS release 6.2 (Final)
<pre>
uname -a
</pre>
Linux zcs.qzalab.cn 2.6.32-220.17.1.el6.x86_64 #1 SMP Wed May 16 00:01:37 BST 2012 x86_64 x86_64 x86_64 GNU/Linux


2、迁移过程
新旧两个系统安装同样zimbra版本。
2.1 OSold原来安装8.0.0，升级为8.0.2，升级过程比较简单，略过。
2.2 OSnew新安装，直接安装8.0.2, 过程详见：http://aba.qzu.cn/archives/518.html，
2.3 停止新旧系统服务，新旧都做备份，防止出错恢复，只备份/opt/zimbra目录就行。
2.4 处理ldap大文件问题，详细请参阅：https://wiki.zimbra.com/wiki/OpenLDAP_Performance_Tuning_8.0
OSold利用mdb_copy备份data.mdb文件，移除data.mdb，同步后在OSnew利用mdb_copy恢复。
<pre>
mkdir /opt/zimbra/data/ldap/mdb/db
mdb_copy /opt/zimbra/backup/ldap/db /opt/zimbra/data/ldap/mdb/db 
mv /opt/zimbra/data/ldap/mdb/db/data.mdb  /opt
</pre>
2.5 OSold配置rsync服务（rsync配置请参考有关资料），同步/opt/zimbra目录到OSnew。
在OSnew运行rsync同步。
<pre>
 rsync -azHpr   --delete zimbra@10.10.10.27::zcshome /opt/zimbra/ --password-file=/etc/rsyncd/rsyncd.secrets
</pre>

3、OSnew检查/opt/zimbra目录权限
<pre>
/opt/zimbra/libexec/zmfixperms
</pre>


4、祝贺您完成工作
<pre>
su - zimbra
zmcontrol start
</pre>
可以咖啡了！

参考：
<a href="http://aba.qzu.cn/archives/247.html">zimbra迁移</a>
<a href="http://aba.qzu.cn/archives/518.html" title="Centos6.3安装zimbra 8.0.2">Centos6.3安装zimbra 8.0.2</a>
<a href="https://wiki.zimbra.com/wiki/OpenLDAP_Performance_Tuning_8.0" title="OpenLDAP Performance Tuning 8.0">OpenLDAP Performance Tuning 8.0</a>
