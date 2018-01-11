---
layout: post
title: centos 7 硬盘扩容过程
date: 2016-12-07 17:24
author: abanger
comments: true
categories: CentOS
---
虚拟机，硬盘空间满，原采用lvm磁盘分区。
大概过程如下：磁盘 => 分区 => PV => VG => LV => 文件系统
1. 添加硬盘

2. 查看环境
<pre>
less /etc/redhat-release 
</pre>
<code>
CentOS Linux release 7.2.1511 (Core) 
</code>
<!--more-->


现有硬盘空间情况
<pre>
df
</pre>
<code>
Filesystem              1K-blocks      Used Available Use% Mounted on
/dev/mapper/centos-root  29669220   3672600  25996620  13% /
devtmpfs                  3995128         0   3995128   0% /dev
tmpfs                     4005328         0   4005328   0% /dev/shm
tmpfs                     4005328      8660   3996668   1% /run
tmpfs                     4005328         0   4005328   0% /sys/fs/cgroup
/dev/mapper/morris-lv01 262011908 259423252   2588656 100% /srv
/dev/sda1                  508588    289872    218716  57% /boot
tmpfs                      801068         0    801068   0% /run/user/0
</code>

文件系统配置文件内容
<pre>
less /etc/fstab 
</pre>

<code>
#
# /etc/fstab
# Created by anaconda on Thu Jul 10 07:18:36 2014
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        1 1
UUID=f5a3af00-b538-456f-8aae-fe787f5baf53 /boot                   xfs     defaults        1 2
/dev/mapper/centos-swap swap                    swap    defaults        0 0
/dev/mapper/morris-lv01 /srv            xfs defaults        0  0
</code>

磁盘分区情况
<pre>
fdisk  -l
</pre>
<code>
Disk /dev/sda: 34.4 GB, 34359738368 bytes, 67108864 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000a6701

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     1026047      512000   83  Linux
/dev/sda2         1026048    67108863    33041408   8e  Linux LVM

Disk /dev/sdc: 107.4 GB, 107374182400 bytes, 209715200 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/sdb: 268.4 GB, 268435456000 bytes, 524288000 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-swap: 3435 MB, 3435134976 bytes, 6709248 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-root: 30.4 GB, 30396121088 bytes, 59367424 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/morris-lv01: 268.4 GB, 268431261696 bytes, 524279808 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

</code>



显示LVM卷组的信息
<pre>
vgdisplay
</pre>
<code>
  --- Volume group ---
  VG Name               centos
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               31.51 GiB
  PE Size               4.00 MiB
  Total PE              8066
  Alloc PE / Size       8066 / 31.51 GiB
  Free  PE / Size       0 / 0   
  VG UUID               lE0V3z-UjaD-B3m2-rRrp-vMhf-odIp-VYIs8S
   
  --- Volume group ---
  VG Name               morris
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  2
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                1
  Open LV               1
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               250.00 GiB
  PE Size               4.00 MiB
  Total PE              63999
  Alloc PE / Size       63999 / 250.00 GiB
  Free  PE / Size       0 / 0   
  VG UUID               TJFfFD-G7zB-EYBP-UkvT-J5QA-A93g-EyoYS1
   
</code>


3. 添加卷组
将刚加入的物理硬盘分区初始化为物理卷，以便LVM使用。
<pre>
pvcreate /dev/sdc 
</pre>
<code>
  Physical volume "/dev/sdc" successfully created
</code>


4. 将整块物理卷扩展到morris卷组
<pre>
 vgextend morris /dev/sdc
</pre>
<code>
  Volume group "morris" successfully extended
</code>
查看卷组情况
<pre>
vgdisplay
</pre>
<code>
  --- Volume group ---
  VG Name               centos
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               31.51 GiB
  PE Size               4.00 MiB
  Total PE              8066
  Alloc PE / Size       8066 / 31.51 GiB
  Free  PE / Size       0 / 0   
  VG UUID               lE0V3z-UjaD-B3m2-rRrp-vMhf-odIp-VYIs8S
   
  --- Volume group ---
  VG Name               morris
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                1
  Open LV               1
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               349.99 GiB
  PE Size               4.00 MiB
  Total PE              89598
  Alloc PE / Size       63999 / 250.00 GiB
  Free  PE / Size       25599 / 100.00 GiB
  VG UUID               TJFfFD-G7zB-EYBP-UkvT-J5QA-A93g-EyoYS1

</code>
  Free  PE / Size       25599 / 100.00 GiB  为增加的硬盘

显示当前lv
<pre>
lvdisplay 
</pre>
<code>
  --- Logical volume ---
  LV Path                /dev/centos/swap
  LV Name                swap
  VG Name                centos
  LV UUID                WY7c7k-2Sz3-LEGE-Mq3s-okFP-I5Cp-MBT3cq
  LV Write Access        read/write
  LV Creation host, time localhost, 2014-07-10 15:18:34 +0800
  LV Status              available
  # open                 2
  LV Size                3.20 GiB
  Current LE             819
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:0
   
  --- Logical volume ---
  LV Path                /dev/centos/root
  LV Name                root
  VG Name                centos
  LV UUID                QkdwSd-PCjN-CnZo-WHfJ-BTRT-vJsx-djy1ti
  LV Write Access        read/write
  LV Creation host, time localhost, 2014-07-10 15:18:34 +0800
  LV Status              available
  # open                 1
  LV Size                28.31 GiB
  Current LE             7247
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:1
   
  --- Logical volume ---
  LV Path                /dev/morris/lv01
  LV Name                lv01
  VG Name                morris
  LV UUID                E5ja0r-uYXs-0ycD-jY2v-a8JF-Kx13-5DLG6E
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2014-07-10 16:16:15 +0800
  LV Status              available
  # open                 1
  LV Size                250.00 GiB
  Current LE             63999
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:2
</code>
  
