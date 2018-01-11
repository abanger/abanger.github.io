---
layout: post
title: ESXi5.1调用对象"ha-datastoresystem"出错及处理
date: 2013-01-10 14:26
author: abanger
comments: true
categories: maintenance
tags: esxi vmware
---
VMware vSphere Client上显示：在 ESXi“10.10.10.3”上调用对象 “ha-datastoresystem”的“HostDatastoreSystem.QueryVmfsDatastoreCreateOptions” 失败。

google查询，初步判断硬盘（此硬盘原来安装其他操作系统）没有空间写暂时文件。

使用ssh登录到ESXi服务器，删除分区，然后VMware客户端登录，添加存储器，一切正常。

下面是ssh登录ESXi服务器操作过程：

1、查看磁盘情况 
<pre>
fdisk -l
</pre>
显示
<pre>
***
*** The fdisk command is deprecated: fdisk does not handle GPT partitions.  Please use partedUtil
***


Disk /dev/disks/naa.60026b90489ea400128bf67510276afb: 1348.4 GB, 1348485513216 bytes
255 heads, 63 sectors/track, 163944 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes

                                           Device Boot      Start         End      Blocks  Id System
/dev/disks/naa.60026b90489ea400128bf67510276afbp1               1          10       80293+ de Unknown
/dev/disks/naa.60026b90489ea400128bf67510276afbp2   *          11          36      204800  83 Linux
Partition 2 does not end on cylinder boundary
/dev/disks/naa.60026b90489ea400128bf67510276afbp3              36      102022   819200000  83 Linux
/dev/disks/naa.60026b90489ea400128bf67510276afbp4          102022      163945   497394688   5 Extended
/dev/disks/naa.60026b90489ea400128bf67510276afbp5          102022      104062    16384000  82 Linux swap
/dev/disks/naa.60026b90489ea400128bf67510276afbp6          104062      163945   481008640  83 Linux
Found valid GPT with protective MBR; using GPT

Disk /dev/disks/mpx.vmhba32:C0:T0:L0: 15597568 sectors, 14.8M
Logical sector size: 512
Disk identifier (GUID): 9256ab3a-46e8-4244-ab25-8889ff503c28
Partition table holds up to 128 entries
First usable sector is 34, last usable sector is 15597534

Number  Start (sector)    End (sector)  Size       Code  Name
   1              64            8191        8128   0700  
   5            8224          520191        499K   0700  
   6          520224         1032191        499K   0700  
   7         1032224         1257471        219K   0700  
   8         1257504         1843199        571K   0700  
~ # 

</pre>


2、删除磁盘分区
注意上面磁盘/dev/disks/mpx.vmhba32:C0:T0:L0是USB盘,ESXi系统是安装在USB盘上的。
对/dev/disks/naa.60026b90489ea400128bf67510276afb操作，删除分区。
<pre>
fdisk /dev/disks/naa.60026b90489ea400128bf67510276afb
</pre>

过程如下：
<pre>
***
*** The fdisk command is deprecated: fdisk does not handle GPT partitions.  Please use partedUtil
***


The number of cylinders for this disk is set to 163944.
There is nothing wrong with that, but this is larger than 1024,
and could in certain setups cause problems with:
1) software that runs at boot time (e.g., old versions of LILO)
2) booting and partitioning software from other OSs
   (e.g., DOS FDISK, OS/2 FDISK)

Command (m for help):
Command (m for help): d
Partition number (1-6): 6

Command (m for help): d
Partition number (1-5): 5

Command (m for help): d

Command (m for help): d
Partition number (1-5): 4

Command (m for help): d
Partition number (1-4): 3

Command (m for help): d
Partition number (1-4): 2

Command (m for help): p

Disk /dev/disks/naa.60026b90489ea400128bf67510276afb: 1348.4 GB, 1348485513216 bytes
255 heads, 63 sectors/track, 163944 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes

                                           Device Boot      Start         End      Blocks  Id System
/dev/disks/naa.60026b90489ea400128bf67510276afbp1               1          10       80293+ de Unknown

Command (m for help): d
Selected partition 1


Command (m for help): p

Disk /dev/disks/naa.60026b90489ea400128bf67510276afb: 1348.4 GB, 1348485513216 bytes
255 heads, 63 sectors/track, 163944 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes

                                           Device Boot      Start         End      Blocks  Id System

Command (m for help): 


Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table
</pre>



显示删除分区后
<pre>
fdisk -l
</pre>
显示分区
<pre>
***
*** The fdisk command is deprecated: fdisk does not handle GPT partitions.  Please use partedUtil
***


Disk /dev/disks/naa.60026b90489ea400128bf67510276afb: 1348.4 GB, 1348485513216 bytes
255 heads, 63 sectors/track, 163944 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes

                                           Device Boot      Start         End      Blocks  Id System
Found valid GPT with protective MBR; using GPT

Disk /dev/disks/mpx.vmhba32:C0:T0:L0: 15597568 sectors, 14.8M
Logical sector size: 512
Disk identifier (GUID): 9256ab3a-46e8-4244-ab25-8889ff503c28
Partition table holds up to 128 entries
First usable sector is 34, last usable sector is 15597534

Number  Start (sector)    End (sector)  Size       Code  Name
   1              64            8191        8128   0700  
   5            8224          520191        499K   0700  
   6          520224         1032191        499K   0700  
   7         1032224         1257471        219K   0700  
   8         1257504         1843199        571K   0700  
</pre>


3、vmware图形界面添加存储器
在vmware图形界面添加存储器后，ssh登录到ESXi服务器显示分区状态。
<pre>
fdisk -l
</pre>
显示分区
<pre>
***
*** The fdisk command is deprecated: fdisk does not handle GPT partitions.  Please use partedUtil
***

Found valid GPT with protective MBR; using GPT

Disk /dev/disks/naa.60026b90489ea400128bf67510276afb: 2633760768 sectors, 2511M
Logical sector size: 512
Disk identifier (GUID): 737e524b-e22c-4f7d-9e43-76be232198f5
Partition table holds up to 128 entries
First usable sector is 34, last usable sector is 2633760734

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048      2633760734       2511M   0700  
Found valid GPT with protective MBR; using GPT

Disk /dev/disks/mpx.vmhba32:C0:T0:L0: 15597568 sectors, 14.8M
Logical sector size: 512
Disk identifier (GUID): 9256ab3a-46e8-4244-ab25-8889ff503c28
Partition table holds up to 128 entries
First usable sector is 34, last usable sector is 15597534

Number  Start (sector)    End (sector)  Size       Code  Name
   1              64            8191        8128   0700  
   5            8224          520191        499K   0700  
   6          520224         1032191        499K   0700  
   7         1032224         1257471        219K   0700  
   8         1257504         1843199        571K   0700     
</pre>

4、结束
系统正常。
   
