---
layout: post
title: CentOS 7下的 systemd
date: 2014-07-17 18:02
author: abanger
comments: true
categories: CentOS
---
安装下centos 7 ，发现已不能在/etc/init.d/配置服务了，网上学学一下。CentOS 7使用systemd替换了现在常用的System V与BSD风格init程序。

systemd采用了以下新技术：
<ul>
	<li>采用Socket激活式与总线激活式服务，以提高相互依赖的各服务的并行运行性能；</li>
	<li>用cgroups代替PID来追踪进程，以此即使是两次fork之后生成的守护进程也不会脱离systemd的控制。</li>
</ul>
systemd服务管理
<pre>systemctl is-enabled .service #查询服务是否开机启动
systemctl enable .service #开机运行服务
systemctl disable .service #取消开机运行
systemctl start .service #启动服务
systemctl stop .service #停止服务
systemctl restart .service #重启服务
systemctl reload .service #重新加载服务配置文件
systemctl status .service #查询服务运行状态
systemctl --failed #显示启动失败的服务

</pre>
<!--more-->

&nbsp;
<pre>
[root@lab ~]# systemctl status httpd.service
httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled)
   Active: active (running) since Sat 2014-07-12 15:30:07 CST; 5 days ago
  Process: 3229 ExecStop=/bin/kill -WINCH ${MAINPID} (code=exited, status=0/SUCCESS)
  Process: 3868 ExecReload=/usr/sbin/httpd $OPTIONS -k graceful (code=exited, status=0/SUCCESS)
 Main PID: 3234 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           +-3234 /usr/sbin/httpd -DFOREGROUND
           +-3874 /usr/sbin/httpd -DFOREGROUND
           +-3875 /usr/sbin/httpd -DFOREGROUND
           +-3876 /usr/sbin/httpd -DFOREGROUND
           +-3877 /usr/sbin/httpd -DFOREGROUND
           +-3878 /usr/sbin/httpd -DFOREGROUND
           +-8338 /usr/sbin/httpd -DFOREGROUND
           +-8339 /usr/sbin/httpd -DFOREGROUND
           +-8340 /usr/sbin/httpd -DFOREGROUND

Jul 12 15:30:07 localhost.localdomain httpd[3234]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using lo... message
Jul 12 15:30:07 localhost.localdomain systemd[1]: Started The Apache HTTP Server.
Jul 13 03:40:01 lab.qzlab.cn systemd[1]: Reloading The Apache HTTP Server.
Jul 13 03:40:02 lab.qzlab.cn httpd[3868]: AH00557: httpd: apr_sockaddr_info_get() failed for lab.qzlab.cn
Jul 13 03:40:02 lab.qzlab.cn httpd[3868]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 127.0.0....s message
Jul 13 03:40:02 lab.qzlab.cn systemd[1]: Reloaded The Apache HTTP Server.
Hint: Some lines were ellipsized, use -l to show in full.
</pre>
&nbsp;

参考：<a href="https://wiki.archlinux.org/index.php/Systemd">https://wiki.archlinux.org/index.php/Systemd</a>

<a href="http://www.freedesktop.org/wiki/Software/systemd/">http://www.freedesktop.org/wiki/Software/systemd/</a>
