---
layout: post
title: CentOS 7启动出错一例
date: 2017-04-10 14:43
author: abanger
comments: true
categories: CentOS
---
<h2>CentOS 7 启动出现如下错误</h2>

<pre><code>not syncing:VFS: Unable to mount root fs on unknown，详见下面附图。
</code></pre>

<h2>原因</h2>

<pre><code>初步考虑是是更新了内核3.10.0-514.6.1.el7后出现的问题。
</code></pre>

<h2>处理</h2>

<ol>
<li>开机启动时选择旧版本的内核启动</li>
<li>成功进入系统后改变开机默认选择的内核
<!--more--></li>
</ol>

<ul>
<li>查看可选内核</li>
</ul>

<code>[root@lab grub2]# cat /boot/grub2/grub.cfg | grep 3.10.0-327.28.3
menuentry 'CentOS Linux (3.10.0-327.28.3.el7.x86_64) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-3.10.0-123.el7.x86_64-advanced-193aab79-d53d-4f9a-b122-23fde7f637d3' {
        linux16 /vmlinuz-3.10.0-327.28.3.el7.x86_64 root=/dev/mapper/centos_nic-root ro rd.lvm.lv=centos_nic/root crashkernel=auto  vconsole.font=latarcyrheb-sun16 vconsole.keymap=us rd.lvm.lv=centos_nic/swap rhgb quiet LANG=en_US.UTF-8
        initrd16 /initramfs-3.10.0-327.28.3.el7.x86_64.img</code>

<ul>
<li>设置默认启动内核</li>
</ul>

<code>[root@lab grub2]# grub2-set-default 'CentOS Linux (3.10.0-327.28.3.el7.x86_64) 7 (Core)'</code>

<ul>
<li>显示设置默认启动内核</li>
</ul>

<code>[root@lab grub2]# grub2-editenv list
saved_entry=CentOS Linux (3.10.0-327.28.3.el7.x86_64) 7 (Core)</code>

end

<a href="http://aba.qzu.cn/vmwareerror20170408192523"><img src="http://aba.qzu.cn/wp-content/uploads/2017/04/vmwareerror20170408192523.png" alt="" width="736" height="422" class="aligncenter size-full wp-image-1178" /></a>
