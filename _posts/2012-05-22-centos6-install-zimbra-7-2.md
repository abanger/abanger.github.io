---
layout: post
title: Centos6.2下安装zimbra 7.2
date: 2012-05-22 10:43
author: abanger
comments: true
categories: zimbra
tags: CentOS, install, linux, zimbra
---
Centos6.2安装过程另文，最小化安装。
DNS配置请参考有关文档
[root@zcs /]# less /etc/redhat-release
CentOS release 6.2 (Final)
[root@zcs /]# uname -a
Linux zcs.qzu.cn 2.6.32-220.17.1.el6.x86_64 #1 SMP Wed May 16 00:01:37 BST 2012 x86_64 x86_64 x86_64 GNU/Linux

<!--more-->

1、安装环境准备

a.防火墙设置
<pre>vi /etc/sysconfig/iptables

# enable zimbra ports
-A INPUT -m state --state NEW -m tcp -p tcp --dport 25 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 110 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 389 -j ACCEPT -s 10.10.10.0/24
-A INPUT -m state --state NEW -m tcp -p tcp --dport 443 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 465 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 993 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 995 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 5222 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 7071 -j ACCEPT -s 10.10.10.0/24
-A INPUT -m state --state NEW -m tcp -p tcp --dport 873 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 7110 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 7780 -j ACCEPT</pre>
b.主机名配置
[root@zcs /]# less /etc/hosts
# Do not remove the following line, or various programs
# that require network functionality will fail.
127.0.0.1 localhost.localdomain localhost
10.10.10.20 zcs.qzulab.cn zcs

c.安装依赖
<pre>yum  -y install libidn11 curl fetchmail libpcre3 libgmp3c2 libxml2 libstdc++6 openssl perl sysstat libtool-ltdl compat-libstdc* nc  file</pre>
d.关闭系统默认安装postfix
<pre>chkconfig postfix off
/etc/init.d/postfix stop</pre>
e.关闭selinux

SELINUX=disabled
<pre>vi /etc/selinux/config</pre>
2、安装
<pre>tar -xzvf zcs-7.2.0_GA_2669.RHEL6_64.20120410002025.tgz</pre>
[root@zcs home]# tar -xzvf zcs-7.2.0_GA_2669.RHEL6_64.20120410002025.tgz
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/packages/
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/packages/zimbra-apache-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/packages/zimbra-core-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/packages/zimbra-ldap-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/packages/zimbra-logger-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/packages/zimbra-memcached-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/packages/zimbra-mta-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/packages/zimbra-proxy-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/packages/zimbra-snmp-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/packages/zimbra-spell-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/packages/zimbra-store-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/bin/
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/bin/get_plat_tag.sh
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/bin/zmdbintegrityreport
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/data/
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/data/versions-init.sql
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/YPL.txt
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/zcl.txt
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/zimbra_public_eula_2.1.txt
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/en_US/
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/en_US/admin.pdf
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/en_US/Fedora Server Config.pdf
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/en_US/Import_Wizard_Outlook.pdf
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/en_US/MigrationWizard_Domino.pdf
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/en_US/MigrationWizard.pdf
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/en_US/OSmultiserverinstall.pdf
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/en_US/quick_start.pdf
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/en_US/RNZCSO_2005Beta.pdf
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/en_US/User Instructions for ZCS Import Wizard.pdf
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/en_US/Zimbra iCalendar Migration Guide.pdf
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/en_US/Zimbra_Release_Note.pdf
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/en_US/Zimbra Schema.pdf
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/docs/en_US/ZWC User Guide_7_0.pdf
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/util/
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/util/modules/
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/util/modules/getconfig.sh
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/util/modules/packages.sh
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/util/modules/postinstall.sh
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/util/addUser.sh
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/util/globals.sh
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/util/utilfunc.sh
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/readme_source_en_US.txt
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/readme_binary_en_US.txt
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/install.sh
zcs-7.2.0_GA_2669.RHEL6_64.20120410002025/README.txt
<pre>./install.sh --platform-override</pre>
[root@zcs zcs-7.2.0_GA_2669.RHEL6_64.20120410002025]# ./install.sh --platform-override

Operations logged to /tmp/install.log.1356
Checking for existing installation...
zimbra-ldap...NOT FOUND
zimbra-logger...NOT FOUND
zimbra-mta...NOT FOUND
zimbra-snmp...NOT FOUND
zimbra-store...NOT FOUND
zimbra-apache...NOT FOUND
zimbra-spell...NOT FOUND
zimbra-convertd...NOT FOUND
zimbra-memcached...NOT FOUND
zimbra-proxy...NOT FOUND
zimbra-archiving...NOT FOUND
zimbra-cluster...NOT FOUND
zimbra-core...NOT FOUND

