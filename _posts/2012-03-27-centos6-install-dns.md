---
layout: post
title: centos6安装配置DNS服务器
date: 2012-03-27 15:43
author: abanger
comments: true
categories: CentOS
---
1、检查是否已经安装了DNS服务器软件bind
<pre>rpm -qa | grep bind</pre>
2、安装dns服务器
yum -y install bind* #dns服务器必装软件 yum remove bind* #删除
/etc/init.d/named start #启动DNS服务器
/etc/init.d/named restart #重启DNS服务器
service named stop #停止服务器
chkconfig named on #设为开机启动
<pre>yum -y install bind*
/etc/init.d/named start
/etc/init.d/named restart
service named stop
chkconfig named on</pre>
<!--more-->

3、配置DNS服务器

1) 配置全局设置
<pre>vi /etc/named.conf</pre>
#修改以下代码
listen-on port 53 { any; };
allow-query { any; };
<pre>//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//

options {
/// listen-on port 53 { 127.0.0.1; };
/// listen-on-v6 port 53 { ::1; };
listen-on port 53 { any; };
directory "/var/named";
dump-file "/var/named/data/cache_dump.db";
statistics-file "/var/named/data/named_stats.txt";
memstatistics-file "/var/named/data/named_mem_stats.txt";
/// allow-query { localhost; };
allow-query { any; };
recursion yes;

forwarders { 8.8.8.8; };

dnssec-enable yes;
dnssec-validation yes;
dnssec-lookaside auto;

/* Path to ISC DLV key */
bindkeys-file "/etc/named.iscdlv.key";
};

logging {
channel default_debug {
file "data/named.run";
severity dynamic;
};
};

zone "." IN {
type hint;
file "named.ca";
};

include "/etc/named.rfc1912.zones";</pre>
全局配置（options ）
options 语句在每个配置文件中只有一个。如果出现多个options, 则第一个options 的配置有效，并会产生一个警告信息。
directory "/var/named";
zone文件的存放目录，这里的/var/named 是相对目录，在chroot环境下/var/named目录下。
allow-query { any; };
任何地址都解析
forwarders { 8.8.8.8; };可以多个用;分开
自己不解析的都扔到这dns

2)添加正向解析区域及反向解析区域
区域配置（zone ）
zone 语句作用是定义DNS 区域，在此语句中可定义DNS 区域选项
zone区域设置，第一步，设置根区域当DNS服务器处理递归查询时，如果本地区域文件不能进行查询的解析，就会转到根DNS服务器查询，所以在主配置文件named.conf文件中还要定义根区域。
type:设置域的类型
file:设置根服务列表文件名
zone "." IN {
type hint;
file "named.ca";
};
“.” 意思的根区域
IN 是internet记录
type是类型 根的类型是hint
file是根区域文件
/etc/named.ca记录13组根域名服务器
<pre>vi /etc/named.rfc1912.zones</pre>
#添加以下代码

zone "qzulab.cn" IN {
type master;
file "qzulab.cn.zone";
allow-update { none; };
};

zone "1.168.192.in-addr.arpa" IN {
type master;
file "192.168.1.zone";
allow-update { none; };
};
<pre>// named.rfc1912.zones:
//
// Provided by Red Hat caching-nameserver package
//
// ISC BIND named zone configuration for zones recommended by
// RFC 1912 section 4.1 : localhost TLDs and address zones
// and http://www.ietf.org/internet-drafts/draft-ietf-dnsop-default-local-zones-02.txt
// (c)2007 R W Franks
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//

zone "localhost.localdomain" IN {
type master;
file "named.localhost";
allow-update { none; };
};

zone "localhost" IN {
type master;
file "named.localhost";
allow-update { none; };
};

//zone "1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.ip6.arpa" IN {
// type master;
// file "named.loopback";
// allow-update { none; };
//};

zone "1.0.0.127.in-addr.arpa" IN {
type master;
file "named.loopback";
allow-update { none; };
};

zone "0.in-addr.arpa" IN {
type master;
file "named.empty";
allow-update { none; };
};

zone "qzulab.cn" IN {
type master;
file "qzulab.cn.zone";
allow-update { none; };
};

zone "1.168.192.in-addr.arpa" IN {
type master;
file "192.168.1.zone";
allow-update { none; };
};</pre>
3)新增正向区域文件
#编辑正向区域文件
<pre>cp /var/named/named.localhost /var/named/chroot/var/named/qzulab.cn.zone
vi /var/named/chroot/var/namedqzulab.cn.zone</pre>
#添加以下代码
$TTL 1D
@ IN SOA dns.qzulab.cn. root.qzulab.cn. (
20120312 ; serial # 仅作为序列号而已
3H ; refresh # 服务器的更新时间
1H ; retry # 重新更新时间间隔
1W ; expire # 多久之后宣布失败
0) ; minimum # 相当于缓存记忆时间
@ IN NS dns.qzulab.cn.
@ IN MX 10 mail.qzulab.cn.
dns IN A 192.168.1.12
mail IN A 192.168.1.13
www IN A 192.168.1.15
ftp IN CNAME www
<pre>$TTL 1D
@ IN SOA dns.qzulab.cn. root (
   20120312 ; serial
   1D ; refresh
   1H ; retry
   1W ; expire
   3H ) ; minimum
