---
layout: post
title:  "树莓派串口通信配置"
date:   2021/06/20  1:16:00 +0800
categories:  pi
---


环境：树莓派4B 4G，ARMv7 Processor rev 3 (v7l)，Raspberry Pi 4 Model B Rev 1.4，2021-05-07-raspios-buster-armhf-full.zip，32位。

树莓派串口AMA0是外部晶振驱动的，稳定性高，本文将使用用AMA0串口。




## 更改串口映射

- 1. 配置交换映射关系

要把硬件串口ttyAMA0映射到GPIO14，15上(08,10脚)
命令行输入：
```
sudo raspi-config
```
选择第五个选项,选中P6敲回车键进入，选择是否关闭serial login：选关闭，打开串口调试：选打开

图暂略...

- 2. 修改配置config.txt文件

在/boot目录下找到config.txt文件，在最后添加一行内容如下
sudo nano/boot/config.txt
在末尾添加一行代码：
dtoverlay=pi3-miniuart-bt


```
#sudo nano /boot/config.txt
enable_uart=1
dtoverlay=pi3-miniuart-bt

```
重启设备。

- 3.查看串口的情况
```
ls /dev/serial0 -al
#lrwxrwxrwx 1 root root 7 Jun 19 15:13 /dev/serial0 -> ttyAMA0
```

注：有文章需要更改/boot/cmdline.txt，本文并没有更改。

## 测试

- 安装串口调试助手

```
sudo apt-get install minicom
#minicom -D /dev/ttyAMA0 -b 9600 
```

## 树莓派与arduino串口通信
树莓派与arduino都有一个串行口，都是TTL电平，可以直接连接。不同的是树莓派是5V供电，而arduino是3.3V供电。

- Arduino Uno R3开发板上，硬件串口位于Rx(0)和Tx(1)

- 连接方式：
```
树莓派的10 RX       －－－－－      arduino的01 TX
树莓派的08 TX       －－－－－      arduino的00 RX
树莓派09 的GND      －－－－－      arduino的14  GND
```

arduino：usb口	下载线连接到电脑，可以监视arduino串口通信。


