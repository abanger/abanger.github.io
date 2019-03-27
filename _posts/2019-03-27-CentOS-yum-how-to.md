---
layout: post
title:  "CentOS7配置Yum源"
date:   2019-03-27 9:29:00 +0800
categories: CentOSbook
---




## CentOS7配置本地Yum源

CentOS7安装的DVD有大部分常用软件，通过配置DVD为本地Yum源，可以在没有网络的情况下安装软件。下面使用虚拟机挂载DVD的iso文件，空间足够也可以直接拷贝到系统，然后修改yum源配置。

```
mkdir /media/CentOS
mount -t auto /dev/cdrom /media/CentOS
```

### 修改yum源配置
备份你的原镜像文件CentOS-Base.repo，以免出错后可以恢复。
```
cd /etc/yum.repos.d/
cp CentOS-Base.repo CentOS-Base.repo.bak
```
修改CentOS-Base.repo的内容，注释文档中的所有mirrorlist属性，把baseurl属性打开注释，并设置baseurl的属性值为挂载点
baseurl=file:///media/CentOS/

或直接配置一个本地repo的配置文件CentOS-Media.repo
```
#vi CentOS-Media.repo
[C7-media]
name=CentOS-$releasever - Media
baseurl=file:///media/CentOS/
gpgcheck=0
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

```


###清空yum已存在的所有源信息
```
yum clean all
```
### 查看本地源的所有软件
```
yum list
```

注：如果想要恢复默认的源，把CentOS-Base.repo.bak重命名为CentOS-Base.repo就可以了；系统重启之后，需要再次手动挂载，可以设置/media/CentOS自动挂载。


## CentOS7配置国内yum源

阿里yum源配置文件http://mirrors.aliyun.com/repo/Centos-7.repo
163yum源的配置文件http://mirrors.163.com/.help/CentOS7-Base-163.repo

### 备份你的原镜像文件，以免出错后可以恢复   
```
cd /etc/yum.repos.d/
mv CentOS-Base.repo CentOS-Base.repo.bak
```

### 下载yum源配置文件

```
curl http://mirrors.aliyun.com/repo/Centos-7.repo > CentOS-Base.repo
```

### 清楚原有yum缓存
```
yum clean all
yum makecache ##生成缓存

```

## 安装软件
```
yum install net-tools
```

## 常用yum命令

### 显示所有仓库
```
yum repolist all
```

### 显示应用程序包
```
yum list 
#支持通配符格式的查询
```

### 显示可更新或已安装的程序包
```
yum list updates
yum list installed
```
### 显示仓库中最近增加的程序包
```
yum list recent
```
### 安装程序包
```
#安装net-tools程序
yum install net-tools
```

### 卸载程序包
```
yum remove net-tools
#或
yum erase net-tools
```

### 搜索
```
yum search net-tools
```
### 查看指定包所依赖的capabilities
```
yum deplist net-tools
```


### 查看yum事务历史
```
yum history
```
### 包组相关的命令
```
yum groupinstall    # 安装包组
yum groupupdate     #更新包组
yum grouplist       #显示包组
yum groupremove     #移除包组
yum groupinfo       #查看包组信息
yum groupinstall 'Minimal Install'
```

## 修订  
- 2019-03-27 15:29:00
