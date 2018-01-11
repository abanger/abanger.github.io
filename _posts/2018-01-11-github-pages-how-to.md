---
layout: post
title:  "Github Pages建独立博客"
date:   2018-01-11 15:06:02 +0800
categories: maintenance
---


Github提供[Github Pages](https://pages.github.com/)服务，不仅可以方便的为项目建立介绍站点，也可以用来建立个人博客。大概需要以下几步。

1. 在Github创建资料库

2. 本地克隆资料库
    <pre>
    git clone https://github.com/username/username.github.io
    </pre>

3. 创建个人网站
    直接使用html建设个人网站，也可以使用jekyll创建，jekyll参考本博前一篇[
Welcome to Jekyll!](/maintenance/2018/01/09/welcome-to-jekyll.html)。

4. 提交到Github
    <pre>
    git add --all
    git commit -m "Initial commit"
    git push -u origin master
    </pre>
5. 通过浏览器访问：username.github.io。


参考：
[Github Pages](https://pages.github.com/)
[用 Github + Jekyll 写博客](http://blog.csdn.net/u014015972/article/details/50497254)