---
layout: post
title: centos网络配置
date: 2012-03-17 14:57
author: abanger
comments: true
categories: CentOS
---
/etc/sysconfig/network 包括主机基本网络信息，用于系统启动

/etc/sysconfig/network-script/ 此目录下是系统启动最初始化网络的信息

/etc/sysconfig/network-script/ifcfg-eth0 网络配置信息

/etc/xinetd.conf 定义了由超级进程XINETD启动的网络服务

/etc/protocols 设定了主机使用的协议以及各个协议的协议号

/etc/services 设定了主机的不同端口的网络服务

/etc/sysconfig/iptables 防火墙配置信息
防火墙配置命令
关闭
/etc/rc.d/init.d/iptables stop

开启
/etc/rc.d/init.d/iptables start

查看当前配置：iptables -L

将网卡禁用
ifconfig eth0 down
将网卡启用
ifconfig eth0 up
修改Ip地址
方法一
ifconfig eth0 1.2.3.4 netmask 255.0.0.0 up
重新启动网络
service network restart
方法二：纯修改配置文件 修改配置
cd /etc/sysconfig/network-scripts
vi ifcfg-eth0 #拿我机子举例，我只有一块网卡，就是eth0
==============================

<!--more-->

DEVICE=eth0　(描述网卡对应的设备别名，例如ifcfg-eth0的文件中它为eth0)
BOOTPROTO=static　(设置网卡获得ip地址的方式，可能的选项为static，dhcp或bootp，分别对应静态指定的ip地址，通过dhcp协议获得的ip地址，通过bootp协议获得的ip地址)
BROADCAST=192.168.0.255　(对应的子网广播地址,设置的局域网广播地址,可以不写这行)
HWADDR=00:07:E9:05:E8:B4　 (对应的网卡物理地址)
IPADDR=12.168.1.2　(如果设置网卡获得ip地址的方式为静态指定，此字段就指定了网卡对应的ip地址)
IPV6INIT=no　 (开启或关闭IPv6；关闭no，开启yes)
IPV6_AUTOCONF=no　 (开启或关闭IPv6自动配置；关闭no，开启yes)
NETMASK=255.255.255.0　(网卡对应的网络掩码)
NETWORK=192.168.1.0　(网卡对应的网络地址)
ONBOOT=yes　(系统启动时是否设置此网络接口，设置为yes时，系统启动时激活此设备)
TYPE=Ethernet #这是类型，当然也可以不写这一行。

=======================

&nbsp;

修改对应网卡的网关的配置文件
# vi /etc/sysconfig/network
NETWORKING=yes　(表示系统是否使用网络，一般设置为yes。如果设为no，则不能使用网络，而且很多系统服务程序将无法启动)
HOSTNAME=centos　(设置本机的主机名，这里设置的主机名要和/etc/hosts中设置的主机名对应)
GATEWAY=192.168.1.1　(设置本机连接的网关的IP地址。例如，网关为10.0.0.2)

DNS修改
编辑文件vi /etc/resolv.conf
/* 使用route 命令配置路由表 */

//添加到主机路由

# route add –host 10.10.10.10 dev eth0:1

# route add –host 10.10.10.118 gw 192.168.168.1

//添加到网络的路由

# route add –net IP netmask MASK eth0

# route add –net IP netmask MASK gw IP

# route add –net IP/24 eth1

//添加默认网关

# route add default gw IP

//删除路由

# route del –host 10.10.10.10 dev eth0:1
/* 常用命令 */

# traceroute www.baidu.com

# ping www.baidu.com

//显示网络接口状态信息

# netstat –I

//显示所有监控的服务器的Socket和正在使用Socket的程序信息

# netstat –lpe

//显示内核路由表信息

# netstat –r

# netstat –nr

//显示TCP/UDP传输协议的连接状态

# netstat –t

# netstat –u

//更改主机名

# hostname myhost

//查看ARP缓存

# arp

//添加

# arp –s IP MAC

//删除

# arp –d IP

/* 运行级别与网络服务 */

//查看当前运行级别

# runlevel

//运行级别的切换

# init

# telinit
