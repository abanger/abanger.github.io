---
layout: post
title: centos6安装语言包
date: 2012-03-26 11:16
author: abanger
comments: true
categories: CentOS
---
从安装盘进行安装，找到对应rpm包：

fonts-chinese-3.02-9.6.el5.noarch.rpm
fonts-ISO8859-2-75dpi-1.0-17.1.noarch.rpm

可以通过yum进行安装，安装办法为：
#yum groupinstall &lt;language&gt;-support

在上面的命令中，&lt;language&gt; 是下列之一: assamese, bengali, chinese, gujarati, hindi, japanese,
kannada, korean, malayalam, marathi, oriya, punjabi, sinhala, tamil, thai, 或 telegu。
