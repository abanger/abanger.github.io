---
layout: post
title: ESXi5.5  “storageSystem”的“HostStorageSystem.ComputeDiskPartitionInfo” 失败的处理
date: 2014-03-05 16:45
author: abanger
comments: true
categories: maintenance
---
VMware vSphere Client上显示：

在 ESXi“10.1.10.204”上调用对象 “storageSystem”的“HostStorageSystem.ComputeDiskPartitionInfo” 失败。

与前面提到“<a href="http://aba.qzu.cn/archives/531.html">ESXi5.1调用对象”ha-datastoresystem”出错及处理</a>”是一样，硬盘（此硬盘原来安装其他操作系统）没有空间写暂时文件。

使用ssh登录到ESXi服务器，删除分区，然后VMware客户端登录，添加存储器，一切正常，不过过程已和上一篇不一样了，vmware换了工具partedUtil。

下面是ssh登录ESXi服务器操作过程：
<!--more-->


1、查看磁盘情况
<pre>
 df -h
</pre>
显示
<pre>
Filesystem   Size   Used Available Use% Mounted on
vfat       249.7M 157.0M     92.7M  63% /vmfs/volumes/29c4e0e1-03947b68-ee9b-d8b9fcb5cfd5
vfat       249.7M   8.0K    249.7M   0% /vmfs/volumes/7adf65e5-16d6006a-5f4f-7c90aa1146d5
vfat       285.8M 191.3M     94.6M  67% /vmfs/volumes/530f0260-07cea110-13af-c81f66e0e373
~ # fdisk -l

***
*** The fdisk command is deprecated: fdisk does not handle GPT partitions.  Please use partedUtil
***
</pre>
提示使用partedUtil

2. 使用esxcfg-scsidevs查看设备
<pre>
esxcfg-scsidevs -l
</pre>
显示
<pre>
mpx.vmhba32:C0:T0:L0
   Device Type: Direct-Access
   Size: 15384 MB
   Display Name: Local USB Direct-Access (mpx.vmhba32:C0:T0:L0)
   Multipath Plugin: NMP
   Console Device: /vmfs/devices/disks/mpx.vmhba32:C0:T0:L0
   Devfs Path: /vmfs/devices/disks/mpx.vmhba32:C0:T0:L0
   Vendor: UFD 2.0   Model: Silicon-Power16G  Revis: 1100
   SCSI Level: 2  Is Pseudo: false Status: on
   Is RDM Capable: false Is Removable: true
   Is Local: true  Is SSD: false
   Other Names:
      vml.0000000000766d68626133323a303a30
   VAAI Status: unsupported
naa.6c81f660da37aa001a6b6c7c1a6ada06
   Device Type: Direct-Access
   Size: 1887435 MB
   Display Name: Local DELL Disk (naa.6c81f660da37aa001a6b6c7c1a6ada06)
   Multipath Plugin: NMP
   Console Device: /vmfs/devices/disks/naa.6c81f660da37aa001a6b6c7c1a6ada06
   Devfs Path: /vmfs/devices/disks/naa.6c81f660da37aa001a6b6c7c1a6ada06
   Vendor: DELL      Model: PERC H700         Revis: 2.10
   SCSI Level: 5  Is Pseudo: false Status: on
   Is RDM Capable: false Is Removable: false
   Is Local: true  Is SSD: false
   Other Names:
      vml.02000000006c81f660da37aa001a6b6c7c1a6ada06504552432048
   VAAI Status: unsupported
naa.6c81f660da37aa001a6b6c881b208efd
   Device Type: Direct-Access
   Size: 1887435 MB
   Display Name: Local DELL Disk (naa.6c81f660da37aa001a6b6c881b208efd)
   Multipath Plugin: NMP
   Console Device: /vmfs/devices/disks/naa.6c81f660da37aa001a6b6c881b208efd
   Devfs Path: /vmfs/devices/disks/naa.6c81f660da37aa001a6b6c881b208efd
   Vendor: DELL      Model: PERC H700         Revis: 2.10
   SCSI Level: 5  Is Pseudo: false Status: on
   Is RDM Capable: false Is Removable: false
   Is Local: true  Is SSD: false
   Other Names:
      vml.02000000006c81f660da37aa001a6b6c881b208efd504552432048
   VAAI Status: unsupported
