---
layout: post
title: linux日常检查
date: 2012-05-28 12:26
author: abanger
comments: true
categories: maintenance
tags: CentOS
---
<div>1/检查安全日志</div>
<div></div>
<div>less /var/log/secure</div>
<div>Accepted password for root from 123.456.78.90 port 63445 ssh2</div>
<div>搜索“Accepted password for”，看有哪些用户和IP登录过服务器</div>
<div></div>
<div>who /var/log/wtmp  查看谁登录，没有没异常</div>
<div></div>
<div><!--more--></div>
<div>last 和who类似</div>
<div>单独执行last指令，它会读取位于/var/log目录下，名称为wtmp的文件，并把该给文件的内容记录的登入系统的用户名单全部显示出来。</div>
<div></div>
<div><!--more--></div>
<div></div>
<div>history</div>
<div>    history</div>
<div>命令能够保存最近所执行的命令。如果是root命令所保存的命令内容在/root/.bash_history文件中，如果是普通用户，操作所命令保存在这个用户的所属目录下，即一般的/home/username/.bash_history。这个history的保存值可以设置，编辑/etc/profile文件，其中的HISTSIZE=1000的值就是history保存的值。</div>
<div></div>
<div></div>
<div>less /var/log/messages 记录输出到系统主控台</div>
<div>messages 日志是核心系统日志文件。它包含了系统启动时的引导消息，以及系统运行时的其他状态消息。IO 错误、网络错误和其他系统错误都会记录到这个文件中。其他信息，比如某个人的身份切换为 root，也在这里列出。如果服务正在运行，比如 DHCP 服务器，您可以在 messages 文件中观察它的活动。通常，/var/log/messages 是您在做故障诊断时首先要查看的文件。</div>
<div></div>
<div></div>
<div>less /var/log/httpd/*.error 检查出错信息，非法访问和配置出错信息</div>
<div></div>
<div></div>
<div></div>
<div></div>
<div>2/ less /etc/passwd      检查用户列表</div>
<div>查看是否有陌生的用户帐号</div>
<div></div>
<div>3/ netstat -anp | more     检查网络状态</div>
<div></div>
<div>重点查看Local Address和Foreign Address是否有异常</div>
<div></div>
<div></div>
<div></div>
<div></div>
<div>4/ 查看异常进程</div>
<div></div>
<div>ps  -ef</div>
<div></div>
<div></div>
<div>5/ 查看定时器</div>
<div></div>
<div>crontab -l</div>
<div></div>
<div>/etc/cron.*   目录下文件</div>
<div></div>
<div></div>
<div></div>
<div></div>
<div></div>
<div>6/</div>
<div>查防火墙</div>
<div>iptables -L</div>
<div></div>
<div></div>
<div>7/ 检查软件更新</div>
<div></div>
<div>yum update 更新时候要注意系统兼容性，如有安装其他应用软件就要小心更新</div>
<div></div>
<div></div>
<div></div>
<div>Linux 日志存储在 /var/log 目录中。这里有几个由系统维护的日志文件，但其他服务和程序也可能会把它们的日志放在这里。大多数日志只有 root 才可以读，不过只需要修改文件的访问权限就可以让其他人可读。</div>
<div>在/var/log 目录下有一些文件以一个数字结尾，这些是已轮循的归档文件。日志文件会变得特别大，特别笨重。Linux 提供了一个命令来轮循这些日志，以使您的当前日志信息不会淹没在旧的无关信息之中。 logrotate 通常是定时自动运行的，但是也可以手工运行。当执行后，logrotate 将取得当前版本的日志文件，然后在这个文件名最后附加一个“.1”。其他更早轮循的文件为“.2”、“.3”，依次类推。文件名后的数字越大，日志就越老。</div>
<div>可以通过编辑 /etc/logrotate.conf 文件来配置 logrotate 的自动行为。通过 man logrotate 来学习 logrotate 的全部细节。</div>
<div>其中：</div>
<div># rotate log files weekly</div>
<div>weekly</div>
<div>这里代表每个日志文件是每个星期循环一次，一个日志文件保存一个星期的内容。</div>
<div># keep 4 weeks worth of backlogs</div>
<div>rotate 4</div>
<div>这里代表日志循环的次数是4次，即可以保存4个日志文件。</div>
<div></div>
<div>以下是常用的系统日志文件名称及其描述：</div>
<div>lastlog 记录用户最后一次成功登录时间</div>
<div>loginlog 不良的登陆尝试记录</div>
<div>messages 记录输出到系统主控台以及由syslog系统服务程序产生的消息</div>
<div>utmp 记录当前登录的每个用户</div>
<div>utmpx 扩展的utmp</div>
<div>wtmp 记录每一次用户登录和注销的历史信息 wtmpx 扩展的wtmp</div>
<div>vold.log 记录使用外部介质出现的错误</div>
<div>xferkig 记录Ftp的存取情况 sulog 记录su命令的使用情况</div>
<div>acct 记录每个用户使用过的命令</div>
<div>aculog 拨出自动呼叫记录</div>