PLEASE READ THIS AGREEMENT CAREFULLY BEFORE USING THE SOFTWARE.
ZIMBRA, INC. ("ZIMBRA") WILL ONLY LICENSE THIS SOFTWARE TO YOU IF YOU
FIRST ACCEPT THE TERMS OF THIS AGREEMENT. BY DOWNLOADING OR INSTALLING
THE SOFTWARE, OR USING THE PRODUCT, YOU ARE CONSENTING TO BE BOUND BY
THIS AGREEMENT. IF YOU DO NOT AGREE TO ALL OF THE TERMS OF THIS
AGREEMENT, THEN DO NOT DOWNLOAD, INSTALL OR USE THE PRODUCT.

License Terms for the Zimbra Collaboration Suite:
http://www.zimbra.com/license/zimbra_public_eula_2.1.html

Do you agree with the terms of the software license agreement? [N] y

Checking for prerequisites...
FOUND: NPTL
FOUND: nc-1.84-22
FOUND: sudo-1.7.4p5-9
FOUND: libidn-1.18-2
FOUND: gmp-4.3.1-7
FOUND: /usr/lib64/libstdc++.so.6
Checking for suggested prerequisites...
FOUND: perl-5.10.1
FOUND: sysstat
FOUND: sqlite
Prerequisite check complete.

Checking for installable packages

Found zimbra-core
Found zimbra-ldap
Found zimbra-logger
Found zimbra-mta
Found zimbra-snmp
Found zimbra-store
Found zimbra-apache
Found zimbra-spell
Found zimbra-memcached
Found zimbra-proxy

Select the packages to install

Install zimbra-ldap [Y]

Install zimbra-logger [Y]

Install zimbra-mta [Y]

Install zimbra-snmp [Y]

Install zimbra-store [Y]

Install zimbra-apache [Y]

Install zimbra-spell [Y]

Install zimbra-memcached [N]

Install zimbra-proxy [N]
Checking required space for zimbra-core
checking space for zimbra-store

Installing:
zimbra-core
zimbra-ldap
zimbra-logger
zimbra-mta
zimbra-snmp
zimbra-store
zimbra-apache
zimbra-spell

You appear to be installing packages on a platform different
than the platform for which they were built.

This platform is CentOS6_64
Packages found: RHEL6_64
This may or may not work.

Using packages for a platform in which they were not designed for
may result in an installation that is NOT usable. Your support
options may be limited if you choose to continue.

Install anyway? [N] y

The system will be modified. Continue? [N] y

Removing /opt/zimbra
Removing zimbra crontab entry...done.
Cleaning up zimbra init scripts...done.
Cleaning up /etc/ld.so.conf...done.
Cleaning up /etc/security/limits.conf...done.

Finished removing Zimbra Collaboration Suite.

Installing packages

zimbra-core......zimbra-core-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm...done
zimbra-ldap......zimbra-ldap-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm...done
zimbra-logger......zimbra-logger-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm...done
zimbra-mta......zimbra-mta-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm...done
zimbra-snmp......zimbra-snmp-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm...done
zimbra-store......zimbra-store-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm...done
zimbra-apache......zimbra-apache-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm...done
zimbra-spell......zimbra-spell-7.2.0_GA_2669.RHEL6_64-20120410002025.x86_64.rpm...done
Operations logged to /tmp/zmsetup.05212012-152613.log
Installing LDAP configuration database...done.
Setting defaults...
Interface: 210.34.120.30
Interface: 127.0.0.1

DNS ERROR - none of the MX records for zcs.qzulab.cn
resolve to this host
Change domain name? [Yes]
Create domain: [zcs.qzulab.cn] qzulab.cn
MX: zcs.qzulab.cn (210.34.120.30)
MX: mail.qzulab.cn (210.34.120.30)

Interface: 210.34.120.30
Interface: 127.0.0.1
done.
Checking for port conflicts
Port conflict detected: 25 (zimbra-mta)
Port conflict detected: 25 (zimbra-mta)
Port conflicts detected! - Any key to continue

Main menu

1) Common Configuration:
2) zimbra-ldap: Enabled
3) zimbra-store: Enabled
+Create Admin User: yes
+Admin user to create: admin@qzulab.cn
******* +Admin Password UNSET
+Anti-virus quarantine user: virus-quarantine.un_zu6gj@qzulab.cn
+Enable automated spam training: yes
+Spam training user: spam.o_xmgng0g@qzulab.cn
+Non-spam(Ham) training user: ham.ky2b1t6t@qzulab.cn
+SMTP host: zcs.qzulab.cn
+Web server HTTP port: 80
+Web server HTTPS port: 443
+Web server mode: http
+IMAP server port: 143
+IMAP server SSL port: 993
+POP server port: 110
+POP server SSL port: 995
+Use spell check server: yes
+Spell server URL: http://zcs.qzulab.cn:7780/aspell.php
+Configure for use with mail proxy: FALSE
+Configure for use with web proxy: FALSE
+Enable version update checks: TRUE
+Enable version update notifications: TRUE
+Version update notification email: admin@qzulab.cn
+Version update source email: admin@qzulab.cn

