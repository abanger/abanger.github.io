---
layout: post
title: Centos6.3安装zimbra 8.0.2
date: 2013-01-08 15:34
author: abanger
comments: true
categories: zimbra
---
一步一步安装zimbra。

1、环境
Centos6.3 64 最小化安装。
<pre>
less /etc/redhat-release 
</pre>
CentOS release 6.3 (Final)
<pre>
uname -a
</pre>
Linux zcs.qzalab.cn 2.6.32-279.19.1.el6.x86_64 #1 SMP Wed Dec 19 07:05:20 UTC 2012 x86_64 x86_64 x86_64 GNU/Linux


2、yum安装依赖库
<pre>
yum  -y install libidn11 curl fetchmail libpcre3 libgmp3c2 libxml2 libstdc++6 openssl perl sysstat libtool-ltdl compat-libstdc* nc  file
yum -y update
</pre>


3、停止系统默认邮件服务
<pre>
chkconfig postfix off
/etc/init.d/postfix stop
</pre>
Shutting down postfix: [  OK  ]

4、关闭SELINUX
zimbra要求的。
<pre>
vi /etc/selinux/config 
</pre>
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=disabled
# SELINUXTYPE= can take one of these two values:
#     targeted - Targeted processes are protected,
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted

5、修改系统防火墙配置
根据个人需要修改
<pre>
vi /etc/sysconfig/iptables
</pre>

增加iptables内容如下:
<pre>
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
-A INPUT -m state --state NEW -m tcp -p tcp --dport 7780 -j ACCEPT
</pre>

6、修改hosts文件
<pre>
vi /etc/hosts
</pre>
hosts文件内容如下：
<pre>
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
#::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.10.10.20           zcs.qzalab.cn zcs
</pre>

7、重新启动系统
SELINUX需要重启才能生效。
<pre>
reboot
</pre>

8、解压安装文件
tar
<pre>
[root@zcs soft]# tar -xzvf /mnt/nfs/zcs-8.0.2_GA_5569.RHEL6_64.20121210115059.tgz 
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/packages/
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/packages/zimbra-apache-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/packages/zimbra-core-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/packages/zimbra-ldap-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/packages/zimbra-logger-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/packages/zimbra-memcached-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/packages/zimbra-mta-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/packages/zimbra-proxy-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/packages/zimbra-snmp-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/packages/zimbra-spell-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/packages/zimbra-store-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/bin/
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/bin/get_plat_tag.sh
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/bin/zmdbintegrityreport
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/data/
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/data/versions-init.sql
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/YPL.txt
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/zcl.txt
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/zimbra_public_eula_2.1.txt
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/oracle_jdk_eula.txt
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/en_US/
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/en_US/admin.pdf
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/en_US/Fedora Server Config.pdf
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/en_US/Import_Wizard_Outlook.pdf
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/en_US/Migration_Exch_Admin.pdf
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/en_US/Migration_Exch_User.pdf
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/en_US/MigrationWizard_Domino.pdf
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/en_US/MigrationWizard.pdf
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/en_US/OSmultiserverinstall.pdf
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/en_US/quick_start.pdf
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/en_US/RNZCSO_2005Beta.pdf
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/en_US/User Instructions for ZCS Import Wizard.pdf
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/en_US/Zimbra iCalendar Migration Guide.pdf
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/en_US/Zimbra_Release_Note.pdf
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/en_US/Zimbra Schema.pdf
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/docs/en_US/zimbra_user_guide.pdf
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/util/
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/util/modules/
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/util/modules/getconfig.sh
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/util/modules/packages.sh
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/util/modules/postinstall.sh
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/util/addUser.sh
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/util/globals.sh
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/util/utilfunc.sh
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/readme_source_en_US.txt
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/readme_binary_en_US.txt
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/install.sh
zcs-8.0.2_GA_5569.RHEL6_64.20121210115059/README.txt
[root@zcs soft]# 
</pre>

