---
layout: post
title:  "cenots 7  httpd 安装及安全加固"
date:   2018-06-08 17:07:00 +0800
categories: maintenance
---

cenots 7  httpd 安装及安全加固  

环境为CentOS Linux release 7.5.1804 (Core)。  主要目的作为代理服务器，其他目的请参考调整。
## httpd安装及其环境配置
```
yum update -y
yum install httpd 
#允许http连接
getsebool -a | grep httpd
setsebool -P httpd_can_network_connect=1
systemctl enable  httpd
systemctl restart httpd
#防火墙打开80端口
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --reload

```

## proxy配置
```
#vi /etc/httpd/conf.d/proxy.conf 
<VirtualHost *:80>
    ServerAdmin nic@qzlab.it
    ServerName www.qzlab.it
    <Location />
        Order allow,deny
        Allow from all
        ProxyPass  http://10.0.10.2/  retry=3
        ProxyPassReverse http://10.0.10.2/
    </Location>    
    ErrorLog logs/qzlab-error_log
    CustomLog logs/qzlab-access_log combined
</VirtualHost>
```

## 检查加载和安装模块
```
#检查加载模块
httpd -M
#检查安装模块
ls /usr/lib64/httpd/modules/
#检查编译的MPM模块
httpd -l
```

## httpd服务器配置调整  

### 安全配置
```
#vi /etc/httpd/conf/httpd.conf
#设置目录禁止访问,将Options Indexes FollowSymLinks改为
Options  FollowSymLinks
##在文件最后一行加上
#防被作为代理
ProxyRequests off
#禁用TRACE Method防范XSS跨站攻击
TraceEnable off
Header always append X-Frame-Options DENY
#出错页不显示服务器操作系统的名称
ServerTokens prod 
#关闭apahce版本信息
ServerSignature Off 
#服务器断开与客户机的连接的超时间隔
Timeout 60
#一次连接可以进行的HTTP请求的最大请求次数
MaxKeepAliveRequests 100
```

### 错误提示页面
应该定义自己的错误页面，避免由于运行出错时泄露敏感信息
```
ErrorDocument         401           /custom401.html
ErrorDocument         404           /custom404.html
ErrorDocument         500           /custom500.html
```


### 删除无用CGI配置
```
##<Directory "/var/www/cgi-bin">
##    AllowOverride None
##    Options None
##    Require all granted
##</Directory>
```

### 删除无用模块
1. 删除无用配置文件  
详见/etc/httpd/conf.modules.d，根据实际需要调整。本文只保留如下四个文件。
```
00-base.conf
00-mpm.conf
00-proxy.conf
00-systemd.conf
```

2. 删除配置文件内没用到的模块  
以00-proxy.conf为例如下：  
```
## vi /etc/httpd/conf.modules.d/00-proxy.conf 
# This file configures all the proxy modules:
LoadModule proxy_module modules/mod_proxy.so
##LoadModule lbmethod_bybusyness_module modules/mod_lbmethod_bybusyness.so
##LoadModule lbmethod_byrequests_module modules/mod_lbmethod_byrequests.so
##LoadModule lbmethod_bytraffic_module modules/mod_lbmethod_bytraffic.so
##LoadModule lbmethod_heartbeat_module modules/mod_lbmethod_heartbeat.so
##LoadModule proxy_ajp_module modules/mod_proxy_ajp.so
##LoadModule proxy_balancer_module modules/mod_proxy_balancer.so
LoadModule proxy_connect_module modules/mod_proxy_connect.so
##LoadModule proxy_express_module modules/mod_proxy_express.so
##LoadModule proxy_fcgi_module modules/mod_proxy_fcgi.so
##LoadModule proxy_fdpass_module modules/mod_proxy_fdpass.so
##LoadModule proxy_ftp_module modules/mod_proxy_ftp.so
LoadModule proxy_http_module modules/mod_proxy_http.so
##LoadModule proxy_scgi_module modules/mod_proxy_scgi.so
##LoadModule proxy_wstunnel_module modules/mod_proxy_wstunnel.so
```


### 限制IP地址直接访问
```
<VirtualHost *:80>
        ServerName 10.*.*.*  ##目标IP
        <Location />
                Order allow,deny
                Deny from all
        </Location>
        #<Directory />
        #       Require all denied
        #</Directory>
</VirtualHost>

```






## 检查语法是否有误
```
httpd -t
systemctl restart httpd
```

## 修订  
- 2018-06-08 17:07:00
- 2018-07-03 09:01:00
- 2019-06-03 17:01:00
- 待续
          
## 参考  
- [ProxyAbuse](https://wiki.apache.org/httpd/ProxyAbuse)   
- [模块索引](http://httpd.apache.org/docs/2.4/zh-cn/mod/)  
- [Centos 7 安全加固](/maintenance/2018/05/22/centos-7-security-reinforcement.html)




