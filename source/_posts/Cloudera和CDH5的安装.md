---
title: Cloudera和CDH5的安装
date: 2018-06-15 08:20:02
tags: 
  - cloudera
categories:
  - 解决方案 
---

## 1.准备工作

### 1.安装Oracel JDK（所有节点）
查看cloudera官网上堆系统的要求安装jdk版本，[查看链接](https://www.cloudera.com/documentation/enterprise/release-notes/topics/rn_consolidated_pcm.html#concept_dcw_m5v_jz)
CDH5.11.X要求JDK1.7u55 or higher 、JDK1.8u31 or higher，**要求集群所有机器的JDK版本一致**

### 2.安装Mysql （主节点）
集群中至少有一台机器安装mysql，建议安装在主节点机器上。安装mysql[参考链接][1]

- mysql配置
``` bash
# 配置mysql数据库远程连接
$ mysql -uroot -p123456
mysql>grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;
mysql>flush privileges;
$ sudo vi /etc/mysql/my.conf
bind-address=127.0.0.1 注释掉
$ sudo service mysql restart
```
- 生成CDH5所需数据库

``` sql
create database amon default character set utf8;
grant all on amon.* to 'amon'@'%' identified by 'amon';
create database rman default character set utf8;
grant all on rman.* to 'rman'@'%' identified by 'rman';
create database metastore default character set utf8;
grant all on metastore.* to 'hive'@'%' identified by 'hive';
create database sentry default character set utf8;
grant all on sentry.* to 'sentry'@'%' identified by 'sentry';
create database nav default character set utf8;
grant all on nav.* to 'nav'@'%' identified by 'nav';
create database navms default character set utf8;
grant all on navms.* to 'navms'@'%' identified by 'navms';
create database hue default character set utf8 default collate utf8_general_ci;
grant all on hue.* to 'hue'@'%' identified by 'hue';
select * from information_schema.schemata;
create database oozie;
grant all privileges on oozie.* to 'oozie'@'localhost' identified by 'oozie';
grant all privileges on oozie.* to 'oozie'@'%' identified by 'oozie';
```

### 3.网络配置 （所有节点）
``` bash
# 1.修改hostname
sudo vi /etc/hostsname ==> cloud01,cloud02,cloud03
# 2. 修改ip映射
sudo vi /etc/hosts 
==> example: 
192.168.1.1 cloud01[,other name]
192.168.1.3 cloud02[,other name]
192.168.1.3 cloud03[,other name]

# 3. 关闭防火墙
sudo apt-get install ufw -y
sudo ufw stop
sudo ufw status //查看防火墙状态
```

### 3.设置SSH无密码访问
``` bash
# 1.生成密钥对，集群所有机器上执行命令
ssh-keygen -t rsa  //默认生成两个文件 ~/.ssh/id_rsa ~/.ssh/id_rsa.pub

# 2.选择集群一台机器，例如cloud01,假设集群有5台机器
root@cloud01:~# for ip in `seq 1 5`;do scp root@cloud0$ip:~/.ssh/id_rsa.pub ~/.ssh/rsa_temp && cat ~/.ssh/rsa_temp >> ~/.ssh/authorized_keys;done //此时会在~/.ssh/目录下生成authorizied_keys文件
root@cloud01:~# for ip in `seq 2 5`;do scp ~/.ssh/authorized_keys root@cloud0$ip:~/.ssh/;done //复制authorizedkeys到其他的主机中
root@cloud01:~# rm ~/.ssh/rsa_temp

# 3.SSH服务器配置
root@cloud01:~# vi /etc/ssh/sshd_config
PermitRootLogin yes //修改默认的without password

# 4.重启SSH服务
sudo service sshd restart

# 5.免密码登录失败原因
1、权限问题

.ssh目录，以及/home/当前用户 需要700权限，参考以下操作调整

sudo chmod 700 ~/.ssh

sudo chmod 700 /home/当前用户

.ssh目录下的authorized_keys文件需要600或644权限，参考以下操作调整

sudo chmod 600 ~/.ssh/authorized_keys

```

### 4.配置NTP时间同步服务（所有节点）
- 时间服务器配置（主节点）

``` bash 
# 所有节点安装ntp服务
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
- 请求对时客户端配置（其他节点）

``` bash
#安装ntp服务
sudo apt-get install ntp
sudo vi /etc/ntp.conf
#去掉之前默认的server
server 192.168.1.1（时间服务器的ip）
fudge 192.168.1.1 stratum 5

```

- 对时操作

``` bash
1. 使用ntp服务对时
$ sudo service ntp stop
$ sudo ntpdate 时间服务器ip
$ sudo service ntp start

2.查看时间状态
$ timedatectl status
$ sudo ntpq -p

3.更换时区
$ sudo tzselect
```

### 5.修改source.list
``` bash
# 1.增加两个源
deb [arch=amd64] http://archive.cloudera.com/cm5/ubuntu/trusty/amd64/cm trusty-cm5 contrib
deb-src http://archive.cloudera.com/cm5/ubuntu/trusty/amd64/cm trusty-cm5 contrib

# 2.更新
sudo apt-get update
```

## 2.安装CDH

### 1.[下载][2]CDH安装包（主节点）
Ubuntu14.04下载[链接][3]（CM5.11）
cloudera-manager[下载链接][4]
CDH5[下载链接][5]
``` bash

# 1.解压安装包
sudo mkdir /opt/cloudera-manager
sudo tar xzf cloudera-manager*.tar.gz -C /opt/cloudera-manager
# 假设$CMF_DEFAULTS=/opt/cloudera-manager/cm-5.11.1

# 2.初始化数据库
sudo $CMF_DEFAULTS/share/cmf/schema/scm_prepare_database.sh mysql cm -hMasterNode -uroot -p123456 --scm-host MasterNode scm scm scm

# 3.修改angent配置
sudo vi $CMF_DEFAULTS/etc/cloudera-scm-agent/config.ini 
server_host = MasterNode

# 4.复制cloudera-manager到其他主机
root@cloud01:~# for $ip in `seq 2 5`;do scp -r $CMF_DEFAULTS/ root@cloud0$ip:/opt/
```

### 2.为CDH创建用户（所有节点）
``` bash
root@cloud01:~# useradd --system --home=$CMF_DEFAULTS/run/cloudera-scm-server --no-create-home --shell=/bin/false --comment "Cloudera SCM User" cloudera-scm
```
### 3.创建相关文件夹（主节点）
``` bash
$ sudo mkdir /var/lib/cloudera-scm-server
$ sudo chown cloudera-scm:cloudera-scm /var/lib/cloudera-scm-server
```

### 4.准备Parcels，用于安装CDH5
``` bash

# 1.主节点放置CDH5 Parcel 库文件
$ sudo mkdir -p /opt/cloudera/parcel-repo
$ sudo chown cloudera-scm:cloudera-scm /opt/cloudera/parcel-repo
$ sudo mv /path/to/CDH5.parcel /opt/cloudera/parcel-repo/ 
$ sudo mv /path/to/CDH5.parcel.sha1 /opt/cloudera/parcel-repo/CDH5.parcel.sha
$ sudo mv /path/to/manifest.json /opt/cloudera/parcel-repo/

# 2.所有节点创建parcels目录
$ sudo mkdir -p /opt/cloudera/parcels
$ sudo chown cloudera-scm:cloudera-scm /opt/cloudera/parcels
```
### 5.启动cloudera scm 服务
``` bash
# 1. 主节点启动server服务
$ root@cloud01:~# /$CMF_DEFAULTS/etc/init.d/cloudera-scm-server start
//server启动日志/var/log/cloudera-scm-server/cloudera-scm-server.log

# 2. 所有节点启动agent服务
$ root@cloud01:~# /$CMF_DEFAULTS/etc/init.d/cloudera-scm-agent start
//agent启动日志/var/log/cloudera-scm-agent/cloudera-scm-agent.log

# 3.设置机器重启自动运行服务
$ cp $CMF_DEFAULTS/etc/init.d/cloudera-scm-server /etc/init.d/cloudera-scm-server
$ update-rc.d cloudera-scm-server defaults

$ cp $CMF_DEFAULTS/etc/init.d/cloudera-scm-agent /etc/init.d/cloudera-scm-agent
$ update-rc.d cloudera-scm-agent defaults

# 4.取消自动重启
$ sudo update-rc.d -f cloudera-scm-agent remove
```
tips:这一步如果没有完成好，会导致后面的cloudera-manager agent安装出错，
推荐顺序
启动/opt目录下的agent服务
设置步骤3，**但不要使用service 命令启动**
安装完成后，可以去掉自动重启（执行步骤4）



### 6.CDH5网页配置

- 打开http://clouder-server-host:7180
- 默认密码是admin
- 选择免费版本，不需要安装JDK
- root用户登录，提前将集群机器的root密码设置一样，并且打通root用户的ssh登录

### 7.安装kafka服务

1.下载kafka parcel，在线或者[离线][6]都可以，然后在集群所有机器中分发激活Kafka Parcel

2.集群添加kafka服务
若提示，或者错误日志中提示Java Heap空间太小：
Missing required value: Destination Broker List
Missing required value: Source Broker List

可按如下方法配置后重试即可：
a. 填写Source Brokers List
填写Kafka Broker所在节点构成的列表（用逗号分隔），如下（本文在所有节点部署了Kafka Broker）：
master:9092,slave1:9092,slave2:9092,slave3:9092,slave4:9092

b. 填写Destination Brokers List
若添加了Kafka MirrorMaker，则可填写其所在节点构成的列表；若未添加Kafka MirrorMaker，可填写任意服务器即可，如下：
master:9092,slave1:9092,slave2:9092,slave3:9092,slave4:9092
或：
example.com:9092

c. 修改Java Heap Size
填写上面列表后，点击继续，出错后，Kafka服务未启动。返回集群配置，打卡Kafka服务配置页，查找“Java Heap Size of Broker”项，将对大小从50MB修改为256MB。
d. 配置Topic Whitelist
配置Topic Whitelist项为正则表达式：(?!x)x，保存更改。然后添加角色实例，重新配置即可。
参考链接：adding a Kafka service failed


## 3.CDH5安装常见问题

### 1. 主机检查警告
![此处输入图片的描述][7]
``` 
第一个警告：

 Cloudera 建议将 /proc/sys/vm/swappiness 设置为 10。当前设置为 60。使用 sysctl 命令在运行时更改该设置并编辑 /etc/sysctl.conf 以在重启后保存该设置。 

 echo 10 > /proc/sys/vm/swappiness

这样操作重启机器还是还原，要永久改变

vim   /etc/sysctl.conf
vm.swappiness=10
 
 第二个警告，提示执行命令：

 echo never > /sys/kernel/mm/transparent_hugepage/defrag

 echo never > /sys/kernel/mm/transparent_hugepage/enabled

执行完毕，重启后，警告依然，暂时不处理
```
### 2.mysql数据库生成报错
``` bash
# 1.主要是系统的java环境中没有连接数据库的包，因此需要手动把包拷贝到系统目录
$ sudo cp mysql-connector-*.jar /usr/share/java/mysql-connector-java.jar

# 2.设置mysql远程登录和访问权限问题
mysql>grant all privileges on . to ‘root’@’%’ identified by ‘youpassword’ with grant option;
mysql>flush privileges;
$ sudo vi /etc/mysql/my.conf
comment bind-address = 127.0.0.1
$ sudo service mysql restart
```

### 3.kafka安装问题

参考官方教程，分配Kafka parcel包，第一次安装会遇到问题，解决方案如下：
http://www.aboutyun.com/thread-19903-1-1.html


### 4.参考链接
http://www.cnblogs.com/codedevelop/p/6762555.html


  [1]: http://blog.csdn.net/chenpy/article/details/50344085
  [2]: https://www.cloudera.com/documentation/enterprise/release-notes/topics/cm_vd.html
  [3]: https://archive.cloudera.com/cm5/cm/5/cloudera-manager-trusty-cm5.11.1_amd64.tar.gz
  [4]: http://archive.cloudera.com/cm5/cm/5/
  [5]: http://archive.cloudera.com/cdh5/parcels/
  [6]: https://archive.cloudera.com/kafka/parcels/latest/
  [7]: http://images2015.cnblogs.com/blog/1150091/201704/1150091-20170425193926303-554555577.png
  
  
### 5.agent启动报错
ProtocolError: <ProtocolError for 127.0.0.1/RPC2: 401 Unauthorized>
resolution: 
Steps to find and kill the process:
1) Find the port which is used by supervisor: 
> ps aux |grep supervisor
2) kill the port
>sudo kill -9 <port number> 