9、安装install
由于是centos使用redhat安装包，所以安装要添加参数--platform-override
<pre>
./install.sh --platform-override
</pre>
安装过程，主要是“Create domain“改变域名为qzalab.cn；修改管理员密码3->4->r->a；本安装是把所有服务安装在一台服务器上，分布式的安装请参考其他资料。
<pre>
[root@zcs zcs-8.0.2_GA_5569.RHEL6_64.20121210115059]# ./install.sh --platform-override

Operations logged to /tmp/install.log.1317
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




Oracle 

LICENSE 略...

Last updated May 17, 2011




Do you agree with the terms of the software license agreement? [N] y

Checking for prerequisites...
     FOUND: NPTL
     FOUND: nc-1.84-22
     FOUND: sudo-1.7.4p5-13
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

Install zimbra-memcached [N] y

Install zimbra-proxy [N] y
Checking required space for zimbra-core
Checking space for zimbra-store

Installing:
    zimbra-core
    zimbra-ldap
    zimbra-logger
    zimbra-mta
    zimbra-snmp
    zimbra-store
    zimbra-apache
    zimbra-spell
    zimbra-memcached
    zimbra-proxy

You appear to be installing packages on a platform different
than the platform for which they were built.

This platform is CentOS6_64
Packages found: RHEL6_64
This may or may not work.

Using packages for a platform in which they were not designed for
may result in an installation that is NOT usable. Your support
options may be limited if you choose to continue.


Install anyway? [N] y

The system will be modified.  Continue? [N] y

Removing /opt/zimbra
Removing zimbra crontab entry...done.
Cleaning up zimbra init scripts...done.
Cleaning up /etc/ld.so.conf...done.
Cleaning up /etc/security/limits.conf...done.

Finished removing Zimbra Collaboration Server.

Installing packages

    zimbra-core......zimbra-core-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm...done
    zimbra-ldap......zimbra-ldap-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm...done
    zimbra-logger......zimbra-logger-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm...done
    zimbra-mta......zimbra-mta-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm...done
    zimbra-snmp......zimbra-snmp-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm...done
    zimbra-store......zimbra-store-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm...done
    zimbra-apache......zimbra-apache-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm...done
    zimbra-spell......zimbra-spell-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm...done
    zimbra-memcached......zimbra-memcached-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm...done
    zimbra-proxy......zimbra-proxy-8.0.2_GA_5569.RHEL6_64-20121210115059.x86_64.rpm...done
Operations logged to /tmp/zmsetup.01082013-094551.log
Installing LDAP configuration database...done.
Setting defaults...
        Interface: 10.10.10.20
        Interface: 127.0.0.1
        Interface: ::1


DNS ERROR - none of the MX records for zcs.qzalab.cn
resolve to this host
Change domain name? [Yes] 
Create domain: [zcs.qzalab.cn] qzalab.cn
        MX: mail.qzalab.cn (10.10.10.20)
        MX: zcs.qzalab.cn (10.10.10.20)

        Interface: 10.10.10.20
        Interface: 127.0.0.1
        Interface: ::1
done.
Checking for port conflicts

Main menu

   1) Common Configuration:                                                  
   2) zimbra-ldap:                             Enabled                       
   3) zimbra-store:                            Enabled                       
        +Create Admin User:                    yes                           
        +Admin user to create:                 admin@qzalab.cn                  
******* +Admin Password                        UNSET                         
        +Anti-virus quarantine user:           virus-quarantine.lelx1uqjz@qzalab.cn
        +Enable automated spam training:       yes                           
        +Spam training user:                   spam.2letin98@qzalab.cn          
        +Non-spam(Ham) training user:          ham.q94pdr4mo1@qzalab.cn         
        +SMTP host:                            zcs.qzalab.cn                    
        +Web server HTTP port:                 80                            
        +Web server HTTPS port:                443                           
        +Web server mode:                      https                         
        +IMAP server port:                     7143                          
        +IMAP server SSL port:                 7993                          
        +POP server port:                      7110                          
        +POP server SSL port:                  7995                          
        +Use spell check server:               yes                           
        +Spell server URL:                     http://zcs.qzalab.cn:7780/aspell.php
        +Enable version update checks:         TRUE                          
        +Enable version update notifications:  TRUE                          
        +Version update notification email:    admin@qzalab.cn                  
        +Version update source email:          admin@qzalab.cn                  

   4) zimbra-mta:                              Enabled                       
   5) zimbra-snmp:                             Enabled                       
   6) zimbra-logger:                           Enabled                       
   7) zimbra-spell:                            Enabled                       
   8) zimbra-proxy:                            Enabled                       
   9) Default Class of Service Configuration:                                
   r) Start servers after configuration        yes                           
   s) Save config to file                                                    
   x) Expand menu                                                            
   q) Quit                                    

