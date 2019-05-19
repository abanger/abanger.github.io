---
layout: post
title:  "raspberrypi install ps3 Eye mics"
date:   2019-05-19  21:31:00 +0800
categories: pi
---

## 安装硬件
详细过程网上很多可参考，不一一介绍。PS3 Eye 是 USB 口，可以即插即用。
- 树莓派3B+主板+USB ps3



## 配置
- 根据参考2，配置安装驱动。
- 检查硬件：arecord -l
```
**** List of CAPTURE Hardware Devices ****
card 1: CameraB409241 [USB Camera-B4.09.24.1], device 0: USB Audio [USB Audio]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
```
- 调整系统配置文件
编辑声音处理的配置文件asound.conf。
```
##/etc/asound.conf

# use samplerate to resample as speexdsp resample is bad
defaults.pcm.rate_converter "samplerate"

pcm.!default {
    type asym
    playback.pcm "playback"
    capture.pcm "capture"
}

pcm.playback {
    type plug
    slave.pcm "hw:0,0"
}

pcm.capture {
    type plug
    slave.pcm "hw:1,0"
}

pcm.dmixed {
    type dmix
    slave.pcm "hw:1,0"
}
```


## 待续

## 参考 
1. [麦克风选购建议](https://wukong.hahack.com/#/mic-choices?id=respeaker-2-mics-pi-hat)
2. [Making the Playstation Eye’s microphones work on Linux](https://renatocunha.com/2012/04/playstation-eye-audio-linux/)
