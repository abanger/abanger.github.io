---
layout: post
title:  "Welcome to Jekyll!"
date:   2018-01-09 09:06:02 +0800
categories: maintenance
---

jekyll是一个基于ruby的博客系统，本文简要介绍其安装过程。
主要有以下步骤：
1. 安装 Ruby
2. 安装 DevKit
3. 安装 Jekyll
4. 修改配置

### 1. 安装 Ruby和DevKit

略...

下载[Ruby](http://rubyinstaller.org/downloads/ )

直接下载[Ruby+Devkit 2.4.4-1 (x64) ](https://github.com/oneclick/rubyinstaller2/releases/download/rubyinstaller-2.4.4-1/rubyinstaller-devkit-2.4.4-1-x64.exe )
2018-04-25

### 2. 安装 Jekyll

1. gem update,bundle install, bundle update
2. gem install jekyll

### 3. 配置 Jekyll

1. (Optional) Create a new Jekyll site: jekyll new mysite
2. Replace the current theme in your Gemfile with gem "jekyll-theme-XXX".
3. Install the theme: bundle install
4. Replace the current theme in your _config.yml file with theme: jekyll-theme-XXX.
5. Build your site: bundle exec jekyll serve


### 4.修改配置
1. 修改_config.yml中的信息。
2. 修改about/index.html中个人信息。
3. 修改_include/文件夹相关内容(主页, 应用, 标签, 关于等)。

### 5. 运行

bundle exec jekyll serve

#### 参考：
[clean blog](https://github.com/BlackrockDigital/startbootstrap-clean-blog-jekyll)

[Jekyll搭建个人博客](https://segmentfault.com/a/1190000007243257)


[Gem Source](https://ruby.taobao.org/) 更换
```
gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/

bundle config mirror.https://rubygems.org https://gems.ruby-china.org
```

修改：2018-04-25 20:39