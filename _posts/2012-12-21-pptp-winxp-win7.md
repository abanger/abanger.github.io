---
layout: post
title: pptp winxp win7拨号批处理
date: 2012-12-21 21:26
author: abanger
comments: true
categories: maintenance
---
pptp winxp win7拨号批处理


WinXP
<pre>
route delete 210.34.120.0
route delete 219.229.64.0
rem 前两行是防止上次拨号不成功
rem rasdial qzuvpn user password
rem 用批处理自动拨号，注释这行得先拨后再运行这批处理
ipconfig|find /i "210.34.121">"%temp%\vpn.txt"
set /p ip=< "%temp%\vpn.txt"
set ip=%ip:. =%
set ip=%ip: =%
set ip=%ip::=%
set ip=%ip:IPaddress=%
del /f /s /q "%temp%\vpn.txt">nul2>nul
route delete 0.0.0.0 mask 0.0.0.0 %ip%
route add 210.34.120.0 mask 255.255.248.0 %ip%
route add 219.229.64.0 mask 255.255.240.0 %ip%
rem pause
</pre> 

Win7
<pre>
@echo off
route delete 210.34.120.0
route delete 219.229.64.0
rasdial qzuvpn user password
rem 用批处理自动拨号，注释这行得先拨后再运行这批处理
if exist ipconfig.txt del ipconfig.txt
ipconfig /all >ipconfig.txt

if exist IPAddr.txt del IPaddr.txt
rem find "IPv4" ipconfig.txt >IPAddr.txt
find "210.34.121" ipconfig.txt >IPAddr.txt
for /f "skip=2 tokens=16" %%I in (IPAddr.txt) do echo %%I>IP.txt

for /f "delims=(首选) tokens=1,2 " %%I in  (IP.txt) do  set IP=%%I

rem route delete  %IP%
route ADD 210.34.120.0 MASK 255.255.248.0  %IP%
route add 219.229.64.0 mask 255.255.240.0 %IP%
exit
</pre>

