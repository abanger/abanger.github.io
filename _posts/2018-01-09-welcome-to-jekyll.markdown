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

### 1. 安装 Ruby

略...
### 2. 安装 DevKit

略...
### 3. 安装 Jekyll
1. (Optional) Create a new Jekyll site: jekyll new mysite
2. Replace the current theme in your Gemfile with gem "jekyll-theme-XXX".
3. Install the theme: bundle install
4. Replace the current theme in your _config.yml file with theme: jekyll-theme-XXX.
5. Build your site: bundle exec jekyll serve


### 修改配置
1. 修改_config.yml中的信息。
2. 修改about/index.html中个人信息。
3. 修改_include/文件夹相关内容(主页, 应用, 标签, 关于等)。


参考：
[clean blog](https://github.com/BlackrockDigital/startbootstrap-clean-blog-jekyll)