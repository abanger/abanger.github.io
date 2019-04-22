# CentOS 7 vsftpd 安装


## 检查是否已安装vsftp
```
rpm -qa | grep ftp
#yum list | grep ftp
```
## 安装ftp服务器

```
yum install vsftpd -y  #安装 

##yum remove vsftpd #删除
```
## vsftpd的配置信息和目录

安装vsftpd的话，以下是一些文件的默认位置：
```
/usr/sbin/vsftpd —- VSFTPD的主程序
/etc/systemd/system/multi-user.target.wants/vsftpd.service  —- 启动脚本
/etc/vsftpd/vsftpd.conf —- 主配置文件
/etc/pam.d/vsftpd —- PAM认证文件
/etc/vsftpd/ftpusers —- 禁止使用VSFTPD的用户列表文件
/etc/vsftpd/user_list —- 禁止或允许使用VSFTPD的用户列表文件
/var/ftp —- 匿名用户主目录
/var/ftp/pub —- 匿名用户的下载目录
```

- systemctl enable vsftpd
Created symlink from /etc/systemd/system/multi-user.target.wants/vsftpd.service to /usr/lib/systemd/system/vsftpd.service.




## 默认配置

### 设置anonymous用户可以登录

```
#vi /etc/vsftpd/vsftpd.conf
anonymous_enable=YES
local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_std_format=YES
listen=NO
listen_ipv6=YES

pam_service_name=vsftpd
userlist_enable=YES
tcp_wrappers=YES

```


### 配置firewalld防火墙开放21和45000-49000端口

```
firewall-cmd --permanent --add-port=21/tcp
firewall-cmd --permanent --add-port=45000-49000/tcp
firewall-cmd --reload
```

### 测试登录
```
ftp 10.41.10.110
```
登录日志
```
ftp 10.41.10.110
连接到 10.41.10.110
220 (vsFTPd 3.0.2)
200 Always in UTF8 mode.
用户(10.41.10.110:(none)): anonymous
331 Please specify the password.
密码:
230 Login successful.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
ftp.conf
226 Directory send OK.
ftp: 收到 13 字节，用时 0.00秒 13.00千字节/秒。
ftp>
```
浏览器访问：ftp://10.41.10.110/




## 更改默认下载目录
在配置文件尾部添加如下内容
```
local_root=/home/vsftpd
chroot_local_user=YES
anon_root=/home/vsftpd
```
local_root表示使用本地用户登录到ftp时的默认目录
anon_root表示匿名用户登录到ftp时的默认目录


```
anon_upload_enable=YES
```



## 配置认证用户登录
（选修）





## 备注
### 配置selinux允许FTP服务
没有selinux相关命令的话，需要安装policycoreutils-python包
yum -y install policycoreutils-python.x86_64
setsebool -P ftpd_full_access=on
setsebool ftpd_anon_write 1
getsebool -a|grep ftp

### ftp目录建议 
最好不要设置默认目录为/，使用建议使用mount –bind来挂载需要的目录


### 修改配置文件里的注释行

mv /etc/vsftpd/vsftpd.conf /etc/vsftpd/vsftpd.conf.bak
grep -v "#" /etc/vsftpd/vsftpd.conf.bak > /etc/vsftpd/vsftpd.conf



### 添加用户
```
useradd -m -d /home/test -s/bin/sh -g root test
passwd test 123456
```

useradd [-u uid  | -g group] | -d dir | -s shell | -c comment | -m [-k skel_dir] ] login
``` 
说明：  
u 指定用户ID号  
g 制定所在组  
d 指定用户目录  
s 制定用户SHELL  
c 用户的注释  
m 建立用户目录  
login 用户的登录名 
```

### 防火墙配置参考
```
#/etc/firewalld/zones/public.xml
<?xml version="1.0" encoding="utf-8"?>
<zone>
  <short>Public</short>
  <description>For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.</description>
  <service name="dhcpv6-client"/>
  <service name="http"/>
  <service name="https"/>
  <service name="ssh"/>
  <port protocol="tcp" port="21"/>
  <port protocol="tcp" port="45000-49000"/>
</zone>
```