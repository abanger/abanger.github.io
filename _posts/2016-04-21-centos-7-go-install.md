---
layout: post
title: Centos 7 go入门--安装和配置
date: 2016-04-21 10:54
author: abanger
comments: true
categories: go
---
1. 下载go1.6.1
<pre>
curl -O http://golangtc.com/static/go/1.6.1/go1.6.1.linux-amd64.tar.gz
</pre>
解压到/usr/local/go

2. 环境变量
<pre>
mkdir /home/goapps
mkdir /home/goapps/bin
mkdir /home/goapps/pkg
mkdir /home/goapps/src

vi /root/.bash_profile 
</pre>
<!--more-->


bash_profile添加如下内容
<pre>
export GOROOT=/usr/local/go
export GOPATH=/home/goapps
export GOBIN=/home/goapps/bin
PATH=$PATH:$HOME/bin:$GOROOT/bin:$GOBIN
</pre>

3. go命令基础

1) go run直接执行
通过go run加上要编译的Go源文件名，可直接执行文件
<pre>
go run src.go
</pre>
2) go build 编译
通过go build加上要编译的Go源文件名，可得到一个可执行文件，默认源文件名字可输入.go后缀。
<pre>
go build src
</pre>
3) go install编译
go install与build命令相比，install命令在编译源码后还会将可执行文件或库文件安装到约定的目录下。go install将可执行文件安装到与src同级别的bin目录下，bin目录由go install自动创建；可执行文件依赖的各种package编译后，放在与src同级别的pkg目录下。
<pre>
go build src.go
</pre>