naa.6c81f660da37aa001a6b6c931bc932c2
   Device Type: Direct-Access
   Size: 227560 MB
   Display Name: Local DELL Disk (naa.6c81f660da37aa001a6b6c931bc932c2)
   Multipath Plugin: NMP
   Console Device: /vmfs/devices/disks/naa.6c81f660da37aa001a6b6c931bc932c2
   Devfs Path: /vmfs/devices/disks/naa.6c81f660da37aa001a6b6c931bc932c2
   Vendor: DELL      Model: PERC H700         Revis: 2.10
   SCSI Level: 5  Is Pseudo: false Status: on
   Is RDM Capable: false Is Removable: false
   Is Local: true  Is SSD: false
   Other Names:
      vml.02000000006c81f660da37aa001a6b6c931bc932c2504552432048
   VAAI Status: unsupported
t10.DP______BACKPLANE000000
   Device Type: Enclosure Svc Dev
   Size: 0 MB
   Display Name: Local DP Enclosure Svc Dev (t10.DP______BACKPLANE000000)
   Multipath Plugin: NMP
   Console Device: /vmfs/devices/genscsi/t10.DP______BACKPLANE000000
   Devfs Path: /vmfs/devices/genscsi/t10.DP______BACKPLANE000000
   Vendor: DP        Model: BACKPLANE         Revis: 1.09
   SCSI Level: 5  Is Pseudo: false Status: on
   Is RDM Capable: false Is Removable: false
   Is Local: true  Is SSD: false
   Other Names:
      vml.000d000000766d686261303a33323a30
   VAAI Status: unsupported
</pre>



3. 删除磁盘分区
注意上面磁盘mpx.vmhba32:C0:T0:L0是USB盘,ESXi系统是安装在USB盘上的。
对/vmfs/devices/disks/naa.xxxxxxxxxx操作，删除分区，注意本例是删除所有原来分区，如要保留得小心辨认。


查看分区情况
<pre>
partedUtil get /vmfs/devices/disks/naa.6c81f660da37aa001a6b6c7c1a6ada06
</pre>
显示
<pre>
240614 255 63 3865468544
1 36 65987 222 0
2 67584 4261887 11 128
</pre>
删除
<pre>
partedUtil delete /vmfs/devices/disks/naa.6c81f660da37aa001a6b6c7c1a6ada06 1
partedUtil delete /vmfs/devices/disks/naa.6c81f660da37aa001a6b6c7c1a6ada06 2
</pre>

其他两个类似

<pre>
partedUtil delete /vmfs/devices/disks/naa.6c81f660da37aa001a6b6c881b208efd 1
partedUtil delete /vmfs/devices/disks/naa.6c81f660da37aa001a6b6c931bc932c2 1
</pre>




4. 删除后重启系统，不再显示出错信息完成

查看分区情况如下：

<pre>
 esxcfg-scsidevs -l
</pre>
显示
<pre>
mpx.vmhba32:C0:T0:L0
   Device Type: Direct-Access
   Size: 15384 MB
   Display Name: Local USB Direct-Access (mpx.vmhba32:C0:T0:L0)
   Multipath Plugin: NMP
   Console Device: /vmfs/devices/disks/mpx.vmhba32:C0:T0:L0
   Devfs Path: /vmfs/devices/disks/mpx.vmhba32:C0:T0:L0
   Vendor: UFD 2.0   Model: Silicon-Power16G  Revis: 1100
   SCSI Level: 2  Is Pseudo: false Status: on
   Is RDM Capable: false Is Removable: true
   Is Local: true  Is SSD: false
   Other Names:
      vml.0000000000766d68626133323a303a30
   VAAI Status: unsupported
