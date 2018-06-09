---
title: '[原]Ubuntu14.04下安装单机版Storm运行环境'
tags: [ubuntu,storm,教程]
categories: [解决方案,storm]
date: 2016-10-20 19:40:39
---

### 1、安装java环境

- 如果选择的是sun的java安装包，去官网下载后，记得提升文件权限（chmod +x path/文件名）
- 设置环境变量，在/etc/profile文件中添加以下内容（针对所有用户，相当于windows下面的系统变量）
```
export JAVA_HOME=/opt/software/jdk1.8.0_92
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

- 设置默认jdk

```
sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_92/bin/java 300    
sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_92/bin/javac 300   
sudo update-alternatives --install /usr/bin/jar jar /usr/lib/jvm/jdk1.8.0_92/bin/jar 300  
sudo update-alternatives --install /usr/bin/javah javah /usr/lib/jvm/jdk1.8.0_92/bin/javah 300  
sudo update-alternatives --install /usr/bin/javap javap /usr/lib/jvm/jdk1.8.0_92/bin/javap 300  
```
- 查看系统已经存在的jdk
sudo update­alternatives ­­list java
![这里写图片描述](http://img.blog.csdn.net/20161020193441180)
- 配置默认jdk
sudo update­alternatives ­­config java
![这里写图片描述](http://img.blog.csdn.net/20161020193529813)

*表示当前默认的JDK，我这里已经设置好了最后使用java javac java -version命令来查看是否配置成功*

### 2、安装Python

- Ubuntu默认安装了，直接使用Python命令来检测是否安装成功
![这里写图片描述](http://img.blog.csdn.net/20161020193604853)

### 3、安装Zookeeper

安装前，把后面要用到的安装工具全部安装好
sudo apt-get install g++
sudo apt-get install uuid-dev
sudo apt-get install git
sudo apt-get install automake
sudo apt-get install libtool

- 下载Zookeeper3.4.6
- 提升文件权限（chnmod +x path/文件名）
- 解压 tar -xvzf filename
- 配置ZooKeeper环境变量
>  sudo gedit /etc/profile
>  在/etc/profile中添加环境变量
```
export ZOOKEEPER_HOME=/opt/software/zookeeper-3.4.6
export PATH=\$PATH:\$ZOOKEEPER_HOME/bin
```

利用source /etc/profile 命令来让环境变量立即生效

### 4、安装ZeroMQ

```
下载zeromq2.1.7.tar.gz
编译安装
./configure
Make
Sudo make install
Sudo ldconfig
```
### 5、安装Jzmp
安装git工具，不安装也可以直接去github下载release版本
```
sudo apt-get install git
进入jzmp解压包的根目录
编译和安装（前面的java的环境变量没有配置好，或者没有设置成默认的，这里无法通过）
./configure
make
sudo make install
```
### 6、安装storm
```
去Apache下载apache-storm-0.9.6.tar.gz
解压后设置环境变量
Sudo gedit /etc/profile

末尾添加
export STORM_HOME=/opt/software/apache-storm-0.9.6
export PATH=$PATH:$STORM_HOME/bin
```
### 7、设置Storm的配置文件

####首先设置zookeeper的配置文件
```
进入 cd /opt/software/zookeeper-3.4.6/conf
从sample_zoo.cfg文件复制一份并重命名为zoo.cfg
添加以下内容
dataDir=/opt/zookeeper/data
dataLogDir=/opt/zookeeper/log
server.1=127.0.0.1:2888:3888
dataDir放置数据信息
dataLogDir放置日志信息
注意：必须手动创建该目录，否则后面启动zookeeper的时候，zkServer.sh status将会报错，最终显示的界面都是错误信息
server.1由于是本地模式，所以只配置了一个，如果有多个机器，可以进行多个配置server.2
进入/opt/software/zookeeper-3.4.6/bin目录进行测试
```
zkServer.sh start
![这里写图片描述](http://img.blog.csdn.net/20161020193804760)
zkServer.sh status
![这里写图片描述](http://img.blog.csdn.net/20161020193836614)
zkServer.sh stop
![这里写图片描述](http://img.blog.csdn.net/20161020193846942)
####接下来设置storm
```
进入 /opt/software/apache-storm-0.9.6/conf中的storm.yaml文件
添加以下条目
 storm.zookeeper.servers:
 -     "127.0.0.1"
 nimbus.host: "127.0.0.1"
 storm.zookeeper.port: 2181
 storm.local.dir : "/home/linux/data"
 supervisor.slots.ports:
 -          6700
 -          6701
 -          6702
 -          6703
添加的时候注意，每一行必须有一个空格在前面，其中nimbus.host:后面有个空格，.local.dir后面的冒号前后都有空格，这个奇葩的设定让我浪费了半天的时间。
进入opt/software/apache-storm-0.9.6/bin中用以下命令启动
Storm nimbus
Storm supervisor
Storm ui
```
![这里写图片描述](http://img.blog.csdn.net/20161020193918834)

> 至此Storm的安装工作完成了
