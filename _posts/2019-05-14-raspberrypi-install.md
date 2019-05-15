---
layout: post
title:  "raspberrypi install"
date:   2019-05-14  21:00:00 +0800
categories: pi
---

## 安装
详细过程网上很多可参考，不一一介绍。
- 树莓派3B+主板，无显示器
- 下载2018-11-13-raspbian-stretch-full.zip
- 用Win32DiskImager软件烧完镜像


## 配置

- 配置共享网络连接WiFi
- Putty连接树莓派(默认情况下用户名为 pi ，密码为 raspberry )
- 升级源 
```
#备份源文件
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
sudo cp /etc/apt/sources.list.d/raspi.list /etc/apt/sources.list.d/raspi.list.bak
sudo nano /etc/apt/sources.list
```
使用 # 注释掉原文件内容，使用以下内容取代
```
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi
```
- 更换系统更新源 (/etc/apt/sources.d/raspi.list)
```
sudo nano /etc/apt/sources.list.d/raspi.list
```
使用 # 注释掉原文件内容，使用以下内容取代  
```
deb http://mirror.tuna.tsinghua.edu.cn/raspberrypi/ stretch main ui  
deb-src http://mirror.tuna.tsinghua.edu.cn/raspberrypi/ stretch main ui  
```

- 升级系统
```
sudo apt-get upgrade
```

## 参考 
- [树莓派3B+无显示器配置详细教程](https://blog.csdn.net/Jack_Lue/article/details/82912327)

