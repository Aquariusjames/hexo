---
title: ubuntu端口转发
tags:
  - ubuntu
  - iptable
  - 端口转发
categories:
  - 解决方案
  - ubuntu
date: 2017-05-23 21:11:44
---

# 1. 内网有一台公网服务器
[iptables方法](http://blog.csdn.net/gobitan/article/details/50696641)

# 2. 内网没有公网服务器，只有云服务器

> 思路：利用ssh反向代理实现内网穿透

准备工作：
假设内网有机器A(192.168.1.2),机器B(192.168.1.3),机器A机器B在一个局域网内，但是只有机器A能够访问互联网，本身没有静态公网IP,机器B可以访问机器A，但是无法访问互联网。另外有一台可访问的云服务器C(公网IP: 104.28.39.108)

目的：实现在任意有网络的地方访问服务器A和服务器B

1. 将机器A的ssh公钥复制到机器C的authorized_keys，实现A到C的免密码登陆
2. 在机器A执行如下脚本：

``` bash
touch /home/hadoop/autossh/monitor-auto-ssh.sh && chmod +x /home/hadoop/autossh/monitor-auto-ssh.sh
vim /home/hadoop/autossh/monitor-auto-ssh.sh
```
monitor-auto-ssh.sh内容如下：
``` bash
#! /bin/sh

# check every 30 seconds
# crontab -e
# */1 * * * * bash /home/hadoop/autossh/monitor-auto-ssh.sh
# * * * * * sleep 30; bash /home/hadoop/autossh/monitor-auto-ssh.sh

PROCESS_NAME='55566'
PROCESS_PATH='/home/hadoop/autossh'
START_PROCESS="autossh -f -M 55567 -NR 55566:localhost:22 hadoop@104.28.39.108"
VPN_PROCESS="autossh -f -M 55577 -NR 10012:localhost:13838 hadoop@104.28.39.108"
VPN_PROCESS_NAME="10012"
#PORT_TRANS="ssh -C -f -N -g -R 8888:127.0.0.1:8888 hadoop@39.108.120.127"
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

3. 机器A执行定时检查任务，确保ssh隧道断开自动重连

``` bash
# check every 30 seconds
crontab -e
*/1 * * * * bash /home/hadoop/autossh/monitor-auto-ssh.sh
* * * * * sleep 30; bash /home/hadoop/autossh/monitor-auto-ssh.sh
```
4. 服务器C需要在云网络安全组开放55566和10012两个端口，这两个端口分别通过隧道反向代理A机器的22号端口和13838端口

### 外网访问方式

1. ssh 指定机器C的55566端口就可以连接到A机器了，通过A机器可以跳转到B机器，实现间接链接

2. 搭建VPN。在机器A上安装[shadowsocks服务器端](https://shadowsocks.be/9.html)，并且设置端口为13838（要和脚本中的端口保持一致），然后客户端机器安装[shadowsocks客户端](https://github.com/shadowsocks/shadowsocks/releases),代理服务器地址为104.28.39.108，端口为10012，用户名和密码从A机器上获取。

至此，代理服务器搭建完毕，设置shadowsocks全局代理模式，默认将本地127.0.0.1的1080端口的请求全部转发到C服务器上的10012端口，而C服务器的10012端口通过autossh反向代理到了A机器的13838端口，从而实现了从外网任何一台可以上网的设备远程连接到没有公网环境的内网服务器A和服务器B，能够访问内网的任何服务。