5. 在线扩展逻辑卷的空间大小
在线扩展逻辑卷的空间到/dev/morris/lv01，全部用到lv01
<pre>
lvextend  -L +99G /dev/morris/lv01  
</pre>

<code>
  Size of logical volume morris/lv01 changed from 250.00 GiB (63999 extents) to 349.00 GiB (89343 extents).
  Logical volume lv01 successfully resized.
</code>

查看vg和lv
<pre>
vgdisplay 
</pre>
<code>
  --- Volume group ---
  VG Name               centos
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               31.51 GiB
  PE Size               4.00 MiB
  Total PE              8066
  Alloc PE / Size       8066 / 31.51 GiB
  Free  PE / Size       0 / 0   
  VG UUID               lE0V3z-UjaD-B3m2-rRrp-vMhf-odIp-VYIs8S
   
  --- Volume group ---
  VG Name               morris
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  4
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                1
  Open LV               1
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               349.99 GiB
  PE Size               4.00 MiB
  Total PE              89598
  Alloc PE / Size       89343 / 349.00 GiB
  Free  PE / Size       255 / 1020.00 MiB   -----------------------&lt;1G
  VG UUID               TJFfFD-G7zB-EYBP-UkvT-J5QA-A93g-EyoYS1
  
</code>  
查看逻辑卷
<pre> 
 lvdisplay 
</pre>  
<code>
  --- Logical volume ---
  LV Path                /dev/centos/swap
  LV Name                swap
  VG Name                centos
  LV UUID                WY7c7k-2Sz3-LEGE-Mq3s-okFP-I5Cp-MBT3cq
  LV Write Access        read/write
  LV Creation host, time localhost, 2014-07-10 15:18:34 +0800
  LV Status              available
  # open                 2
  LV Size                3.20 GiB
  Current LE             819
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:0
   
  --- Logical volume ---
  LV Path                /dev/centos/root
  LV Name                root
  VG Name                centos
  LV UUID                QkdwSd-PCjN-CnZo-WHfJ-BTRT-vJsx-djy1ti
  LV Write Access        read/write
  LV Creation host, time localhost, 2014-07-10 15:18:34 +0800
  LV Status              available
  # open                 1
  LV Size                28.31 GiB
  Current LE             7247
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:1
   
  --- Logical volume ---
  LV Path                /dev/morris/lv01
  LV Name                lv01
  VG Name                morris
  LV UUID                E5ja0r-uYXs-0ycD-jY2v-a8JF-Kx13-5DLG6E
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2014-07-10 16:16:15 +0800
  LV Status              available
  # open                 1
  LV Size                349.00 GiB
  Current LE             89343
  Segments               2
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:2
   
</code>

查看硬盘分区情况
<pre>  
df -H
</pre>
<code>
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   31G  3.8G   27G  13% /
devtmpfs                 4.1G     0  4.1G   0% /dev
tmpfs                    4.2G     0  4.2G   0% /dev/shm
tmpfs                    4.2G  8.9M  4.1G   1% /run
tmpfs                    4.2G     0  4.2G   0% /sys/fs/cgroup
/dev/mapper/morris-lv01  269G  266G  2.7G 100% /srv
/dev/sda1                521M  297M  224M  57% /boot
tmpfs                    821M     0  821M   0% /run/user/0
</code> 



6. xfs_growfs扩展XFS文件系统
xfs_growfs将会自动扩展XFS文件系统到最大的可用大小

<pre>
xfs_growfs /dev/morris/lv01
</pre>
<code>
meta-data=/dev/mapper/morris-lv01 isize=256    agcount=4, agsize=16383744 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=0        finobt=0
data     =                       bsize=4096   blocks=65534976, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=0
log      =internal               bsize=4096   blocks=31999, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 65534976 to 91487232
</code>


查看硬盘分区情况
<pre>  
df -H
</pre>  
<code> 
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   31G  3.8G   27G  13% /
devtmpfs                 4.1G     0  4.1G   0% /dev
tmpfs                    4.2G     0  4.2G   0% /dev/shm
tmpfs                    4.2G  8.9M  4.1G   1% /run
tmpfs                    4.2G     0  4.2G   0% /sys/fs/cgroup
/dev/mapper/morris-lv01  375G  266G  109G  71% /srv
/dev/sda1                521M  297M  224M  57% /boot
tmpfs                    821M     0  821M   0% /run/user/0
</code>  

/dev/mapper/morris-lv01  375G  266G  109G  71% /srv  
显示成功添加完成。

8. 出错信息
<pre> 
resize2fs   -p  /dev/morris/lv01
</pre> 
<code> 
resize2fs 1.42.9 (28-Dec-2013)
resize2fs: Bad magic number in super-block while trying to open /dev/morris/lv01
Couldn't find valid filesystem superblock.
[</code> 
若xfs，不能用resize2fs
