---
layout: post
title:  "Centos网络配置"
date:   2019-03-13 18:06:02 +0800
categories: maintenance
---




## Centos配置IP方法


### 查看与配置网络状态命令

```
ipconifg   
```
### 临时配置IP地址
```
ifconfig eth0 10.41.10.100 netmask 255.255.255.0  
```
### 编辑网卡配置文件
```
vi /etc/sysconfig/network-scripts/ifcfg-ens33  
```

```
BOOTPROTO=static        #采用静态IP（none、static、dhcp）
DEVICE=ens192     #网卡设备名
ONBOOT=yes       #是否随网络服务启动
IPADDR0="10.41.10.100"    #IP地址
PREFIX0="24"           #子网掩码（netmask=255.255.255.0）
GATEWAY0="10.10.10.251"   #网关
DNS1="210.34.120.1"         #DNS
```

## 连接到主机

'''
#ssh使用
'''

## 修改主机名称

### 配置文件/etc/hostname
查看文件内容
'''
less /etc/hostname
'''

### 查看状态 
'''
hostnamectl status 
'''
### 修改主机名称
'''
hostnamectl set-hostname netlab
'''

## 关闭与启动网络卡
ifdown 网卡设备名 ifup 网卡设备名
'''
ifdown ens33
ping www.baidu.com
'''

'''
ifup ens33
ping www.baidu.com

'''


## 修订  
- 2019-03-19 18:07:00
