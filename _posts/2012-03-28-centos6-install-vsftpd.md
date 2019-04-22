---
layout: post
title: centos6安装vsftpd
date: 2012-03-28 11:21
author: abanger
comments: true
categories: CentOS
---
## 1. 检查是否已经安装了ftp服务器软件vsftp
<pre>rpm -qa | grep ftp</pre>
或
<pre>yum list | grep ftp</pre>
## 2. 安装ftp服务器
```
yum install vsftpd #ftp服务器必装软件 yum remove vsftpd #删除
/etc/init.d/vsftpd start #启动DNS服务器
/etc/init.d/vsftpd restart #重启DNS服务器
chkconfig vsftpd on #设为开机启动 chkconfig vsftpd off设为开机不启动
yum install vsftpd
/etc/init.d/vsftpd start
/etc/init.d/vsftpd restart
chkconfig vsftpd on
```
FTP现可以正常运行



## 3. 匿名用户使用ftp配置
匿名用户使用ftp这个系统用户,无需密码

权限：
默认/var/ftp/pub，这个目录的权限应该是770，owner是root，group是ftp
```
chmod 770 /var/ftp/pub
chown root:ftp /var/ftp/pub
```
如系统开启sulinux
```
setsebool -P allow_ftpd_full_access 1
```
### 1) 匿名服务器的连接（独立的服务器）
功能：只能连接FTP服务器，不能上传和下载
在/etc/vsftpd/vsftpd.conf配置文件中添加如下几项：
```
anonymous_enable=yes (允许匿名登陆)
dirmessage_enable=yes （切换目录时，显示目录下.message的内容）
local_umask=022 (FTP上本地的文件权限，默认是077)
connect_from_port_20=YES （启用FTP数据端口的数据连接）*
xferlog_file=/var/log/vsftpd.log （日志记录）
xferlog_enable=yes （激活上传和下载的日志）
xferlog_std_format=yes (使用标准的日志格式)
Ftpd_banner=Welcome my FTP server （欢迎信息）
pam_service_name=vsftpd （验证方式）*
listen=yes （独立的VSFTPD服务器）*
```
注：其中所有和日志欢迎信息相关连的都是可选项,打了星号的无论什么帐户都要添加，是属于FTP的基本选项
```
anonymous_enable=yes
dirmessage_enable=yes
local_umask=022
connect_from_port_20=YES
xferlog_file=/var/log/vsftpd.log
xferlog_enable=yes
xferlog_std_format=yes
ftpd_banner=Welcome my FTP server
pam_service_name=vsftpd
listen=yes
```
### 2) 开启匿名FTP服务器上传权限
在配置文件中添加以下的信息即可：
```
Anon_upload_enable=yes (开放上传权限)
Anon_mkdir_write_enable=yes （可创建目录的同时可以在此目录中上传文件）
Write_enable=yes (开放本地用户写的权限)
Anon_other_write_enable=yes (匿名帐号可以有删除的权限)
<pre>anon_upload_enable=yes
anon_mkdir_write_enable=yes
write_enable=yes
anon_other_write_enable=yes
```
### 3) 安全选项
```
idle_session_timeout=600(秒) （用户会话空闲后10分钟）
data_connection_timeout=120（秒） （将数据连接空闲2分钟断）
accept_timeout=60（秒） （将客户端空闲1分钟后断）
Connect_timeout=60（秒） （中断1分钟后又重新连接）
local_max_rate=200000（bite） （本地用户传输率200K）
anon_max_rate=100000（bite） （匿名用户传输率100K）
pasv_min_port=50000 （将客户端的数据连接端口改在
pasv_max_port=60000 50000—60000之间）
max_clients=200 （FTP的最大连接数）
max_per_ip=4 （每IP的最大连接数）
<pre>idle_session_timeout=600
data_connection_timeout=120
accept_timeout=60
connect_timeout=60
local_max_rate=200000
anon_max_rate=100000
pasv_min_port=50000
pasv_max_port=60000
max_clients=200
max_per_ip=4
```
## 4. 配置本地组访问的FTP

暂略

## 5、维护
查看谁登陆了FTP,并杀死它的进程
```
ps –xf |grep ftp
```
kill 进程号

查看日记
```
tail -f /var/log/vsftpd.log
```