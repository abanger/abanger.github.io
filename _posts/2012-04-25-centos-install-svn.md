---
layout: post
title: Centos下安装svn服务
date: 2012-04-25 17:11
author: abanger
comments: true
categories: CentOS
---
安装php，会直接安装httpd

<pre>
yum -y install php
</pre>


<pre>
yum install mod_dav_svn
</pre>

建svn服务器路径
<pre>
mkdir /svnroot
</pre>

编辑httpd配置文件

录/etc/httpd/conf.d/svn.conf

<pre>
  < location /svn>
        DAV svn
        SVNParentPath /svnroot    
        AuthBasicProvider ldap
        AuthName "LDAP Developer Auth"
        AuthLDAPUrl ldap://x.x.x.:389/ou=People,dc=xx,dc=cn
        AuthLDAPBindDN "cn=Manager"
        AuthLDAPBindPassword xxxxxxx
        AuthzLDAPAuthoritative off
        AuthType basic
        Require valid-user
        AuthzSVNAccessFile /svnroot/etc/authz
  < /location>
</pre>


编辑
/svnroot/etc/authz
设置组gp1,两个成员xiaoer,xiaosan,把test项目gp1有读写权限，laoda只看就行了,这三个用户密码通过ldap管理
<pre>
[groups]
# harry_and_sally = harry,sally
gp1=xiaoer,xiaosan


[test:/]
@gp1 = wr
laoda =r

</pre>


设置权限
<pre>
 chown -R apache.apache /svnroot/etc/authz
 svnadmin create --fs-type bdb /svnroot/abc

 chown -R apache.apache /svnroot/test/
 chcon -R -h -t httpd_sys_content_t /svnroot

</pre>

完成，可Commit,checkout.
