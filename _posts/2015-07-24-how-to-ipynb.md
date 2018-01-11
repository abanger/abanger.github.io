---
layout: post
title: 如何打开.ipynb文件
date: 2015-07-24 11:23
author: abanger
comments: true
categories: maintenance
---
&nbsp;

IPython Notebook是Web based IPython封装，可以展现富文本，使得整个工作可以以笔记的形式展现、存储，对于交互编程、学习非常方便。
安装Ipython NoteBook，在WIN7、32位,大概步骤如下：
1.安装python
下载python：https://www.python.org/ftp/python/2.7.10/python-2.7.10.msi
默认安装，把安装路径加入path。
<!--more-->


2.命令行下操作：D:\Python27&gt;（步骤1的安装路径）
<pre>
pip install IPython
pip install pyzmq
pip install jinja2
pip install tornado
pip install jsonschema
ipython.exe notebook
</pre>
3. 按照提示上传.ipynb文件，即可在浏览器上阅读与操作