naa.6c81f660da379a001a6af66e16dd122d
   Device Type: Direct-Access
   Size: 1887435 MB
   Display Name: Local DELL Disk (naa.6c81f660da379a001a6af66e16dd122d)
   Multipath Plugin: NMP
   Console Device: /vmfs/devices/disks/naa.6c81f660da379a001a6af66e16dd122d
   Devfs Path: /vmfs/devices/disks/naa.6c81f660da379a001a6af66e16dd122d
   Vendor: DELL      Model: PERC H700         Revis: 2.10
   SCSI Level: 5  Is Pseudo: false Status: on
   Is RDM Capable: false Is Removable: false
   Is Local: true  Is SSD: false
   Other Names:
      vml.02000000006c81f660da379a001a6af66e16dd122d504552432048
   VAAI Status: unsupported
naa.6c81f660da379a001a6af67a1792de5b
   Device Type: Direct-Access
   Size: 1887435 MB
   Display Name: Local DELL Disk (naa.6c81f660da379a001a6af67a1792de5b)
   Multipath Plugin: NMP
   Console Device: /vmfs/devices/disks/naa.6c81f660da379a001a6af67a1792de5b
   Devfs Path: /vmfs/devices/disks/naa.6c81f660da379a001a6af67a1792de5b
   Vendor: DELL      Model: PERC H700         Revis: 2.10
   SCSI Level: 5  Is Pseudo: false Status: on
   Is RDM Capable: false Is Removable: false
   Is Local: true  Is SSD: false
   Other Names:
      vml.02000000006c81f660da379a001a6af67a1792de5b504552432048
   VAAI Status: unsupported
naa.6c81f660da379a001a6af68518395ae6
   Device Type: Direct-Access
   Size: 227560 MB
   Display Name: Local DELL Disk (naa.6c81f660da379a001a6af68518395ae6)
   Multipath Plugin: NMP
   Console Device: /vmfs/devices/disks/naa.6c81f660da379a001a6af68518395ae6
   Devfs Path: /vmfs/devices/disks/naa.6c81f660da379a001a6af68518395ae6
   Vendor: DELL      Model: PERC H700         Revis: 2.10
   SCSI Level: 5  Is Pseudo: false Status: on
   Is RDM Capable: false Is Removable: false
   Is Local: true  Is SSD: false
   Other Names:
      vml.02000000006c81f660da379a001a6af68518395ae6504552432048
   VAAI Status: unsupported
t10.DP______BACKPLANE000000
   Device Type: Enclosure Svc Dev
   Size: 0 MB
   Display Name: Local DP Enclosure Svc Dev (t10.DP______BACKPLANE000000)
   Multipath Plugin: NMP
   Console Device: /vmfs/devices/genscsi/t10.DP______BACKPLANE000000
   Devfs Path: /vmfs/devices/genscsi/t10.DP______BACKPLANE000000
   Vendor: DP        Model: BACKPLANE         Revis: 1.09
   SCSI Level: 5  Is Pseudo: false Status: on
   Is RDM Capable: false Is Removable: false
   Is Local: true  Is SSD: false
   Other Names:
      vml.000d000000766d686261303a33323a30
   VAAI Status: unsupported
</pre>
df查看
<pre>
 df -h
</pre>
显示
<pre>
Filesystem   Size   Used Available Use% Mounted on
VMFS-5       1.8T 981.0M      1.8T   0% /vmfs/volumes/v0600
VMFS-5       1.8T 981.0M      1.8T   0% /vmfs/volumes/v0601
VMFS-5     222.0G 973.0M    221.0G   0% /vmfs/volumes/v0602
vfat       249.7M 157.0M     92.7M  63% /vmfs/volumes/03e68ded-e1a329d1-e711-fa3b07dc6118
vfat       249.7M   8.0K    249.7M   0% /vmfs/volumes/79a4e3ed-ffc3f852-8a61-60bb9b2a0b6d
vfat       285.8M 191.3M     94.6M  67% /vmfs/volumes/530f21e4-59212e52-91b1-c81f66e0e7bd
</pre>

