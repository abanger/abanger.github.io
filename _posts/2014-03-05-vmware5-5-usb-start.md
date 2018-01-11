---
layout: post
title: vmware5.5安装到USB，用USB启动系统
date: 2014-03-05 15:00
author: abanger
comments: true
categories: maintenance
---
vmware5.5安装大概过程如下：制作虚拟化ESXi系统的USB启动盘，安装ESXi系统到USB，用USB启动ESXi系统。比较难理解，下面图解过程。

1. 使用UNetbootin把ESXI安装镜像文件（VMware-VMvisor-Installer-5.5.0-1331820.x86_64.iso,vmware官网下载）写入U盘，设置本U盘为启动盘（称为A盘）

UNetbootin可到<a title="http://unetbootin.sourceforge.net/" href="http://unetbootin.sourceforge.net/">http://unetbootin.sourceforge.net/</a>下载。

<a href="http://aba.qzu.cn/wp-content/uploads/2014/03/vmware20140227075819.jpg">
</a> <a href="http://aba.qzu.cn/wp-content/uploads/2014/03/vmware20140227071000.jpg"><img class="aligncenter size-full wp-image-864" alt="vmware20140227071000" src="http://aba.qzu.cn/wp-content/uploads/2014/03/vmware20140227071000.jpg" width="534" height="392" /></a>

&nbsp;

&nbsp;
<p style="text-align: center;"><a href="http://aba.qzu.cn/wp-content/uploads/2014/03/vmware20140227075819.jpg"><img class="aligncenter" alt="vmware20140227075819" src="http://aba.qzu.cn/wp-content/uploads/2014/03/vmware20140227075819.jpg" width="531" height="392" /></a></p>
&nbsp;

2. A盘（上述ESXI启动盘）安装到服务器的USB盘（称之为B盘）

把A、B盘分别插入服务器USB口，注意插入端口循序，过程如下图。

&nbsp;

<a href="http://aba.qzu.cn/wp-content/uploads/2014/03/IMG_20140227_091204.jpg"><img class="aligncenter size-full wp-image-865" alt="IMG_20140227_091204" src="http://aba.qzu.cn/wp-content/uploads/2014/03/IMG_20140227_091204.jpg" width="913" height="343" /></a>

&nbsp;

2.1 启动光盘后要求选择ESXI安装到服务器的位置，本例是要安装到USB（上述B盘），要上图所示，两个盘大小是一样，只是盘已在第一步安装了esxi启动的系统，所以图中A盘是最后一个（14.44G），B盘（15.82G）是本例要选择安装的位置，光标移至B盘（15.82G），按回车键继续。

&nbsp;

<a href="http://aba.qzu.cn/wp-content/uploads/2014/03/IMG_20140227_091439.jpg"><img class="aligncenter size-full wp-image-866" alt="IMG_20140227_091439" src="http://aba.qzu.cn/wp-content/uploads/2014/03/IMG_20140227_091439.jpg" width="887" height="102" /></a>

2.2 系统开始安装

&nbsp;

<a href="http://aba.qzu.cn/wp-content/uploads/2014/03/IMG_20140227_091547.jpg"><img class="aligncenter size-full wp-image-867" alt="IMG_20140227_091547" src="http://aba.qzu.cn/wp-content/uploads/2014/03/IMG_20140227_091547.jpg" width="754" height="270" /></a>

2.3 确认安装到B盘

&nbsp;

<a href="http://aba.qzu.cn/wp-content/uploads/2014/03/IMG_20140227_091818.jpg"><img class="aligncenter size-full wp-image-868" alt="IMG_20140227_091818" src="http://aba.qzu.cn/wp-content/uploads/2014/03/IMG_20140227_091818.jpg" width="594" height="126" /></a>

2.4 系统安装B盘过程

<a href="http://aba.qzu.cn/wp-content/uploads/2014/03/IMG_20140227_092254.jpg"><img class="aligncenter size-full wp-image-869" alt="IMG_20140227_092254" src="http://aba.qzu.cn/wp-content/uploads/2014/03/IMG_20140227_092254.jpg" width="887" height="414" /></a>

2.5 安装完成，要求重新启动。

&nbsp;

&nbsp;

3. 安装完成之后配置一下服务器管理IP即可。

<a href="http://aba.qzu.cn/wp-content/uploads/2014/03/IMG_20140227_093222.jpg"><img class="aligncenter size-full wp-image-871" alt="IMG_20140227_093222" src="http://aba.qzu.cn/wp-content/uploads/2014/03/IMG_20140227_093222.jpg" width="931" height="400" /></a> <a href="http://aba.qzu.cn/wp-content/uploads/2014/03/IMG_20140227_093204.jpg"><img class="aligncenter size-full wp-image-872" alt="IMG_20140227_093204" src="http://aba.qzu.cn/wp-content/uploads/2014/03/IMG_20140227_093204.jpg" width="990" height="427" /></a>

&nbsp;

<a href="http://aba.qzu.cn/wp-content/uploads/2014/03/IMG_20140227_093132.jpg"><img class="aligncenter size-full wp-image-870" alt="IMG_20140227_093132" src="http://aba.qzu.cn/wp-content/uploads/2014/03/IMG_20140227_093132.jpg" width="550" height="511" /></a>

&nbsp;

结束。

参考：<a href="http://aba.qzu.cn/archives/419.html">vmware5.0.0安装到USB，用USB启动系统</a>

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;
