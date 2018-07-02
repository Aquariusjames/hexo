---
title: ssh反向代理新姿势
tags:
  - 笔记
  - ssh
categories:
  - 笔记
  - 网络
date: 2018-07-02 17:28:36
---

# 基础知识

## ssh 的端口转发

应用链接方向： 从应用的client到应用的server端
ssh链接方向：ssh client --> ssh server

1. 本地转发(local forwarding)

SSH链接方向和本地链接方向一致

    ssh -L <local port>:<remote host>:<remote port> <SSH hostname>

![ssh local forwarding](http://img610.ph.126.net/-ifAtproB7491sZsVf1gYQ==/1689131335242809359.jpg)

本地转发会在本地机器上监听一个端口，所有访问这个端口的数据都会通过SSH隧道传输到远程主机的对应端口上, 最后将数据返回到客户端，完成整个转发过程

由于本地转发绑定的地址是lookback接口，因此绑定的local port只能在本机访问，不能让其他的client访问，如果想实现其他的client也可以访问的话，需要在ssh host 的 sshd_config 文件中添加一个参数 "GatewayPorts yes"，或者直接使用 -g 命令


2. 远程转发(local forwarding)

SSH链接方向和本地方向不一致，就是远程转发

    ssh -R <local port>:<remote host>:<remote port> <SSH hostname>

远程转发会在远程主机上面监听一个端口，所有访问远程主机的端口的数据都会被转发到本地对应的端口上,ssh反向代理(隧道)技术就是这样实现的

![ssh remote forwarding](http://img623.ph.126.net/AM7N_HSE8y4XDkYqACKM5A==/1938518164608052599.jpg)

例如实现远程服务器A的1234端口映射到本机的22端口，实现远程服务器访问本地的22端口

    ssh -R 1234:localhost:22  username@ServerA

3. 动态转发(dynamic forwarding)

指定一个本地机器 “动态的'’ 应用程序端口转发. 工作原理是这样的, 本地机器上分配了一个 socket 侦听 port 端口, 一旦这个端口上有了连接, 该连接就经过安全通道转发出去, 根据应用程序的协议可以判断出远程主机将和哪里连接. 目前支持 SOCKS4 协议, 将充当 SOCKS4 服务器. 只有 root 才能转发特权端口. 可以在配置文件中指定动态端口的转发.

    ssh -D <local port> <SSH Server>

# ssh 端口转发参数

ssh的三个强大的端口转发命令：

转发到远端：ssh -C -f -N -g -L 本地端口:目标IP:目标端口 用户名@目标IP

转发到本地：ssh -C -f -N -g –R 本地端口:目标IP:目标端口 用户名@目标IP

ssh -C -f -N -g -D listen_port user@Tunnel_Host

-C：压缩数据传输。

-f ：后台认证用户/密码，通常和-N连用，不用登录到远程主机。

-N ：不执行脚本或命令，通常与-f连用。

-g ：在-L/-R/-D参数中，允许远程主机连接到建立的转发的端口，如果不加这个参数，只允许本地主机建立连接。

-L 本地端口:目标IP:目标端口

将本地机(客户机)的某个端口转发到远端指定机器的指定端口. 工作原理是这样的, 本地机器上分配了一个 socket 侦听 port 端口, 一旦这个端口上有了连接, 该连接就经过安全通道转发出去, 同时远程主机和 host 的 hostport 端口建立连接. 可以在配置文件中指定端口的转发. 只有 root 才能转发特权端口. IPv6 地址用另一种格式说明: port/host/hostport

-R本地端口:目标IP:目标端口

将远程主机(服务器)的某个端口转发到本地端指定机器的指定端口. 工作原理是这样的, 远程主机上分配了一个 socket 侦听 port 端口, 一旦这个端口上有了连接, 该连接就经过安全通道转向出去, 同时本地主机和 host 的 hostport 端口建立连接. 可以在配置文件中指定端口的转发. 只有用 root 登录远程主机才能转发特权端口. IPv6 地址用另一种格式说明: port/host/hostport

-p ：被登录的ssd服务器的sshd服务端口。

-D port

指定一个本地机器 “动态的'’ 应用程序端口转发. 工作原理是这样的, 本地机器上分配了一个 socket 侦听 port 端口, 一旦这个端口上有了连接, 该连接就经过安全通道转发出去, 根据应用程序的协议可以判断出远程主机将和哪里连接. 目前支持 SOCKS4 协议, 将充当 SOCKS4 服务器. 只有 root 才能转发特权端口. 可以在配置文件中指定动态端口的转发.

# 实现内网端口转发

问题： 内网有服务器器A, 无公网IP，现有公网服务器B，如何实现在外网情况下链接服务器B的某个端口访问到A的服务

host | 类型 | ip | port
--- | ---|---|---
A | 内网 | 192.168.1.3 | 1234
B | 外网 | www.123.com | 5678

通过ssh的反向代理可以实现B服务器的5678 端口绑定在A服务器的1234端口上

``` bash
# 登录到A服务器
ssh -C -f -N -g -R 5678:127.0.0.1:1234 username@www.123.com
```

# 实现内网VPN服务


问题： 内网有服务器器A, 无公网IP，现有公网服务器B，如何实现在外网情况下访问到内网的所有服务

host | 类型 | ip | port
--- | ---|---|---
A | 内网 | 192.168.1.3 | 192.168.1.0/24:*
B | 外网 | www.123.com | 5678

解决方案： shadowsocks + ssh远程转发

1. A机器安装shadowsock 服务端

[shadowsocksR一键安装](http://xraorao.top/2018/06/25/ShadowsocksR%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85%E8%84%9A%E6%9C%AC/)


2. A机器做远程转发

    ssh -C -f -N -g -R  5678:127.0.0.1:shadowsocks_server_port username@www.123.com 

3. VPN访问

    安装shadowsocks客户端，链接后配置socks5代理，就可以链接到内网的任何服务了

# autossh--强大而稳定的解决方案

ssh隧道非常容易断掉，在做远程转发的时候，有时候往往无法链接服务器，因此需要一个稳定的SSH链接，autossh就能实现SSH的断线自动重启


``` bash

# 配置好客户端到远程服务的ssh免密码连接

autossh -f -M 55888 -NR *:10013:localhost:22 hadoop@oceanai.com.cn

usage: autossh [-V] [-M monitor_port[:echo_port]] [-f] [SSH_OPTIONS]

    -M specifies monitor port. Overrides the environment
       variable AUTOSSH_PORT. 0 turns monitoring loop off.
       Alternatively, a port for an echo service on the remote
       machine may be specified. (Normally port 7.)
    -f run in background (autossh handles this, and does not
       pass it to ssh.)
    -V print autossh version and exit.

```

设置crontab任务，实现代理的自检查和重启功能，基本不会断连接

```

crontab -e 
*/1 * * * * bash /home/hadoop/autossh/monitor-auto-ssh.sh
* * * * * sleep 30; bash /home/hadoop/autossh/monitor-auto-ssh.sh

vim /home/hadoop/autossh/monitor-auto-ssh.sh

---- 
#! /bin/sh

PROCESS_NAME='55566'
PROCESS_PATH='/home/hadoop/autossh'
START_PROCESS="autossh -f -M 55567 -NR 55566:localhost:22 hadoop@www.123.com"
VPN_PROCESS="autossh -f -M 55577 -NR 10012:localhost:13838 hadoop@www.123.com"
VPN_PROCESS_NAME="10012"
#PORT_TRANS="ssh -C -f -N -g -R 8888:127.0.0.1:8888 hadoop@www.123.com"
#PORT_TRANS_NAME="8888:127.0.0.1:8888"

proc_num()                      #查询进程数量  
{  
    num=`ps -ef | grep ${PROCESS_NAME} | grep -v grep | wc -l`  
    return $num  
}   

proc_num2()
{
    num=`ps -ef | grep ${VPN_PROCESS_NAME} | grep -v grep | wc -l`  
    return $num
} 
proc_num    
number=$?
proc_num2
number2=$?
if [ $number -eq 0 ]            #如果进程数量为0  
then                            #重新启动服务
    echo "Restarting ${PROCESS_NAME} ..."
    echo $(date '+%Y-%m-%d %T') >> ${PROCESS_PATH}/restart.log
    (${START_PROCESS}) & 
    echo "over"
elif [ $number2 -eq 2 ]
then
    echo "ssh proxy has been started!"
fi
   
if [ $number2 -eq 0 ]            #如果进程数量为0  
then                            #重新启动服务
    echo "Restarting ${VPN_PROCESS_NAME} ..."
    echo $(date '+%Y-%m-%d %T') >> ${PROCESS_PATH}/restart.log
    (${VPN_PROCESS}) &
    echo "over"
elif [ $number -eq 2 ]
then
    echo "shadow proxy has been started!"
fi
```