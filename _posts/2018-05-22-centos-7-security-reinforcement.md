---
layout: post
title:  "Centos 7 安全加固"
date:   2018-05-22 11:37:02 +0800
categories: maintenance
---

Centos 7 安全加固

## 1. 系统安装
最小化安装，安装之后马上更新。
```
yum update -y
```

## 2 系统信息
查看内核信息	uname -a  
查看所有软件包	rpm -qa  
查看主机名	hostname  
查看网络配置	ifconfig -a  
查看路由表	netstat -rn  
查看开放端口	netstat -an  
查看当前进程	ps -aux  


## 3 用户帐号和环境  
### 禁用无用账号  
清除了operator、lp、shutdown、halt、games 帐号；删除的用户组有：lp、uucp、games、dip；其它系统伪帐号均处于锁定SHELL登录的状态。如果不需要登录系统，shell应该/sbin/nologin。
```
userdel lp
userdel operator
userdel shutdown
userdel halt
userdel games
```
### 添加密码策略  
加强密码的复杂度等，降低被猜解的可能性。查看"/etc/login.defs"和“/etc/pam.d/system-auth”查看密码策略设置。
```
##如设置用户test密码过期的天数为60天
chage -M 60 test

```
系统范围密码质量限制的配置详见/etc/security/pwquality.conf配置。
```
#如设置最小密码长度：minlen=8
authconfig --passminlen=8 --update
```



### 检查特殊账号
验证是否有账号存在空口令的情况:
```
awk -F:  '($2 =="") {print $1 }' /etc/shadow
```
检查除了root以外是否还有其它账号的UID为0:
```
#任何UID为0的账号在系统上都具有超级用户权限.
awk -F:  '($3 ==0) {print $1 }' /etc/passwd
```

### 限制用户远程登录  
在#%PAM-1.0的下面，即第二行，添加内容，一定要写在前面，如果写在后面，虽然用户被锁定，但是只要用户输入正确的密码，还是可以登录的！
```
vim /etc/pam.d/sshd
```
```
#%PAM-1.0  
auth required pam_tally2.so deny=3 unlock_time=300 even_deny_root root_unlock_time=10
```
### 设置自动注销时间  
修改帐户TMOUT值，设置自动注销时间
```
vi /etc/profile
```
```
#增加
TMOUT=600
```
使用命令“vi /etc/profile”修改配置文件，添加“TMOUT=”行开头的注释，设置为“TMOUT=600”，即超时时间为10分钟


### 设置Bash保留历史命令的条数  
修改HISTSIZE=5,即只保留最新执行的5条命令
```
vi /etc/profile
```

## 4 调整和关闭非必须的服务

### 关闭Mail   Server 
```
systemctl disable postfix
```
如无必要,禁止该服务。

### 调整SSH服务
调整配置文件：/etc/ssh/sshd_config
```
#修改SSH端口
Port 6622
#日志级别LogLevel设置为INFO或者DEBUG
LogLevel INFO
```
对应调整防火墙和SElinux策略  
```
yum -y install policycoreutils-python
semanage port -a -t ssh_port_t -p tcp 6622

```

### 调整防火墙策略
限制SSH服务访问IP
```
firewall-cmd --permanent --zone=public --remove-service=ssh

firewall-cmd --permanent --zone=public --add-rich-rule="rule family="ipv4"  source address="10.0.10.0/22" service name="ssh" accept"

firewall-cmd --reload
```


## 5 系统和网络参数调整
禁止core   dump:
```
vi /etc/security/limits.conf

* soft core 0

* hard core 0

```
给系文件加锁,防止未经许可的删除或添加  
```
chattr +ia /etc/passwd
chattr +ia /etc/shadow
chattr +ia /etc/group
chattr +ia /etc/gshadow
chattr +ia /etc/services 
```
注意：执行上述权限修改后，就无法添加和删除用户。  



## 6 日记调整
修改rsyslogd日志，rotate>25，日记记录超180天。
```
/etc/logrotate.conf 
```
rotate 30

## 待续

## 修订  
第一版本：2018-05-22 11:37:02

##参考  
- [CentOS 7 设置密码规则](https://blog.csdn.net/wh211212/article/details/53992772)  