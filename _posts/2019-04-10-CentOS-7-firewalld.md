---
layout: post
title:  "CentOS 7 firewalld配置方式"
date:   2019-04-10 9:42:00 +0800
categories: CentOSbook
---

## firewalld的基本使用
- 启动： systemctl start firewalld
- 关闭： systemctl stop firewalld
- 查看状态： systemctl status firewalld 
- 开机禁用  ： systemctl disable firewalld
- 开机启用  ： systemctl enable firewalld
- 重启：systemctl restart firewalld
 
 

## 配置firewalld-cmd

- 查看版本： firewall-cmd --version
- 查看帮助： firewall-cmd --help
- 显示状态： firewall-cmd --state
- 查看所有打开的端口： firewall-cmd --zone=public --list-ports
- 更新防火墙规则： firewall-cmd --reload
- 查看区域信息:  firewall-cmd --get-active-zones
- 查看指定接口所属区域： firewall-cmd --get-zone-of-interface=ens33
- 拒绝所有包：firewall-cmd --panic-on
- 取消拒绝状态： firewall-cmd --panic-off
- 查看是否拒绝： firewall-cmd --query-panic
-  
- 开启端口：firewall-cmd --zone=public --add-port=80/tcp --permanent    （--permanent永久生效，没有此参数重启后失效）
- 重新载入：firewall-cmd --reload
- 查看：firewall-cmd --zone= public --query-port=80/tcp
- 删除：firewall-cmd --zone= public --remove-port=80/tcp --permanent


## 修改配置文件
### 开放80端口
```
vi /etc/firewalld/zones/public.xml
##<service name="http"/>
##<port protocol="tcp" port="80" />
firewall-cmd --reload
```

### 设置限制ip访问服务

```
#ip 10.40.10.100/24 访问 http
vi /etc/firewalld/zones/public.xml
#命令形式
#firewall-cmd --permanent --zone=public --add-rich-rule="rule family="ipv4" source address="10.40.10.100/24" service name="http" reject"
##允许为 accept
```

查看配置文件

```
#less /etc/firewalld/zones/public.xml
<?xml version="1.0" encoding="utf-8"?>
<zone>
  <short>Public</short>
  <description>For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.</description>
  <service name="dhcpv6-client"/>
  <service name="http"/>
  <service name="https"/>
  <service name="ssh"/>
  <rule family="ipv4">
    <source address="10.40.10.100/24"/>
    <service name="http"/>
    <reject/>
  </rule>
</zone>
```

删除配置文件相关内容
```
#命令形式
#firewall-cmd --permanent  --zone=public --remove-rich-rule="rule family="ipv4" source address="192.168.122.0/24" service name="http" reject"
```