Address unconfigured (**) items  (? - help) 3


Store configuration

   1) Status:                                  Enabled                       
   2) Create Admin User:                       yes                           
   3) Admin user to create:                    admin@qzalab.cn                  
** 4) Admin Password                           UNSET                         
   5) Anti-virus quarantine user:              virus-quarantine.lelx1uqjz@qzalab.cn
   6) Enable automated spam training:          yes                           
   7) Spam training user:                      spam.2letin98@qzalab.cn          
   8) Non-spam(Ham) training user:             ham.q94pdr4mo1@qzalab.cn         
   9) SMTP host:                               zcs.qzalab.cn                    
  10) Web server HTTP port:                    80                            
  11) Web server HTTPS port:                   443                           
  12) Web server mode:                         https                         
  13) IMAP server port:                        7143                          
  14) IMAP server SSL port:                    7993                          
  15) POP server port:                         7110                          
  16) POP server SSL port:                     7995                          
  17) Use spell check server:                  yes                           
  18) Spell server URL:                        http://zcs.qzalab.cn:7780/aspell.php
  19) Enable version update checks:            TRUE                          
  20) Enable version update notifications:     TRUE                          
  21) Version update notification email:       admin@qzalab.cn                  
  22) Version update source email:             admin@qzalab.cn                  

Select, or 'r' for previous menu [r] 4

Password for admin@qzalab.cn (min 6 characters): [220BynXaRx] 

Store configuration

   1) Status:                                  Enabled                       
   2) Create Admin User:                       yes                           
   3) Admin user to create:                    admin@qzalab.cn                  
   4) Admin Password                           set                           
   5) Anti-virus quarantine user:              virus-quarantine.lelx1uqjz@qzalab.cn
   6) Enable automated spam training:          yes                           
   7) Spam training user:                      spam.2letin98@qzalab.cn          
   8) Non-spam(Ham) training user:             ham.q94pdr4mo1@qzalab.cn         
   9) SMTP host:                               zcs.qzalab.cn                    
  10) Web server HTTP port:                    80                            
  11) Web server HTTPS port:                   443                           
  12) Web server mode:                         https                         
  13) IMAP server port:                        7143                          
  14) IMAP server SSL port:                    7993                          
  15) POP server port:                         7110                          
  16) POP server SSL port:                     7995                          
  17) Use spell check server:                  yes                           
  18) Spell server URL:                        http://zcs.qzalab.cn:7780/aspell.php
  19) Enable version update checks:            TRUE                          
  20) Enable version update notifications:     TRUE                          
  21) Version update notification email:       admin@qzalab.cn                  
  22) Version update source email:             admin@qzalab.cn                  

Select, or 'r' for previous menu [r] r

Main menu

   1) Common Configuration:                                                  
   2) zimbra-ldap:                             Enabled                       
   3) zimbra-store:                            Enabled                       
   4) zimbra-mta:                              Enabled                       
   5) zimbra-snmp:                             Enabled                       
   6) zimbra-logger:                           Enabled                       
   7) zimbra-spell:                            Enabled                       
   8) zimbra-proxy:                            Enabled                       
   9) Default Class of Service Configuration:                                
   r) Start servers after configuration        yes                           
   s) Save config to file                                                    
   x) Expand menu                                                            
   q) Quit                                    

