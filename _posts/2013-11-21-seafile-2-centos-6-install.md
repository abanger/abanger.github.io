---
layout: post
title: seafile 2在centos 6安装
date: 2013-11-21 13:26
author: abanger
comments: true
categories: CentOS
---
在centos6.4_64安装seafile使用apahce和SQLite。
1. 环境准备
1.1 centos6.4_64 最小化安装
<pre>
yum -y install httpd  ##部署在apache之后，参考https://github.com/haiwen/seafile/wiki/Deploy-Seafile-with-apache
yum -y install install python python-setuptools python-simplejson python-imaging sqlite3  ##系统默认是python2.6
yum -y install  apr apr-devel httpd-devel libtool  ##安装fastcgi需要用到
yum update -y
chkconfig postfix off
/etc/ini t.d/postfix stop  ##关闭邮件服务
reboot

</pre>

1.2 防火墙配置
<pre>
vi /etc/sysconfig/iptables
</pre>
/etc/sysconfig/iptables增加三条内容开放80、10001、12001端口
<pre>
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 10001 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 12001 -j ACCEPT
</pre>

防火墙重启配置生效。
<pre>
/etc/init.d/iptables restart
</pre>

1.3 安装fastcgi为httpd一个模块
seafile的apache部署使用到fastcgi，下载fastcgi，解压
<pre>
curl -O  http://www.fastcgi.com/dist/mod_fastcgi-current.tar.gz
tar -xzvf mod_fastcgi-current.tar.gz 
cd mod_fastcgi-2.4.6/
cp Makefile.AP2 Makefile
</pre>
更改Makefile内文件top_dir
<pre>
#top_dir      = /usr/local/apache2
top_dir      = /usr/lib64/httpd  ##如32位机器应是/usr/lib/httpd
</pre>
编译安装fastcgi，安装到/usr/lib64/httpd/modules/（32位机器/usr/lib/httpd/modules/）
<pre>
make
make install
</pre>
配置httpd.conf
添加FastCGIExternalServer
<pre>
LoadModule fastcgi_module modules/mod_fastcgi.so
##
FastCGIExternalServer /var/www/html/seahub.fcgi -host 127.0.0.1:8000
</pre>

添加/etc/httpd/conf.d/v.conf
<pre>
NameVirtualHost *:80
<virtualhost *:80>
  ServerName sf.qzlab.cn  ##对应dns要配置，如没有就修改自己机器的hosts
  DocumentRoot /var/www/html
  Alias /media  /opt/haiwen/seafile-server-2.0.3/seahub/media/
  RewriteEngine On
  #
  # seafile httpserver
  #
  ProxyPass /seafhttp http://127.0.0.1:8082
  ProxyPassReverse /seafhttp http://127.0.0.1:8082
  RewriteRule ^/seafhttp - [QSA,L]

  # seahub
  #
  RewriteRule ^/(media.*)$ /$1 [QSA,L,PT]
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteRule ^(.*)$ /seahub.fcgi$1 [QSA,L,E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]
</virtualhost>

</pre>




2. 安装seafile2.0
2.1 seafile下载、解压
seafile下载、解压到指定目录就行了，执行安装命令。
<pre>
http://seafile.googlecode.com/files/seafile-server_2.0.3_x86-64.tar.gz
tar -xzvf seafile-server_2.0.3_x86-64.tar.gz 
mkdir /opt/haiwen
mv seafile-server-2.0.3  /opt/haiwen
</pre>
安装seafile，只需要按指定提示填写就行了，一般默认。可参考：https://github.com/haiwen/seafile/wiki/Download-and-setup-seafile-server。

./setup-seafile.sh



2.2 配置selinux
selinux在centos6下面默认是开启的。
<pre>
setsebool -P httpd_can_network_connect 1   ##httpd可网络连接
chcon -R -h -u system_u -t httpd_sys_content_t /opt/haiwen/seafile-server-2.0.3/seahub/media/  ##httpd可访问
chcon -R -h -u system_u -t httpd_sys_content_t  /opt/haiwen/seahub-data/avatars/  ##httpd可访问
</pre>



3. 启动服务
seafile和seahub启动，httpd重启。
<pre>
/opt/haiwen/seafile-server-2.0.3/seafile.sh start
/opt/haiwen/seafile-server-2.0.3/seahub.sh start-fastcgi
/etc/init.d/httpd restart
</pre>

最后祝贺成功安装了，可直接访问http://sf.qzlab.cn
或下载客户端http://seafile.com/download/访问

4. 遇到问题
4.1 httpd出错apr_sockaddr_info_get() failed 
<pre>
Starting httpd: httpd: apr_sockaddr_info_get() failed for seafile.qzlab.cn
</pre>
在/etc/hosts添加seafile.qzlab.cn,机器名称。
<pre>
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4  seafile.qzlab.cn
</pre>

4.2  FastCGI: apr_dir_open()失败
出错信息如下
<pre>
[Wed Nov 20 10:51:13 2013] [error] (13)Permission denied: FastCGI: apr_dir_open() failed
[Wed Nov 20 10:51:13 2013] [notice] Digest: generating secret for digest authentication ...
[Wed Nov 20 10:51:13 2013] [notice] Digest: done
[Wed Nov 20 10:51:13 2013] [error] (13)Permission denied: FastCGI: apr_dir_open() failed
[Wed Nov 20 10:51:13 2013] [notice] FastCGI: process manager initialized (pid 1823)
[Wed Nov 20 10:51:13 2013] [notice] Apache/2.2.15 (Unix) DAV/2 mod_fastcgi/2.4.6 configured -- resuming normal operations
</pre>
是selinux问题，执行如下命令
<pre>
chcon -R -t var_log_t /var/log/httpd/fastcgi/
</pre>
