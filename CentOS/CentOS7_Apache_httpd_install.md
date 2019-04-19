# CentOS 7 Apache httpd安装


Apache在CentOS中，其实叫“httpd”，就是Apache HTTP Server，可以使用yum来安装。


## 安装httpd服务

- 安装
```
yum install httpd -y
```
安装httpd服务同时会安装其依赖的安装包。

- 设置httpd服务开机启动
```
systemctl enable httpd.service  #systemctl enable httpd
#Note: Forwarding request to 'systemctl enable httpd.service'.
#ln -s '/usr/lib/systemd/system/httpd.service' '/etc/systemd/system/multi-user.target.wants/httpd.service'
```
- 启动httpd服务
```
systemctl start  httpd.service
```
- 测试访问
打开浏览器，访问ip验证一下，成功！
若不能访问检查主机防火墙是否允许访问80端口。

可以通过ftp或ssh把网页放到/var/www/html目录里，就可以访问自己设计的网站。



## 配置

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




https://www.linuxidc.com/Linux/2015-02/113921.htm