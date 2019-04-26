# CentOS 7 Apache httpd 管理



## Apache httpd的配置信息和目录

Apache httpd的配置信息和目录

- 主配置目录： /etc/httpd/conf
- 主配置文件： /etc/httpd/conf/httpd.conf
- 子配置目录： /etc/httpd/conf.d
- 子配置文件： /etc/httpd/conf.d/*.conf
- 默认发布目录 ： /var/www/html
- 默认发布文件： index.html
- 默认端口：  80
- 默认安全上下文： httpd_sys_content_t
- 程序开启默认用户： apache
- httpd日志目录 ： /var/logs/httpd/*




## 主配置文件

httpd默认的配置文件目录为/etc/httpd/；主配置文件是/etc/httpd/conf/httpd.conf；配置存储在的/etc/httpd/conf.d/目录。

### 主配置文件

主配置文件httpd.conf里的主要配置项。

```
#服务器根目录
ServerRoot "/etc/httpd"

#端口
#Listen 10.41.10.110:80
Listen 80

#域名+端口来标识服务器，没有域名用ip也可以
#ServerName www.example.com:80

#不许访问根目录
<Directory />
    AllowOverride none
    Require all denied
</Directory>

# 文档目录
DocumentRoot "/var/www/html"

# 对 /var/www目录访问限制
<Directory "/var/www">
    AllowOverride None
    # Allow open access:
    Require all granted
</Directory>

# 对/var/www/html目录访问限制
<Directory "/var/www/html">
　　 Options Indexes FollowSymLinks
　　 AllowOverride None
 　　Require all granted
</Directory>

# 默认编码
AddDefaultCharset UTF-8

#EnableMMAP off
EnableSendfile on

# include进来其它配置文件
IncludeOptional conf.d/*.conf
```


## 修改默认端口  

```
#vi /etc/httpd/conf/httpd.conf
Linsten 81 #修改默认80端口为81
```

## 修改目录访问限制
```
#vi /etc/httpd/conf/httpd.conf
<Directory "/var/www/html">
　　 Options Indexes FollowSymLinks
　　 AllowOverride None
 　　Require ip 10.41.10
</Directory>
##systemctl restart httpd
```
日志
```
[Fri Apr 19 13:22:56.011836 2019] [authz_core:error] [pid 17547] [client 10.41.10.59:64181] AH01630: client denied by server configuration: /var/www/html/noindex, referer: http://10.41.10.110/noindex/css/open-sans.css

```


## 基于域名的虚拟主机（选修）

创建虚拟主机配置文件
```
# vi /etc/httpd/conf.d/v1.conf
<VirtualHost *:80>
	ServerName qzulab.cn
	DocumentRoot "/www/qzulab/"
</VirtualHost>
##systemctl restart httpd
```


## 作为反向代理（选修）

参考：[cenots 7 httpd 安装及安全加固](https://abanger.github.io/maintenance/2018/06/08/centos-7-httpd-security-reinforcement.html)





## 注：

### Require
Require all granted
无条件允许访问。
Require all denied
访问被无条件拒绝。
Require env env-var [env-var] ...
只有在给定的环境变量之一被设置的情况下才允许访问。
Require method http-method [http-method] ...
只有给定的HTTP方法才允许访问。
Require expr expression
如果表达式计算结果为true，则允许访问。
Require user userid [userid] ...
只有指定的用户才能访问资源。
Require group group-name [group-name] ...
只有指定组中的用户才能访问资源。
Require valid-user
所有有效的用户都可以访问资源。
Require ip 10.41.10
指定IP地址范围内的客户端可以访问资源。

### Options
None:不支持任何选项
Indexes：允许索引目录
FollowSymLinks：允许访问符号链接指向的原文件
Includes：允许执行服务端包含（SSI）
ExecCGI：允许允许CGI脚本
ALL：支持所有选项


