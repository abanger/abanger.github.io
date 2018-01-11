---
layout: post
title: Centos 6.3安装mysql主从同步过程出错信息及处理
date: 2012-09-21 14:20
author: abanger
comments: true
categories: maintenance
tags: mysql CentOS
---
1、系统没安装mysqld的依赖文件libaio
出错信息
<pre>
[root@myql03 mysql]# rpm -aq | grep mysql
mysql-libs-5.1.61-4.el6.x86_64
[root@myql03 mysql]# ldd ./bin/mysqld
        linux-vdso.so.1 =>  (0x00007ffff1a3e000)
        libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f2f2930b000)
        libaio.so.1 => not found
        libm.so.6 => /lib64/libm.so.6 (0x00007f2f29086000)
        librt.so.1 => /lib64/librt.so.1 (0x00007f2f28e7e000)
        libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f2f28c47000)
        libdl.so.2 => /lib64/libdl.so.2 (0x00007f2f28a42000)
        libc.so.6 => /lib64/libc.so.6 (0x00007f2f286af000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f2f2952d000)
        libfreebl3.so => /lib64/libfreebl3.so (0x00007f2f2844d000)
[root@myql03 mysql]# 
</pre>
<!--more-->
处理
<pre>
yum -y install libaio
</pre>


2、mysql GA2.6配置文件和数据目录不一致
出错信息
<pre>
InnoDB: Foreign key constraint system tables created
120920 15:02:09  InnoDB: Waiting for the background threads to start
120920 15:02:10 InnoDB: 1.1.8 started; log sequence number 0
120920 15:02:10 [Note] Server hostname (bind-address): '0.0.0.0'; port: 3306
120920 15:02:10 [Note]   - '0.0.0.0' resolves to '0.0.0.0';
120920 15:02:10 [Note] Server socket created on IP: '0.0.0.0'.
120920 15:02:10 [ERROR] Fatal error: Can't open and lock privilege tables: Table 'mysql.host' doesn't exist
120920 15:02:10 mysqld_safe mysqld from pid file /usr/local/mysql/data/myql03.qzu.cn.pid ended
</pre>
处理
<pre>
scripts/mysql_install_db --user=mysql --ldata=/usr/local/mysql/data 
</pre>
另一办法在my.cnf配置文件添加
<pre>
[mysqld]
basedir = /usr/local/mysql
datadir = /usr/local/mysql/data
</pre>

3、网上参加一些旧的配置信息有误
出错信息
<pre>
[root@myql06 data]# less myql06.qzu.cn.err 
...
...
InnoDB: Database physically writes the file full: wait...
InnoDB: Progress in MB: 100 200
InnoDB: Doublewrite buffer not found: creating new
InnoDB: Doublewrite buffer created
InnoDB: 127 rollback segment(s) active.
InnoDB: Creating foreign key constraint system tables
InnoDB: Foreign key constraint system tables created
120920 15:42:06  InnoDB: Waiting for the background threads to start
120920 15:42:07 InnoDB: 1.1.8 started; log sequence number 0
120920 15:42:07 [ERROR] /usr/local/mysql/bin/mysqld: unknown variable 'master-host=10.10.1.160'
120920 15:42:07 [ERROR] Aborting

120920 15:42:07  InnoDB: Starting shutdown...
120920 15:42:09  InnoDB: Shutdown completed; log sequence number 1595675
120920 15:42:09 [Note] /usr/local/mysql/bin/mysqld: Shutdown complete

120920 15:42:09 mysqld_safe mysqld from pid file /usr/local/mysql/data/myql06.qzu.cn.pid ended
</pre>
原因：Mysql版本从5.1.7以后开始就不支持“master-host”类似的参数

处理
在slave的mysql控制台上配置如下命令即可
<pre>
STOP SLAVE;

CHANGE MASTER TO MASTER_HOST='10.10.1.160',
MASTER_PORT=3306,
MASTER_USER='backup',
MASTER_PASSWORD='syncpass';
START SLAVE;
</pre>

4、slave出现一台未能同步信息

出错信息
root@myql06 data]# less myql06.qzu.cn.err 
<pre>