4) zimbra-mta: Enabled
5) zimbra-snmp: Enabled
6) zimbra-logger: Enabled
7) zimbra-spell: Enabled
8) Default Class of Service Configuration:
r) Start servers after configuration yes
s) Save config to file
x) Expand menu
q) Quit

Address unconfigured (**) items (? - help) 3

Store configuration

1) Status: Enabled
2) Create Admin User: yes
3) Admin user to create: admin@qzulab.cn
** 4) Admin Password UNSET
5) Anti-virus quarantine user: virus-quarantine.un_zu6gj@qzulab.cn
6) Enable automated spam training: yes
7) Spam training user: spam.o_xmgng0g@qzulab.cn
8) Non-spam(Ham) training user: ham.ky2b1t6t@qzulab.cn
9) SMTP host: zcs.qzulab.cn
10) Web server HTTP port: 80
11) Web server HTTPS port: 443
12) Web server mode: http
13) IMAP server port: 143
14) IMAP server SSL port: 993
15) POP server port: 110
16) POP server SSL port: 995
17) Use spell check server: yes
18) Spell server URL: http://zcs.qzulab.cn:7780/aspell.php
19) Configure for use with mail proxy: FALSE
20) Configure for use with web proxy: FALSE
21) Enable version update checks: TRUE
22) Enable version update notifications: TRUE
23) Version update notification email: admin@qzulab.cn
24) Version update source email: admin@qzulab.cn

Select, or 'r' for previous menu [r] 4

Password for admin@qzulab.cn (min 6 characters): [mWVTva0S]

Store configuration

1) Status: Enabled
2) Create Admin User: yes
3) Admin user to create: admin@qzulab.cn
4) Admin Password set
5) Anti-virus quarantine user: virus-quarantine.un_zu6gj@qzulab.cn
6) Enable automated spam training: yes
7) Spam training user: spam.o_xmgng0g@qzulab.cn
8) Non-spam(Ham) training user: ham.ky2b1t6t@qzulab.cn
9) SMTP host: zcs.qzulab.cn
10) Web server HTTP port: 80
11) Web server HTTPS port: 443
12) Web server mode: http
13) IMAP server port: 143
14) IMAP server SSL port: 993
15) POP server port: 110
16) POP server SSL port: 995
17) Use spell check server: yes
18) Spell server URL: http://zcs.qzulab.cn:7780/aspell.php
19) Configure for use with mail proxy: FALSE
20) Configure for use with web proxy: FALSE
21) Enable version update checks: TRUE
22) Enable version update notifications: TRUE
23) Version update notification email: admin@qzulab.cn
24) Version update source email: admin@qzulab.cn

Select, or 'r' for previous menu [r] r

Main menu

1) Common Configuration:
2) zimbra-ldap: Enabled
3) zimbra-store: Enabled
4) zimbra-mta: Enabled
5) zimbra-snmp: Enabled
6) zimbra-logger: Enabled
7) zimbra-spell: Enabled
8) Default Class of Service Configuration:
r) Start servers after configuration yes
s) Save config to file
x) Expand menu
q) Quit

*** CONFIGURATION COMPLETE - press 'a' to apply
Select from menu, or press 'a' to apply config (? - help) s

Save config in file: [/opt/zimbra/config.6802]
Saving config in /opt/zimbra/config.6802...done.

Main menu

1) Common Configuration:
2) zimbra-ldap: Enabled
3) zimbra-store: Enabled
4) zimbra-mta: Enabled
5) zimbra-snmp: Enabled
6) zimbra-logger: Enabled
7) zimbra-spell: Enabled
8) Default Class of Service Configuration:
r) Start servers after configuration yes
s) Save config to file
x) Expand menu
q) Quit

*** CONFIGURATION COMPLETE - press 'a' to apply
Select from menu, or press 'a' to apply config (? - help) q
Quit without applying changes? [No] yes
[root@zcs zcs-7.2.0_GA_2669.RHEL6_64.20120410002025]#

3、完成
<pre>zmcontrol status
zmcontrol restart</pre>
http://zcs.qzulab.cn
管理https://zcs.qzulab.cn:7071
