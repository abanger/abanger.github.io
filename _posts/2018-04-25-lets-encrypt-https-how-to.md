---
layout: post
title:  "通过Let's Encrypt申请免费https证书"
date:   2018-04-25 21:06:02 +0800
categories: maintenance
---




通过Let's Encrypt申请免费https证书。

环境：centos 7+apache/nginx

本采用[letsencrypt.org](https://letsencrypt.org/getting-started/)推荐[Certbot ACME](https://certbot.eff.org/) 方式获取证书。

### 获取 Certbot

访问 [https://certbot.eff.org/](https://certbot.eff.org/)，依提示选择环境。

本文为 [https://certbot.eff.org/lets-encrypt/centosrhel7-apache](https://certbot.eff.org/lets-encrypt/centosrhel7-apache)，按提示安装。


### 安装步骤

#### 1. 安装 EPEL (Extra Packages for Enterprise Linux)
```
yum -y install yum-utils
yum-config-manager --enable rhui-REGION-rhel-server-extras rhui-REGION-rhel-server-optional
```
#### 2.安装Certbot
```
yum install certbot-apache
certbot --apache
```

#### 3.开始申请证书
```
# 注xxxlab.cn请根据自己的域名自行更改
certbot -i nginx -d "*.xxxlab.cn" -d xxxlab.cn --server https://acme-v02.api.letsencrypt.org/directory --manual --preferred-challenges dns certonly
```
申请过程交互如下：
```
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator manual, Installer nginx
Starting new HTTPS connection (1): acme-v02.api.letsencrypt.org
Obtaining a new certificate
Performing the following challenges:
dns-01 challenge for xxxlab.cn
dns-01 challenge for xxxlab.cn

-------------------------------------------------------------------------------
NOTE: The IP of this machine will be publicly logged as having requested this
certificate. If you're running certbot in manual mode on a machine that is not
your server, please ensure you're okay with that.

Are you OK with your IP being logged?
-------------------------------------------------------------------------------
(Y)es/(N)o: y

-------------------------------------------------------------------------------
Please deploy a DNS TXT record under the name  (第一次在DNS上配置)
_acme-challenge.xxxlab.cn with the following value:

7rBRoMUcyphsdfdsfsfdfsaa3332rsdfsaOeMv1Tfpk-6phU

Before continuing, verify the record is deployed.
-------------------------------------------------------------------------------
Press Enter to Continue

-------------------------------------------------------------------------------
Please deploy a DNS TXT record under the name (第二次在DNS上配置并保留第一次配置)
_acme-challenge.xxxlab.cn with the following value:

FjPeO3aHA2GXXhAiknu4d0sfdsdf23r223uXIff--E4

Before continuing, verify the record is deployed.
-------------------------------------------------------------------------------
Press Enter to Continue
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/xxxlab.cn/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/xxxlab.cn/privkey.pem
   Your cert will expire on 2018-06-27. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

注意过程需要两次在DNS上配置_acme-challenge.xxxlab.cn，其值如上所示。

使用dig测试
```
dig _acme-challenge.xxxlab.cn txt
```
结果如下：
```
......
;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;_acme-challenge.xxxlab.cn.                IN      TXT

;; ANSWER SECTION:
_acme-challenge.xxxlab.cn. 86400   IN      TXT     "7rBRoMUcyphsdfdsfsfdfsaa3332rsdfsaOeMv1Tfpk-6phU"
_acme-challenge.xxxlab.cn. 86400   IN      TXT     "FjPeO3aHA2GXXhAiknu4d0sfdsdf23r223uXIff--E4"
.........
```

#### 4. 测试及自动续订证书

测试自动续订证书

```
certbot renew --dry-run
```
如上述测试正常可通过定时器或其他方式自动执行如下命令
```
certbot renew
```
本步骤笔者未测试。



#### 5. 部署用例

1) nginx
```
......
server {
    server_name xxxlab.cn;
    listen 443 http2 ssl;
    ssl on;
    ssl_certificate /etc/cert/xxxlab.cn/fullchain.pem;
    ssl_certificate_key /etc/cert/xxxlab.cn/privkey.pem;
    ssl_trusted_certificate  /etc/cert/xxxlab.cn/chain.pem;

    location / {
      proxy_pass http://10.10.10.10:8080;
    }
}
......
```
2)apache
```
.....
SSLEngine on
SSLProtocol all -SSLv2
SSLCertificateFile /etc/cert/xxxlab.cn/fullchain.pem
SSLCertificateKeyFile /etc/cert/xxxlab.cn/privkey.pem
......
```



### 使用http认证方式
上述是使用dns作为认证获取申请通配符证书，早期笔者是使用http认证方式在服务器下配置提示认证信息。命令如下
```
letsencrypt-auto certonly --email abanger@xxxlab.cn  -d login.xxxlab.cn -a manual
```
邮件abanger@xxxlab.cn用于证书过期提示。


### 其他
1. 申请的主机要可访问互联网，DNS要互联网可访问。
2. xxxlab.cn是一个测试域名，请根据自己的域名自行更改。
3. 为什么使用https,参考链接2有描述。
4. 这样配置有没有引发其他安全问题，包括此开源软件隐患，欢迎大家讨论[点击讨论](https://github.com/abanger/abanger.github.io/issues)

### 参考

[申请Let's Encrypt通配符HTTPS证书](https://my.oschina.net/kimver/blog/1634575)

[Let's Encrypt 给网站加 HTTPS 完全指南certbot](https://blog.csdn.net/cstopery/article/details/51911298)

