---
layout: post
title: Centos 6.3 安装mysql主从同步
date: 2012-09-21 13:59
author: abanger
comments: true
categories: maintenance
tags: CentOS mysql
---
一、环境
mysql-5.5.27
  10.10.1.160 master160
  10.10.1.168 slave168
[root@myql03 ~]# uname -a
Linux myql03.qzu.cn 2.6.32-279.5.2.el6.x86_64 #1 SMP Fri Aug 24 01:07:11 UTC 2012 x86_64 x86_64 x86_64 GNU/Linux
[root@myql03 ~]# less /etc/redhat-release 
CentOS release 6.3 (Final)
主从环境配置一样（也可不一样）。
<!--more-->
二、mysql安装
<pre>
tar -xzvf mysql-5.5.27-linux2.6-x86_64.tar.gz 
mv mysql-5.5.27-linux2.6-x86_64 /usr/local/
cd /usr/local
ln -s  mysql-5.5.27-linux2.6-x86_64 mysql
  
groupadd mysql
useradd  -s /sbin/nologin -r -g  mysql mysql
cd mysql
chown -R mysql .
chgrp -R mysql .
##scripts/mysql_install_db --user=mysql
scripts/mysql_install_db --user=mysql --ldata=/usr/local/mysql/data 
chown -R root .
chown -R mysql.mysql data

cp support-files/my-innodb-heavy-4G.cnf /etc/my.cnf

bin/mysqld_safe --user=mysql &
cp support-files/mysql.server /etc/init.d/mysql.server
chkconfig mysql.server on
</pre>
三、修改mysql配置
master160  
修改或增加/etc/my.cnf的内容如下
<pre>
[client]
default-character-set=utf8 

[mysqld]
server-id = 100
character-set-server = utf8
collation-server = utf8_general_ci
##init-connect = SET NAMES utf8
#3skip-character-set-client-handshake

log-bin=mysql-bin
innodb_flush_log_at_trx_commit=1
replicate-ignore-db = mysql
replicate-ignore-db = test
replicate-ignore-db = information_schema
##replicate-do-db = db
</pre>

<pre>
/etc/init.d/mysql.server restart
</pre>
在mysql控制台执行如下命令
<pre>
GRANT REPLICATION SLAVE ON *.* TO 'backup'@'%' IDENTIFIED BY 'syncpass';
flush privileges;
</pre>


slave168
修改或增加/etc/my.cnf的内容如下
<pre>
[client]
default-character-set=utf8

[mysqld]
server-id = 201
character-set-server = utf8
collation-server = utf8_general_ci
log-bin=mysql-bin
master-connect-retry=60
replicate-ignore-db=mysql,test
//replicate-do-db=testlink  //all
</pre>
<pre>
/etc/init.d/mysql.server restart
</pre>
在mysql控制台执行如下命令

<pre>
STOP SLAVE;

CHANGE MASTER TO MASTER_HOST='10.10.1.160',
MASTER_PORT=3306,
MASTER_USER='backup',
MASTER_PASSWORD='syncpass';

START SLAVE;
</pre>

四、测试
<pre>
SHOW SLAVE STATUS \G;
</pre>
<pre>

mysql> SHOW SLAVE STATUS \G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 10.10.1.160
                  Master_User: backup
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000005
          Read_Master_Log_Pos: 188
               Relay_Log_File: myql06-relay-bin.000002
                Relay_Log_Pos: 253
        Relay_Master_Log_File: mysql-bin.000005
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB: 
          Replicate_Ignore_DB: mysql,test,information_schema
           Replicate_Do_Table: 
       Replicate_Ignore_Table: 
      Replicate_Wild_Do_Table: 
  Replicate_Wild_Ignore_Table: 
                   Last_Errno: 0
                   Last_Error: 
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 188
              Relay_Log_Space: 414
              Until_Condition: None
               Until_Log_File: 
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File: 
           Master_SSL_CA_Path: 
              Master_SSL_Cert: 
            Master_SSL_Cipher: 
               Master_SSL_Key: 
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error: 
               Last_SQL_Errno: 0
               Last_SQL_Error: 
  Replicate_Ignore_Server_Ids: 
             Master_Server_Id: 100
1 row in set (0.00 sec)

ERROR: 
No query specified

OK

</pre>
正常
