---
layout: post
title: svn一例错误处理
date: 2012-12-22 12:36
author: abanger
comments: true
categories: maintenance
---
1、出错提示
 
Could not open the requested SVN filesystem

环境:centos 6

2、问题日记
<pre>
[Sat Dec 22 12:24:03 2012] [error] [client 10.10.10.133] (20014)Internal error: Berkeley DB error for filesystem '/svnroot/nic/db' while opening environment:\n
[Sat Dec 22 12:24:03 2012] [error] [client 10.10.10.133] Could not fetch resource information.  [500, #0]
[Sat Dec 22 12:24:03 2012] [error] [client 10.10.10.133] Could not open the requested SVN filesystem  [500, #160029]
[Sat Dec 22 12:24:03 2012] [error] [client 10.10.10.133] Could not open the requested SVN filesystem  [500, #160029]
[Sat Dec 22 12:24:03 2012] [error] [client 10.10.10.133] File does not exist: /var/www/html/favicon.ico
[Sat Dec 22 12:24:07 2012] [error] [client 10.10.10.133] (20014)Internal error: Berkeley DB error for filesystem '/svnroot/nic/db' while opening environment:\n
[Sat Dec 22 12:24:07 2012] [error] [client 10.10.10.133] Could not fetch resource information.  [500, #0]
[Sat Dec 22 12:24:07 2012] [error] [client 10.10.10.133] Could not open the requested SVN filesystem  [500, #160029]
[Sat Dec 22 12:24:07 2012] [error] [client 10.10.10.133] Could not open the requested SVN filesystem  [500, #160029]
</pre>


3、将问题repository备份，然后恢复
关闭apache,然后用以下命令恢复
svnadmin recover <path to your repository>

<pre>
[root@wwwsvn svnroot]# svnadmin recover /svnroot/nic
Repository lock acquired.
Please wait; recovering the repository may take some time...

Recovery completed.
The latest repos revision is 123.
</pre>

4、恢复权限
<pre>
chown -R apache.apache /svnroot/nic/*
</pre>

问题解决，资料库内容还在。</path>
