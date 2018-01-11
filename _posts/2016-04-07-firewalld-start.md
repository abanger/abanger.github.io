---
layout: post
title: firewalld入门
date: 2016-04-07 09:25
author: abanger
comments: true
categories: CentOS
---
<strong>1. firewalld简介</strong>

在CentOS7里有几种防火墙共存：firewalld、iptables、ebtables，默认使用firewalld来管理netfilter子系统，不过底层调用的命令仍然是iptables等。firewalld、iptables、ebtables这三个daemon是有冲突，不能同时启用。

firewalld相比iptables变化是：
第一个是配置文件。firewalld 的配置文件被放置在不同的 xml 文件当中，这使得对规则的维护变得更加容易和可读，有条理。相比于 iptables 的规则配置文件而言，这显然可以算作是一个进步。
第二个是区域模型。firewalld 通过对 iptables 自定义链的使用，抽象出一个区域模型的概念，将原本十分灵活的自定义链统一成一套默认的标准使用规范和流程，使得防火墙在易用性和通用性上得到提升。
另特性是对 ebtables 的支持，通过统一的接口来实现 ipt/ebt 的统一管理；支持是富语言（rich rule）。

<!--more-->

概念ZONE
数据包要进入到内核必须要通过这些zone中的一个，而不同的zone里定义的规则不一样（即信任度不一样，过滤的强度也不一样）。可以根据网卡所连接的网络的安全性来判断，这张网卡的流量到底使用哪个zone。
firewalld将网卡对应到不同的区域（zone），zone 默认共有9个，block dmz drop external home internal public trusted work ，不同的区域之间的差异是其对待数据包的默认行为不同，根据区域名字我们可以很直观的知道该区域的特征，在CentOS7系统中，默认区域被设置为public。

使用下面的命令分别列出所有支持的 zone 和查看当前的默认 zone：
<pre>#firewall-cmd --get-zones
block dmz drop external home internal public trusted work
</pre>
<pre>#firewall-cmd --get-default-zone
public
</pre>
所有可用 zone 的 xml 配置文件被保存在 /usr/lib/firewalld/zones/ 目录，该目录中的配置为默认配置，不允许管理员手工修改，自定义 zone 配置需保存到 /etc/firewalld/zones/ 目录。防火墙规则即是通过 zone 配置文件进行组织管理。

概念service
在 /usr/lib/firewalld/services/ 目录中，还保存了另外一类配置文件，每个文件对应一项具体的网络服务，如 ssh 服务等，与之对应的配置文件中记录了各项服务所使用的 tcp/udp 端口，在最新版本的 firewalld 中默认已经定义了 70+ 种服务，当默认提供的服务不够用或者需要自定义某项服务的端口时，需要将 service 配置文件放置在 /etc/firewalld/services/ 目录中。
相比iptables，service 配置的好处显而易见，第一，通过服务名字来管理规则更加人性化，第二，通过服务来组织端口分组的模式更加高效，如果一个服务使用了若干个网络端口，则服务的配置文件就相当于提供了到这些端口的规则管理的批量操作快捷方式。每加载一项 service 配置就意味着开放了对应的端口访问，使用下面的命令分别列出所有支持的 service 和查看当前 zone 种加载的 service：
<pre>#firewall-cmd --get-services

RH-Satellite-6 amanda-client bacula bacula-client dhcp dhcpv6 dhcpv6-client dns freeipa-ldap freeipa-ldaps
freeipa-replication ftp high-availability http https imaps ipp ipp-client ipsec iscsi-target kerberos 
kpasswd ldap ldaps libvirt libvirt-tls mdns mountd ms-wbt mysql nfs ntp openvpn pmcd pmproxy pmwebapi 
pmwebapis pop3s postgresql proxy-dhcp radius rpc-bind rsyncd samba samba-client smtp ssh telnet tftp 
tftp-client transmission-client vdsm vnc-server wbem-https
</pre>
<pre>#firewall-cmd --list-services

dhcpv6-client ssh
</pre>
&nbsp;

2.firewalld配置

在不改变状态的条件下重新加载防火墙：
<pre>firewall-cmd --reload</pre>
获取支持的区域列表
<pre>firewall-cmd --get-zones</pre>
获取所有支持的服务
<pre>firewall-cmd --get-services</pre>
列出全部启用的区域的特性
<pre>firewall-cmd --list-all-zones</pre>
启用区域中的一种服务，默认区域为public
firewall-cmd [--zone=] --add-service= [--timeout=]
此举启用区域中的一种服务。如果未指定区域，将使用默认区域。如果设定了超时时间，服务将只启用特定秒数。如果服务已经活跃，将不会有任何警告信息。

eg启用默认区域中的http服务:
<pre>firewall-cmd --add-service=http</pre>
禁用区域中的某种服务
firewall-cmd [--zone=] --remove-service=
此举禁用区域中的某种服务。如果未指定区域，将使用默认区域。
eg禁止 home 区域中的 http 服务:
<pre>firewall-cmd --zone=home --remove-service=http</pre>
区域种的服务将被禁用。如果服务没有启用，将不会有任何警告信息。
查询区域中是否启用了特定服务
firewall-cmd [--zone=] --query-service=

启用区域端口和协议组合
firewall-cmd [--zone=] --add-port=[-]/ [--timeout=]
此举将启用端口和协议的组合。端口可以是一个单独的端口 或者是一个端口范围 - 。协议可以是 tcp 或 udp。

禁用端口和协议组合
firewall-cmd [--zone=] --remove-port=[-]/ 查询区域中是否启用了端口和协议组合
firewall-cmd [--zone=] --query-port=[-]/ ##firewall-cmd --query-port=80/tcp
yes
如果启用，此命令将有返回值。没有输出信息。

永久选项不直接影响运行时的状态。这些选项仅在重载或者重启服务时可用。为了使用运行时和永久设置，需要分别设置两者。 选项 --permanent 需要是永久设置的第一个参数。

某些特殊的服务我们并不想开放给所有人访问，只需要开放给特定的IP地址即可，例如 SSH 服务，我们将使用 firewalld 的富语言风格配置指令：
<pre>#firewall-cmd --add-rich-rule 'rule family="ipv4" source address="10.10.10.0/24" service name="ssh" accept' --permanent
</pre>
#删除默认ssh
<pre>#firewall-cmd --zone=public --remove-service=ssh</pre>
输出区域 全部启用的特性。如果生略区域，将显示默认区域的信息。
<pre>firewall-cmd [--zone=] –list-all</pre>
# 获取活动的区域
<pre>firewall-cmd --get-active-zones
public
interfaces: eno16777984
</pre>
#删除默认ssh
<pre>#firewall-cmd --zone=public --remove-service=ssh
#firewall-cmd --zone=public --remove-service=ssh --permanent
</pre>
<pre>firewall-cmd --zone=public --list-all
public (default, active)
interfaces: eno16777984
sources:
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports:
icmp-blocks:
rich rules:
rule family="ipv4" source address="10.10.10.0/24" service name="ssh" accept
</pre>
参考：
firewalld：https://fedoraproject.org/wiki/FirewallD/zh-cn
https://access.redhat.com/documentation/zh-CN/Red_Hat_Enterprise_Linux/7/html/Security_Guide/sec-Using_Firewalls.html
http://www.ibm.com/developerworks/cn/linux/1507_caojh/