*** CONFIGURATION COMPLETE - press 'a' to apply
Select from menu, or press 'a' to apply config (? - help) a
Save configuration data to a file? [Yes] 
Save config in file: [/opt/zimbra/config.9262] 
Saving config in /opt/zimbra/config.9262...done.
The system will be modified - continue? [No] yes
Operations logged to /tmp/zmsetup.01082013-094551.log
Setting local config values...done.
Initializing core config...Setting up CA...done.
Deploying CA to /opt/zimbra/conf/ca ...done.
Creating SSL zimbra-store certificate...done.
Creating new zimbra-ldap SSL certificate...done.
Creating new zimbra-mta SSL certificate...done.
Creating new zimbra-proxy SSL certificate...done.
Installing mailboxd SSL certificates...done.
Installing MTA SSL certificates...done.
Installing LDAP SSL certificate...done.
Installing Proxy SSL certificate...done.
Initializing ldap...done.
Setting replication password...done.
Setting Postfix password...done.
Setting amavis password...done.
Setting nginx password...done.
Creating server entry for zcs.qzalab.cn...done.
Setting Zimbra IP Mode...done.
Saving CA in ldap ...done.
Saving SSL Certificate in ldap ...done.
Setting spell check URL...done.
Setting service ports on zcs.qzalab.cn...done.
Adding zcs.qzalab.cn to zimbraMailHostPool in default COS...done.
Setting zimbraFeatureTasksEnabled=TRUE...done.
Setting zimbraFeatureBriefcasesEnabled=FALSE...done.
Setting MTA auth host...done.
Setting TimeZone Preference...done.
Initializing mta config...done.
Setting services on zcs.qzalab.cn...done.
Creating domain qzalab.cn...done.
Setting default domain name...done.
Creating domain qzalab.cn...already exists.
Creating admin account admin@qzalab.cn...done.
Creating root alias...done.
Creating postmaster alias...done.
Creating user spam.2letin98@qzalab.cn...done.
Creating user ham.q94pdr4mo1@qzalab.cn...done.
Creating user virus-quarantine.lelx1uqjz@qzalab.cn...done.
Setting spam training and Anti-virus quarantine accounts...done.
Initializing store sql database...done.
Setting zimbraSmtpHostname for zcs.qzalab.cn...done.
Configuring SNMP...done.
Setting up syslog.conf...done.
Starting servers...done.
Installing common zimlets...
        com_zimbra_clientuploader...done.
        com_zimbra_tooltip...done.
        com_zimbra_viewmail...done.
        com_zimbra_srchhighlighter...done.
        com_zimbra_adminversioncheck...done.
        com_zimbra_proxy_config...done.
        com_zimbra_email...done.
        com_zimbra_date...done.
        com_zimbra_webex...done.
        com_zimbra_ymemoticons...done.
        com_zimbra_phone...done.
        com_zimbra_cert_manager...done.
        com_zimbra_attachcontacts...done.
        com_zimbra_attachmail...done.
        com_zimbra_bulkprovision...done.
        com_zimbra_url...done.
Finished installing common zimlets.
Restarting mailboxd...done.
Creating galsync account for default domain...done.

You have the option of notifying Zimbra of your installation.
This helps us to track the uptake of the Zimbra Collaboration Server.
The only information that will be transmitted is:
        The VERSION of zcs installed (8.0.2_GA_5569_CentOS6_64)
        The ADMIN EMAIL ADDRESS created (admin@qzalab.cn)

Notify Zimbra of your installation? [Yes] 
Notifying Zimbra of installation via http://www.zimbra.com/cgi-bin/notify.cgi?VER=8.0.2_GA_5569_CentOS6_64&MAIL=admin@qzalab.cn

ERROR: Notification failed 
Setting up zimbra crontab...done.


Moving /tmp/zmsetup.01082013-094551.log to /opt/zimbra/log


Configuration complete - press return to exit 

</pre>

10、重启一下zimbra
我习惯安装完成之后，重新启动一下zimbra。
<pre>
su - zimbra
zmcontrol stop
zmcontrol start
</pre>

11、恭祝可以 
http://zcs.qzalab.cn访问了
管理https://zcs.qzulab.cn:7071

参考另一文：http://aba.qzu.cn/archives/251.html
