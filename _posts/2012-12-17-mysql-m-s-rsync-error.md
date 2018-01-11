---
layout: post
title: mysql主从同步出错日记（解决）
date: 2012-12-17 15:32
author: abanger
comments: true
categories: maintenance
---
master出现的错误
<pre>
InnoDB: Starting crash recovery.
InnoDB: Reading tablespace information from the .ibd files...
InnoDB: Restoring possible half-written data pages from the doublewrite
InnoDB: buffer...
InnoDB: Last MySQL binlog file position 0 8835308, file name ./mysql-bin.000005
121129 15:33:44 InnoDB: Waiting for the background threads to start
121129 15:33:45 InnoDB: 1.1.8 started; log sequence number 19007072
121129 15:33:45 [Note] Recovering after a crash using mysql-bin
121129 15:34:03 [Note] Starting crash recovery...
121129 15:34:03 [Note] Crash recovery finished.
121129 15:34:03 [Note] Server hostname (bind-address): '0.0.0.0'; port: 3306
121129 15:34:03 [Note] - '0.0.0.0' resolves to '0.0.0.0';
121129 15:34:03 [Note] Server socket created on IP: '0.0.0.0'.
121129 15:34:03 [ERROR] /usr/local/mysql/bin/mysqld: Table './mysql/user' is marked as crashed and should be repaired
121129 15:34:03 [Warning] Checking table: './mysql/user'
121129 15:34:03 [ERROR] 1 client is using or hasn't closed the table properly
121129 15:34:03 [ERROR] /usr/local/mysql/bin/mysqld: Table './mysql/db' is marked as crashed and should be repaired
121129 15:34:03 [Warning] Checking table: './mysql/db'
121129 15:34:03 [ERROR] 1 client is using or hasn't closed the table properly
121129 15:34:03 [Note] Event Scheduler: Loaded 0 events
121129 15:34:03 [Note] /usr/local/mysql/bin/mysqld: ready for connections.
Version: '5.5.27-log' socket: '/tmp/mysql.sock' port: 3306 MySQL Community Server (GPL)
121130 9:02:23 [ERROR] /usr/local/mysql/bin/mysqld: Table './naaaacct/userauth' is marked as crashed and should be repaired
121130 9:02:23 [Warning] Checking table: './naaaacct/userauth'
121207 17:03:09 [ERROR] /usr/local/mysql/bin/mysqld: Table './naaaacct/acct_v1_2012_11_12' is marked as crashed and should be repaired
121207 17:03:09 [Warning] Checking table: './naaaacct/acct_v1_2012_11_12'
121207 17:03:09 [ERROR] /usr/local/mysql/bin/mysqld: Table './naaaacct/acct_v1_2012_11_13' is marked as crashed and should be repaired
121207 17:03:09 [Warning] Checking table: './naaaacct/acct_v1_2012_11_13'
121207 17:03:10 [ERROR] /usr/local/mysql/bin/mysqld: Table './naaaacct/acct_v1_2012_11_14' is marked as crashed and should be repaired
121207 17:03:10 [Warning] Checking table: './naaaacct/acct_v1_2012_11_14'
121207 17:03:11 [ERROR] /usr/local/mysql/bin/mysqld: Table './naaaacct/acct_v1_2012_11_15' is marked as crashed and should be repaired
121207 17:03:11 [Warning] Checking table: './naaaacct/acct_v1_2012_11_15'
121207 17:03:12 [ERROR] /usr/local/mysql/bin/mysqld: Table './naaaacct/acct_v1_2012_11_16' is marked as crashed and should be repaired
121207 17:03:12 [Warning] Checking table: './naaaacct/acct_v1_2012_11_16'
121207 17:03:13 [ERROR] /usr/local/mysql/bin/mysqld: Table './naaaacct/acct_v1_2012_11_17' is marked as crashed and should be repaired
121207 17:03:13 [Warning] Checking table: './naaaacct/acct_v1_2012_11_17'
121207 17:03:13 [ERROR] /usr/local/mysql/bin/mysqld: Table './naaaacct/acct_v1_2012_11_18' is marked as crashed and should be repaired
121207 17:03:13 [Warning] Checking table: './naaaacct/acct_v1_2012_11_18'
121207 17:03:14 [ERROR] /usr/local/mysql/bin/mysqld: Table './naaaacct/acct_v1_2012_11_19' is marked as crashed and should be repaired
121207 17:03:14 [Warning] Checking table: './naaaacct/acct_v1_2012_11_19'
121207 17:03:15 [ERROR] /usr/local/mysql/bin/mysqld: Table './naaaacct/acct_v1_2012_11_20' is marked as crashed and should be repaired
121207 17:03:15 [Warning] Checking table: './naaaacct/acct_v1_2012_11_20'
121207 17:03:16 [Warning] Recovering table: './naaaacct/acct_v1_2012_11_20'
121207 17:03:16 [Note] Retrying repair of: './naaaacct/acct_v1_2012_11_20' with keycache
121207 17:03:16 [ERROR] Couldn't repair table: naaaacct.acct_v1_2012_11_20
121209 17:46:16 [ERROR] /usr/local/mysql/bin/mysqld: Table './naaaacct/acct_v1_2012_11_20' is marked as crashed and last (automatic?) repair failed
121209 17:48:06 [ERROR] /usr/local/mysql/bin/mysqld: Table './naaaacct/acct_v1_2012_11_21' is marked as crashed and should be repaired
121209 17:48:06 [Warning] Checking table: './naaaacct/acct_v1_2012_11_21'
121209 17:48:12 [ERROR] /usr/local/mysql/bin/mysqld: Table './naaaacct/acct_v1_2012_11_20' is marked as crashed and last (automatic?) repair failed
</pre>

