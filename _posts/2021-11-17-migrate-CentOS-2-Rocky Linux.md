---
layout: post
title:  "迁移 CentOS 8 到 Rocky Linux"
date:   2021/11/17  16:22:00 +0800
categories:  linuxbook
---

## 数据备份以及系统快照
系统级操作都需要做好数据备份。

## CentOS 8更新到最新版本
更新，检查当前版本后重启系统。清除因其他工作添加源/etc/yum.repos.d/。
```
dnf update -y
reboot
cat /etc/redhat-release
#CentOS Linux release 8.5.2111
```

## 运行迁移脚本 migrate2rocky.sh
下载[Rocky Tools](https://github.com/rocky-linux/rocky-tools) migrate2rocky.sh迁移工具,运行并重启,选择安装所有内容。
```
curl  -o https://raw.githubusercontent.com/rocky-linux/rocky-tools/main/migrate2rocky/migrate2rocky.sh  migrate2rocky.sh
chmod +x migrate2rocky.sh
./migrate2rocky.sh -r
reboot
```
### 运行migrate2rocky.sh 部分日志
```
(base) [root@lab22 migrate2rocky]# ./migrate2rocky.sh -r

Removing dnf cache
Preparing to migrate CentOS Linux 8 to Rocky Linux 8.

Determining repository names for CentOS Linux 8.....

Found the following repositories which map from CentOS Linux 8 to Rocky Linux 8:
CentOS Linux 8  Rocky Linux 8
appstream       appstream
baseos          baseos
extras          extras
...
...
...
Installed:
  rocky-gpg-keys-8.5-1.el8.noarch      rocky-logos-httpd-85.0-3.el8.noarch
  rocky-release-8.5-1.el8.noarch       rocky-repos-8.5-1.el8.noarch
Removed:
  centos-gpg-keys-1:8-3.el8.noarch   centos-linux-release-8.5-1.2111.el8.noarch
  centos-linux-repos-8-3.el8.noarch  centos-logos-httpd-85.8-2.el8.noarch

Complete!
Last metadata expiration check: 0:00:05 ago on Wed Nov 17 04:19:54 2021.
...

```


### 查看迁移后版本
检查是否一切正常的迁移。
```
hostnamectl 
cat /etc/os-release

```





## 更新到国内升级源

```
sed -e 's|^mirrorlist=|#mirrorlist=|g' \
    -e 's|^#baseurl=http://dl.rockylinux.org/$contentdir|baseurl=https://mirrors.aliyun.com/rockylinux|g' \
    -i.bak \
    /etc/yum.repos.d/Rocky-*.repo

dnf makecache

dnf update -y #更新

```

## 参考
- [如何从Centos、Alma Linux、RHEL或Oracle Linux迁移到Rocky Linux](https://docs.rockylinux.org/zh/guides/migrate2rocky/)
- [Rocky Tools](https://github.com/rocky-linux/rocky-tools)
- [rockylinux镜像](https://developer.aliyun.com/mirror/rockylinux)