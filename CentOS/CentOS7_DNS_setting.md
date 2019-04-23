# CentOS7 DNS 设置

CentOS 7，手工设置 /etc/resolv.conf 里的DNS，重启网络服务，系统会重新覆盖，一般采用以下办法修改DNS配置。

## 使用命令行工具nmcli设置
```
#显示当前网络连接
#nmcli connection show
```
NAME           UUID                                  TYPE      DEVICE 
System ens192  e939fa4c-c08b-4eb5-bdcb-0009b8102468  ethernet  ens192 
System ens160  111df35c-1397-476d-afbc-b934f5186508  ethernet  --   

```
#修改当前网络连接对应的DNS服务器，这里的网络连接可以用名称或者UUID来标识
nmcli con connection modify "System ens192" ipv4.dns "223.5.5.5 8.8.8.8"
```
#将dns配置生效
```
nmcli connection up "System ens192"
nmcli connection modify "System ens192" +ipv4.dns +ipv4.dns 8.8.8.8
#修改配置文件执行生效
systemctl restart network  #覆盖/etc/resolv.conf
nmcli connection reload 

```

## 直接修改网卡配置文件
```
#vi /etc/sysconfig/network-scripts/ifcfg-ens192
#添加
DNS1=223.5.5.5
DNS2=8.8.8.8
```

## 使用传统方法，手工修改 /etc/resolv.conf

- 修改 /etc/NetworkManager/NetworkManager.conf 文件，在main部分添加 “dns=none” 选项：
```
[main]
plugins=ifcfg-rh
dns=none
```
- NetworkManager重新装载上面修改的配置
```
systemctl restart NetworkManager.service
```
- 手工修改 /etc/resolv.conf
```
nameserver 114.114.114.114
nameserver 8.8.8.8
```



参考：
- [centOS7网络配置（nmcli，bonding，网络组）](https://www.cnblogs.com/duzhaoqi/p/7491761.html)