slave出现的错误
<pre>
InnoDB: Last MySQL binlog file position 0 188, file name ./mysql-bin.000001
121129 15:33:30  InnoDB: Waiting for the background threads to start
121129 15:33:31 InnoDB: 1.1.8 started; log sequence number 18987706
121129 15:33:31 [Note] Recovering after a crash using mysql-bin
121129 15:33:31 [Note] Starting crash recovery...
121129 15:33:31 [Note] Crash recovery finished.
121129 15:33:31 [Note] Server hostname (bind-address): '0.0.0.0'; port: 3306
121129 15:33:31 [Note]   - '0.0.0.0' resolves to '0.0.0.0';
121129 15:33:31 [Note] Server socket created on IP: '0.0.0.0'.
121129 15:33:31 [ERROR] /usr/local/mysql/bin/mysqld: Table './mysql/user' is marked as crashed and should be repaired
121129 15:33:31 [Warning] Checking table:   './mysql/user'
121129 15:33:31 [ERROR] 1 client is using or hasn't closed the table properly
121129 15:33:31 [ERROR] /usr/local/mysql/bin/mysqld: Table './mysql/db' is marked as crashed and should be repaired
121129 15:33:31 [Warning] Checking table:   './mysql/db'
121129 15:33:31 [ERROR] 1 client is using or hasn't closed the table properly
121129 15:33:31 [Warning] Neither --relay-log nor --relay-log-index were used; so replication may break when this MySQL server acts as a slave and has his hostname changed!! Please use '--relay-log=v01mysql03-relay-bin' to avoid this problem.
121129 15:33:31 [Note] Slave SQL thread initialized, starting replication in log 'mysql-bin.000005' at position 951516569, relay log './v01mysql03-relay-bin.000002' position: 951516634
121129 15:33:31 [Note] Event Scheduler: Loaded 0 events
121129 15:33:31 [Note] /usr/local/mysql/bin/mysqld: ready for connections.
Version: '5.5.27-log'  socket: '/tmp/mysql.sock'  port: 3306  MySQL Community Server (GPL)

</pre>


解决：
<pre>
/etc/init.d/mysql.server stop
/usr/local/mysql/bin/myisamchk -r data//naaaacct/acct_v1_2012_11_20.MYI
- recovering (with sort) MyISAM-table 'acct_v1_2012_11_20.MYI'
Data records: 0
- Fixing index 1
Data records: 66282

/etc/init.d/mysql.server start
</pre>


原因期待高手说明。


