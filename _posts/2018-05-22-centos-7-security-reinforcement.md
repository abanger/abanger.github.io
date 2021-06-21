---
layout: post
title:  "CentOS 7 安全加固"
date:   2018-05-22 11:37:02 +0800
categories: maintenance
---

CentOS 7 安全加固  

## 1. 系统安装
最小化安装，留足磁盘空间存放日记， 配置内部yum源，安装之后马上更新。
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
##设置用户test密码过期的天数为60天，多用户系统配置。
chage -M 60 test

```
系统范围密码质量限制的配置详见/etc/security/pwquality.conf配置。
```
#设置最小密码长度：minlen=8
authconfig --passminlen=8 --update
#为允许的连续相同字符的最大数量设置2
authconfig --passmaxrepeat=2 --update
#新密码中至少需要一位数字
authconfig --enablereqdigit --update
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
#vi /etc/pam.d/sshd
#%PAM-1.0  
auth required pam_tally2.so deny=3 unlock_time=300 even_deny_root root_unlock_time=10
```
### 设置自动注销时间  
修改帐户TMOUT值，设置自动注销时间，使用命令“vi /etc/profile”修改配置文件，添加“TMOUT=”行开头的注释，设置为“TMOUT=600”，即超时时间为10分钟。
```
#vi /etc/profile
#增加
TMOUT=600
```

### 设置Bash保留历史命令的条数  
修改HISTSIZE=5,即只保留最新执行的5条命令
```
#vi /etc/profile
HISTSIZE=5
```

## 4 调整和关闭非必须的服务

### 关闭postfix服务  
如无必要,禁止该服务。  
```
systemctl disable postfix
```


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
## IP根据实际配置
firewall-cmd --reload
```


## 5 系统和网络参数调整
### 禁止内核转储  
```
##vi /etc/security/limits.conf
* soft core 0
* hard core 0

```
### 系统文件加锁  
防止未经许可的删除或添加，注意：执行权限修改后，就无法添加和删除用户。
```
chattr +ia /etc/passwd
chattr +ia /etc/shadow
chattr +ia /etc/group
chattr +ia /etc/gshadow
chattr +ia /etc/services 
```
## 6 审计调整  
### 时间同步  
保证时间准确，配置/etc/chrony.conf，注释掉无法访问服务器，添加本地时间服务器。  
```
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
##server 0.centos.pool.ntp.org iburst
##server 1.centos.pool.ntp.org iburst
##server 2.centos.pool.ntp.org iburst
##server 3.centos.pool.ntp.org iburst
server time.edu.cn iburst  ##根据实际配置

```

### 日记调整  
修改rsyslogd日志，rotate>25，日记记录超180天。  
```
/etc/logrotate.conf 
# rotate 30
```

## 修订  
- 2018-05-22 11:37:02
- 2018-05-25 16:31:02
- 2018-05-28 16:55:00
- 待续
          
## 参考  
- [CentOS 7 设置密码规则](https://blog.csdn.net/wh211212/article/details/53992772)  