120920 16:01:39 [Note] /usr/local/mysql/bin/mysqld: ready for connections.
Version: '5.5.27-log'  socket: '/tmp/mysql.sock'  port: 3306  MySQL Community Server (GPL)
120920 16:12:44 mysqld_safe A mysqld process already exists
120920 16:16:05 [Warning] Neither --relay-log nor --relay-log-index were used; so replication may break when this MySQL server acts as a slave and has his hostname changed!! Please use '--relay-log=myql06-relay-bin' to avoid this problem.
120920 16:16:05 [Note] 'CHANGE MASTER TO executed'. Previous state master_host='', master_port='3306', master_log_file='', master_log_pos='4'. New state master_host='10.10.1.160', master_port='3306', master_log_file='', master_log_pos='4'.
120920 16:16:10 [Note] Slave SQL thread initialized, starting replication in log 'FIRST' at position 0, relay log './myql06-relay-bin.000001' position: 4
120920 16:16:10 [Note] Slave I/O thread: connected to master 'backup@10.10.1.160:3306',replication started in log 'FIRST' at position 4
120920 16:16:10 [ERROR] Slave SQL: Error 'Duplicate entry '%-test-' for key 'PRIMARY'' on query. Default database: 'mysql'. Query: 'INSERT INTO db SELECT * FROM tmp_db WHERE @had_db_table=0', Error_code: 1062
120920 16:16:10 [Warning] Slave: Duplicate entry '%-test-' for key 'PRIMARY' Error_code: 1062
120920 16:16:10 [ERROR] Error running query, slave SQL thread aborted. Fix the problem, and restart the slave SQL thread with "SLAVE START". We stopped at log 'mysql-bin.000001' position 24982
</pre>


[root@myql06 mysql]# ./bin/mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 5
Server version: 5.5.27-log MySQL Community Server (GPL)

Copyright (c) 2000, 2011, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> SHOW SLAVE STATUS \G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 10.10.1.160
                  Master_User: backup
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000005
          Read_Master_Log_Pos: 107
               Relay_Log_File: myql06-relay-bin.000002
                Relay_Log_Pos: 25128
        Relay_Master_Log_File: mysql-bin.000001
             Slave_IO_Running: Yes
            Slave_SQL_Running: No
              Replicate_Do_DB: 
          Replicate_Ignore_DB: mysql,test,information_schema
           Replicate_Do_Table: 
       Replicate_Ignore_Table: 
      Replicate_Wild_Do_Table: 
  Replicate_Wild_Ignore_Table: 
                   Last_Errno: 1062
                   Last_Error: Error 'Duplicate entry '%-test-' for key 'PRIMARY'' on query. Default database: 'mysql'. Query: 'INSERT INTO db SELECT * FROM tmp_db WHERE @had_db_table=0'
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 24982
              Relay_Log_Space: 1066255
              Until_Condition: None
               Until_Log_File: 
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File: 
           Master_SSL_CA_Path: 
              Master_SSL_Cert: 
            Master_SSL_Cipher: 
               Master_SSL_Key: 
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error: 
               Last_SQL_Errno: 1062
               Last_SQL_Error: Error 'Duplicate entry '%-test-' for key 'PRIMARY'' on query. Default database: 'mysql'. Query: 'INSERT INTO db SELECT * FROM tmp_db WHERE @had_db_table=0'
  Replicate_Ignore_Server_Ids: 
             Master_Server_Id: 100
1 row in set (0.00 sec)

ERROR: 
No query specified


解决
在master
[root@myql03 data]# /usr/local/mysql/bin/mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 4
Server version: 5.5.27-log MySQL Community Server (GPL)

Copyright (c) 2000, 2011, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show master status;
+------------------+----------+--------------+------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000005 |      188 |              |                  |
+------------------+----------+--------------+------------------+
1 row in set (0.00 sec)

mysql> 

在slave的mysql控制台上执行
<pre>
CHANGE MASTER TO MASTER_HOST='10.10.1.160',
MASTER_PORT=3306,
MASTER_USER='backup',
MASTER_PASSWORD='syncpass',
MASTER_LOG_FILE='mysql-bin.000005'
master_log_pos=188;;
</pre>



mysql> START SLAVE;
Query OK, 0 rows affected (0.00 sec)

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

