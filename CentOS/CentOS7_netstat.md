# CentOS7 netstat命令详解

## 简介
netstat查询网络状态，显示整个CentOS系统中所有tcp/udp/unix套接字连接状态的命令行工具，列出所有已经连接或者等待连接状态的连接，还能列出路由表，接口状态(Interface Statistics)和多播成员 (Multicast Memberships)等信息。可识别应用监听端口，判断应用是否正常的在监听某个端口。

## 常见参数
-a (all)显示所有选项，netstat默认不显示LISTEN相关
-t (tcp)仅显示tcp相关选项
-u (udp)仅显示udp相关选项
-n 拒绝显示别名，能显示数字的全部转化成数字
-l 仅列出有在 Listen (监听) 的服務状态

-p 显示建立相关链接的程序名(macOS中表示协议 -p protocol)
-r 显示路由信息，路由表
-e 显示扩展信息，例如uid等
-s 按各个协议进行统计 (重要)
-c 每隔一个固定时间，执行该netstat命令


## 实用命令实例
### 列出所有端口 (包括监听和未监听的)
```
列出所有端口:     netstat -a
列出所有tcp端口:  netstat -at
列出所有udp端口:  netstat -au
```

### 列出所有处于监听状态的Sockets
只显示监听端口:          netstat -l
只列出所有监听tcp端口:   netstat -lt
只列出所有监听udp端口:   netstat -lu
只列出所有监听UNIX端口:  netstat -lx

### 显示每个协议的统计信息
```
显示所有端口的统计信息: netstat -s
显示 TCP 或 UDP 端口的统计信息: netstat -st 或 -su

```

### 显示PID和进程名称
```
netstat -p 
```
可以与其它开关一起使用，就可以添加 “PID/进程名称” 到 netstat 输出中，这样 debugging 的时候可以很方便的发现特定端口运行的程序。

### 显示核心路由信息
```
netstat -rn
```

### 显示网络接口列表
```
netstat -i
```


## 输出信息含义
#netstat -l
```
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN     
tcp6       0      0 [::]:http               [::]:*                  LISTEN     
tcp6       0      0 [::]:ssh                [::]:*                  LISTEN     
tcp6       0      0 [::]:https              [::]:*                  LISTEN     
udp        0      0 0.0.0.0:35133           0.0.0.0:*                          
udp        0      0 0.0.0.0:mdns            0.0.0.0:*                          
raw6       0      0 [::]:ipv6-icmp          [::]:*                  7          
Active UNIX domain sockets (only servers)
Proto RefCnt Flags       Type       State         I-Node   Path
unix  2      [ ACC ]     STREAM     LISTENING     17982    /run/lvm/lvmpolld.socket
unix  2      [ ACC ]     STREAM     LISTENING     11351    /run/systemd/journal/stdout
unix  2      [ ACC ]     STREAM     LISTENING     96226    /tmp/ssh-HtfUR1DU4U/agent.10459
unix  2      [ ACC ]     SEQPACKET  LISTENING     18049    /run/udev/control
unix  2      [ ACC ]     STREAM     LISTENING     34953    /run/dbus/system_bus_socket
unix  2      [ ACC ]     STREAM     LISTENING     31386    /var/run/avahi-daemon/socket
unix  2      [ ACC ]     STREAM     LISTENING     20905    /run/lvm/lvmetad.socket
unix  2      [ ACC ]     STREAM     LISTENING     19677    /run/systemd/private
```
上半部分是Active Internet connections，称为有源TCP连接，其中"Recv-Q"和"Send-Q"指的是接收队列和发送队列，这些数字一般都应该是0,如果大于0表示软件包正在队列中堆积，少见。
下半部分是Active UNIX domain sockets，称为有源Unix域套接口(和网络套接字一样，但是只能用于本机通信，性能可以提高一倍)。
Proto显示连接使用的协议，RefCnt表示连接到本套接口上的进程数量，Types显示套接口的类型，State显示套接口当前的状态，Path表示连接到套接口的其它进程使用的路径名。