@ IN NS dns.qzulab.cn.
@ IN MX 10 mail.qzulab.cn.
dns IN A 192.168.1.12
mail IN A 192.168.1.13
www IN A 192.168.1.15
ftp IN CNAME www

chown -R named.named qzulab.cn.zone</pre>
TTL是生存期,单位是秒
$TTL是全局定义的
第二行 SOA记录,@取代在/etc/named.conf中指定的域名。
SOA段中的数字，分别为：序列号、刷新、重试、过期、生存期
序列号：序列号用于DNS数据库文件的版本控制。每当数据被改变，这个序列号就应该被增加。
刷新：从服务器向主服务器查询最新数据的间隔周期。每一次检查时从服务器的数据是否需要更改，则根据序列号来判别。
重试：一旦从服务器尝试连接主服务器失败，下一次查询主服务器的延迟时间。
过期：如果从服务器无法连通主服务器，则在经过此时间后，宣告其数据过期。
生存期：服务器回答 ‘无此域名’ 的间隔时间。
数字的默认单位为秒。否则：W= 周、D= 日、H= 小时、M= 分钟。
IN 是internet记录
SOA 是SOA 初始化记录,我们说的是初始授权记录,这个翻译不过，理解很多反正知道是那个意思就行了
dns.qzulab.cn. 是DNS服务器的名称
root 是管理员的邮箱地址

4)新增反向区域文件
#编辑反向区域文件
<pre>cp /var/named/named.localhost /var/named/chroot/var/named/192.168.1.zone
vi /var/named/chroot/var/named/192.168.1.zone #

#添加以下代码
$TTL 1D
@ IN SOA dns.qzulab.cn. root.qzulab.cn. (
2011091200 ; serial
3H ; refresh
1H ; retry
1W ; expire
0) ; minimum
@ IN NS dns.qzulab.cn.
12 IN PTR dns.qzulab.cn.
13 IN PTR mail.qzulab.cn.
15 IN PTR www.qzulab.cn.

$TTL 1D
@ IN SOA dns.qzulab.cn. root.qzulab.cn. (
     20120310 ; serial
     3H ; refresh
     1H ; retry
     1W ; expire
     0) ; minimum
@ IN NS dns.qzulab.cn.
12 IN PTR dns.qzulab.cn.
13 IN PTR mail.qzulab.cn.
15 IN PTR www.qzulab.cn.

chown -R named.named 192.168.1.zone</pre>
5)生成rndc.conf配置文件
<pre>vim random</pre>
输入以下代码，保存
asdlfkjsdfdsfsadlkjowersdfclDSADSFaos
<pre>rndc-confgen -r random &gt; /etc/rndc.key
rndc-confgen -a -c /etc/rndc.key</pre>
#拷贝至/etc/named.conf文件中
<pre>key "rndc-key" {
algorithm hmac-md5;
secret "C1ujGLCguS21IZBPoND/Gg==";
};

controls {
inet 127.0.0.1 port 953
allow { 127.0.0.1; } keys { "rndc-key"; };
};

//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//

options {
///     listen-on port 53 { 127.0.0.1; };
///     listen-on-v6 port 53 { ::1; };
        listen-on port 53 { any; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
///     allow-query     { localhost; };
        allow-query     { any; };
        recursion yes;

        forwarders {210.34.120.1;8.8.8.8; };

        dnssec-enable yes;
        dnssec-validation yes;
        dnssec-lookaside auto;

        /* Path to ISC DLV key */
        bindkeys-file "/etc/named.iscdlv.key";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

zone "." IN {
        type hint;
        file "named.ca";
};

 key "rndc-key" {
       algorithm hmac-md5;
       secret "C1ujGLCguS21IZBPoND/Gg==";
 };

 controls {
       inet 127.0.0.1 port 953
               allow { 127.0.0.1; } keys { "rndc-key"; };
};

include "/etc/named.rfc1912.zones";</pre>
4、测试DNS服务器

vi /etc/resolv.conf #编辑文件
nameserver 127.0.0.1 #将linux服务器的DNS设置为本身
nameserver 8.8.8.8 #为了能够访问外网需要设置备用DNS
nslookup -type=soa qzulab.cn #测试起始授权机构SOA资源记录
nslookup -type=a www.qzulab.cn #测试主机地址A记录资源
nslookup -type=ptr 192.168.1.129 #测试反向解析指针PTR资源记录
nslookup -type=cname ftp.qzulab.cn #测试别名CNAME资源记录
nslookup -type=ns qzulab.cn #测试名称服务器NS资源记录
nslookup -type=mx qzulab.cn #测试邮件交换器MX资源记录

tail -f /var/log/messages #查看日志
named-checkzone /var/named/qzulab.cn.zone #检查正向区域配置文件
named-checkzone 192.168.1.zone /var/named/192.168.1.zone #检查反向区域配置文件cd /var/named/chroot/etc

5、可能出错及解决
1、could not configure root hints from 'named.ca': permission denied
chown -R named.named ./* 将/var/named路径下的所有文件改成named用户named组的权限

2、lab named[16425]: none:0: open: /etc/rndc.key: file not found
#没有random产生器，手动伪造一个文件代替/dev/random的功能
#新建一个 random 文件随即输入一串数字“记得要长~~
shell $&gt; vim random
asdSDSDFSDAFSasdkfjalsjdflajsldfHLKLLKOI
rndc-confgen -r random &gt; rndc.key
参考:http://www.nginxs.com/linux/43.html
