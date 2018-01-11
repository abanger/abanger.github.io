---
layout: post
title: centos6安装apache Web服务器
date: 2012-03-26 10:08
author: abanger
comments: true
categories: CentOS
---
1、安装Apache
<pre>yum install httpd</pre>
Apache的配置文件在/etc/httpd/conf/httpd.conf，一般默认配置就正常启动
如果要往加配置最好加在/etc/httpd/conf.d/这个文件夹里，命名为XXX.conf就可以，apache启动的时候会自动加载这个文件夹里所有的.conf文件。
Apache默认网站根目录 /var/www/html
启动apache:
<pre>/etc/init.d/httpd start
/sbin/chkconfig httpd on</pre>
安装完成，把网站内容放入网站根目录 /var/www/html，用浏览器即可访问。

2、配置虚拟目录

添加域名，以www.qzulab.cn和域名doc.qzulab.cn为例。习惯把网站目录放在/opt下，vi /etc/httpd/conf.d/v.conf，加入：
<pre>NameVirtualHost *:80
&lt;VirtualHost *:80&gt;
ServerAdmin abanger#qztc.edu.cn
ServerName www.qzulab.cn
ServerAlias www.qzulab.cn
DocumentRoot /opt/qzulab
ErrorLog logs/qzulab_error.log
CustomLog logs/qzulab_access.log combined
&lt;/VirtualHost&gt;

&lt;VirtualHost *:80&gt;
ServerAdmin abanger#qztc.edu.cn
ServerName doc.qzulab.cn
ServerAlias doc.qzulab.cn
DocumentRoot /opt/qzudoc
ErrorLog logs/qzulab_doc.error.log
CustomLog logs/qzulab_doc.access.log combined
&lt;/VirtualHost&gt;</pre>
启动apache前需要按照上面的目录结构建立目录：
<pre>mkdir -p /opt/qzulab
mkdir -p /opt/qzudoc</pre>
以后在v.con中添加域名后都需要重启apache:

/etc/init.d/httpd restart

<!--more-->

3、可能的问题
<pre>Starting httpd: httpd: apr_sockaddr_info_get() failed for lab
httpd: Could not reliably determine the server's fully qualified domain name, using 127.0.0.1 for ServerName</pre>
修改httpd.conf中的ServerName IP:port与Listen IP:端口一致就行了
即找到#ServerName www.example.com:80 把#去掉，换为www.qzulab.cn:80，再重启apache即可。

4、相关图示
<a href="http://aba.qzu.cn/wp-content/uploads/2012/03/httpd050.jpg"><img class="alignnone size-full wp-image-149" title="httpd050" src="http://aba.qzu.cn/wp-content/uploads/2012/03/httpd050.jpg" alt="" width="974" height="89" /></a>
<a href="http://aba.qzu.cn/wp-content/uploads/2012/03/httpd020.jpg"><img class="alignnone size-full wp-image-151" title="httpd020" src="http://aba.qzu.cn/wp-content/uploads/2012/03/httpd020.jpg" alt="" width="1257" height="493" /></a>
<a href="http://aba.qzu.cn/wp-content/uploads/2012/03/httpd030.jpg"><img class="alignnone size-full wp-image-152" title="httpd030" src="http://aba.qzu.cn/wp-content/uploads/2012/03/httpd030.jpg" alt="" width="299" height="50" /></a>
<a href="http://aba.qzu.cn/wp-content/uploads/2012/03/httpd040.jpg"><img class="alignnone size-full wp-image-153" title="httpd040" src="http://aba.qzu.cn/wp-content/uploads/2012/03/httpd040.jpg" alt="" width="727" height="121" /></a>
<a href="http://aba.qzu.cn/wp-content/uploads/2012/03/httpd050.jpg"><img class="alignnone size-full wp-image-149" title="httpd050" src="http://aba.qzu.cn/wp-content/uploads/2012/03/httpd050.jpg" alt="" width="974" height="89" /></a>


<script type="text/javascript"><!--
google_ad_client = "ca-pub-5620859237754405";
/* aba */
google_ad_slot = "0153981594";
google_ad_width = 300;
google_ad_height = 250;
//-->
</script>
<script type="text/javascript"
src="http://pagead2.googlesyndication.com/pagead/show_ads.js">
</script>
