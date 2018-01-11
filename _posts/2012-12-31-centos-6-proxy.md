---
layout: post
title: Centos 6 安装httpd反向代理
date: 2012-12-31 22:34
author: abanger
comments: true
categories: maintenance
---
1. 系统采用默认最小安装
CentOS release 6.3

2. 安装apache服务器
<pre>
yum -y install httpd
</pre>

3.配置虚拟服务器

3.1 增加配置文件
<pre>
vi /etc/httpd/conf.d/v.conf
</pre>

3.2 配置文件内容
<pre>
&lt;virtualhost *:80&gt;
    ServerAdmin nic@qzlab.cn
    ServerName www.qzlab.cn
    &lt;location /map&gt;
        Order allow,deny
        Allow from all
        ProxyPass  http://10.10.1.150/map  retry=3
        ProxyPassReverse http://10.10.1.150/map
    &lt;/location&gt;
    &lt;location /cms&gt;
        Order allow,deny
        Allow from all
        ProxyPass  http://10.10.1.186/cms/  retry=3
        ProxyPassReverse http://10.10.1.186/cms/
    &lt;/location&gt;

    &lt;location / &gt;
        Order allow,deny
        Allow from all
        ProxyPass  http://10.10.1.186/  retry=3
        ProxyPassReverse http://10.10.1.186/
    &lt;/location&gt;
    ErrorLog logs/qztc-error_log
    CustomLog logs/qztc-access_log common
&lt;/virtualhost&gt;
</pre>

4.重启服务器
<pre>
/etc/init.d/httpd restart
</pre>
