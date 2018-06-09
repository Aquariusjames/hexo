---
title: linux服务器时间同步
tags:
  - linux
  - ntp
categories:
  - 解决方案
  - linux
date: 2017-04-25 18:57:24
---

### Ubuntu下服务器时间同步
#### 1.时间服务器配置
``` bash
#服务器cloud01,cloud02,cloud03都安装ntp服务
sudo apt-get install ntp

#查看服务是否启动
service --status-all
[+]表示启动

#选择cloud01作为时间同步服务器
ssh cloud01
sudo vi /etc/ntp.conf
=====
1. 修改server
server [IP or hostname] [prefer]
在 server 后端可以接 IP 或主机名，个人比较喜欢使用 IP 来设定， perfer 表示『优先使用』的服务器。
server 2.cn.pool.ntp.org prefer
server 0.asia.pool.ntp.org prefer
server 3.asia.pool.ntp.org prefer

2. 修改本地server
#让NTP Server和其自身保持同步，如果在/etc/ntp.conf中定义的server都不可用时，将使用local时间作为ntp服务提供给ntp客户端
server 127.127.1.0
fudge 127.127.1.0 stratum 5

3.  restrict管理时间服务器权限
#不允许来自公网上ipv4和ipv6客户端的访问
restrict -4 default kod notrap nomodify nopeer noquery
restrict -6 default kod notrap nomodify nopeer noquery
#
restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap
=====

```
#### 2.请求对时客户端配置
这里cloud02和cloud03分别是客户端
``` bash
#安装ntp服务
sudo apt-get install ntp
sudo vi /etc/ntp.conf

#去掉之前默认的server
server 192.168.1.1（时间服务器的ip）
fudge 192.168.1.1 stratum 5

```

#### 3.客户端和时间服务器同步
- 使用ntpdate更新时间
# 注意要停止ntp服务才能使用ntpdate更新时间
sudo ntpdate 192.168.1.1
sudo hwclock --systohc #将系统时间写入硬件时间
sudo hwclock --hctosys #将硬件时间写入系统时间

- 使用ntpd更新时间
sudo service ntp start #启动ntp进程，自动逐渐同步时间

#### 4.查看时间状态
timedatectl status
sudo ntpq -p

#### 5.更换时区
sudo tzselect


### 常见错误解决方案
1. no server suitable for synchronization found
参考链接：[http://www.blogjava.net/spray/archive/2008/07/10/213964.html](http://www.blogjava.net/spray/archive/2008/07/10/213964.html)
