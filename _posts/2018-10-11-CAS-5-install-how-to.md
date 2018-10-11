---
layout: post
title:  "CAS 5安装"
date:   2018-10-11 13:11:02 +0800
categories: maintenance
---




CAS环境：centos7.5+Tomcat8.5+jdk-8u181。后台用户管理采用openldap，其安装请参考相关资料。

### 获取 CAS 
本次安装采用[gradle overlay](https://github.com/apereo/cas-gradle-overlay-template)项目，减少安装编译过程，速度比较快。需要用源码请到[CAS开源库](https://github.com/apereo/cas)下载。不喜欢使用gradle安装也可使用mvn,请到[CAS WAR Overlay template](https://github.com/apereo/cas-overlay-template)下载，不过最新版本都是采用gradle安装。CAS版本为5.2.4，其他版本安装会缺少依赖，请自行测试。

```
git clone -b 5.2 https://github.com/apereo/cas-gradle-overlay-template.git
```
当前目录下生成cas-gradle-overlay-template目录，称之为安装目录。

### 安装步骤

#### 配置gradle
添加所需依赖：json注册服务，REST接口，ldap接口。修改配置文件build.gradle，文件在安装目录cas-gradle-overlay-template/cas/下。dependencies添加三行。
```
    compile "org.apereo.cas:cas-server-support-json-service-registry:${project.'cas.version'}"
    compile "org.apereo.cas:cas-server-support-rest:${project.'cas.version'}"
    compile "org.apereo.cas:cas-server-support-ldap:${project.'cas.version'}"

```
编辑配置文件如下：
```
cd  cas-gradle-overlay-template
vi cas/build.gradle
```
```
dependencies {
    compile "org.apereo.cas:cas-server-webapp-tomcat:${project.'cas.version'}@war"
    if (!project.hasProperty('bootiful')) {
        // Other dependencies may be listed here...
    } else {
        println "Running CAS in Bootiful mode; all dependencies except the CAS web application are ignored."
    }
    compile "org.apereo.cas:cas-server-support-json-service-registry:${project.'cas.version'}"
    compile "org.apereo.cas:cas-server-support-rest:${project.'cas.version'}"
    compile "org.apereo.cas:cas-server-support-ldap:${project.'cas.version'}"

}
```

#### 生成war
制作机器要连到互联网下载相关文件。执行下面命令，若网速快，很快就可生成war。
```
./build.sh package
```
正常执行之后，在cas/build/libs/目录下生成cas.war。

#### 配置CAS
CAS配置也可以在上一步骤之前先在安装目录（cas-gradle-overlay-template）下的etc/cas/config/目录下修改配置文件后再生成war。  
上述生成war之后，把cas.war拷贝到Tomcat目录下webapps目录，重启Tomcat服务，在webapps目录下生成cas。  

1. 修改application.properties
在Tomcat目录下webapps/cas/WEB-INF/classes/application.properties文件，注释cas.authn.accept.users配置，此配置可用来测试还没有使用ldap管理用户前的安装是否正常。
```
#cas.authn.accept.users=casuser::Mellon
```
2. 修改个性配置文件
拷贝在安装目录（cas-gradle-overlay-template）下etc/cas/config/，至操作系统centos根目录/etc/cas/config下。
修改cas.properties部分内容如下

```
#vi /etc/cas/config/cas.properties
cas.tgc.encryption.key=bwSbnHesdfsafsdsdfsdsdssafsfsdsadsdsd2Ar4rM

##连接ldap
cas.authn.ldap[0].type=AUTHENTICATED
cas.authn.ldap[0].ldapUrl=ldap://10.1.0.16:389
cas.authn.ldap[0].useSsl=false
cas.authn.ldap[0].connectTimeout=5000
cas.authn.ldap[0].subtreeSearch=true
cas.authn.ldap[0].baseDn=ou=people,dc=edu,dc=cn

cas.authn.ldap[0].userFilter=uid={user}

# Bind credentials used to connect to the LDAP instance
cas.authn.ldap[0].bindDn=cn=adm,dc=edu,dc=cn
cas.authn.ldap[0].bindCredential=xxx001xxx001 #password,modi it

```
一般如上修改简单就可以使用ldap管理用户。记得重启Tomcat服务。


#### 注册服务
应用要接入CAS需要注册，在Tomcat目录/webapps/cas/WEB-INF/classes/services/下增加一json配置文件。可参考HTTPSandIMAPS-10000001.json文件配置。



### 其他
1. edu.cn是一个测试域名，请根据自己的域名自行更改。
2. 没有生成证书启动会有日记会报错，不影响使用,用下面两句可生成。
```
keytool -genkey -alias tomcat -keyalg RSA -keypass tomcat -storepass tomcat -keystore server.keystore -validity 36000
keytool -export -trustcacerts -alias tomcat -file server.cer -keystore server.keystore -storepass tomcat
```
3. 配置其他安全问题，包括此开源软件隐患，欢迎大家讨论[点击讨论](https://github.com/abanger/abanger.github.io/issues)
4. Tomcat和openldap安装和管理请先参考其他资料，有空后另文。
### 参考

[CAS Gradle Overlay](https://github.com/apereo/cas-gradle-overlay-template)
[Apereo CAS](https://github.com/apereo/cas)
[CAS之5.2x版本单点登录服务安装](https://blog.csdn.net/yelllowcong/article/details/78805420)


