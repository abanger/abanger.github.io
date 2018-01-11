---
layout: post
title: centos改用163镜像的配置
date: 2012-02-16 15:34
author: abanger
comments: true
categories: CentOS
---
centos改用163镜像的配置

<a href="http://aba.qzu.cn/wp-content/uploads/2012/02/CentOS-Base.repo_.txt">CentOS-Base.repo</a>

系统安装后请按照以下步骤修改CenOS-Base.repo,以后就可以方便的用yum安装软件了.
<pre>
cd /etc/yum.repos.d

mv CentOS-Base.repo CentOS-Base.repo.save.6

curl  http://aba.qzu.cn/wp-content/uploads/2012/02/CentOS-Base.repo_.txt  &gt; CentOS-Base.repo.6

mv CentOS-Base.repo.6 CentOS-Base.repo
</pre>
