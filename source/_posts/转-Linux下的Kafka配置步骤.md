---
title: '[转]Linux下的Kafka配置步骤'
tags: [kafka, linux, 教程]
categories: [解决方案, kafka]
date: 2016-12-05 16:59:19
---

转载自：[http://blog.csdn.net/suifeng3051/article/details/38321043](http://blog.csdn.net/suifeng3051/article/details/38321043)

Kafka集群配置比较简单，为了更好的让大家理解，在这里要分别介绍下面三种配置

- 单节点：一个broker的集群
- 单节点：多个broker的集群
- 多节点：多broker集群

# 一、单节点单broker实例的配置
![Zookeeper 管理Kafka Producer和Consumer](http://img.blog.csdn.net/20140731174519164?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VpZmVuZzMwNTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 1. 首先启动zookeeper服务
<font color=ff0000>**必须使用root用户启动kafka**</font>
Kafka本身提供了启动zookeeper的脚本（在kafka/bin/目录下）和zookeeper配置文件（在kafka/config/目录下），首先进入Kafka的主目录（可通过 whereis kafka命令查找到）：
```
[root@localhost kafka-0.8]# bin/zookeeper-server-start.sh config/zookeeper.properties
zookeeper配置文件的一些重要属性:
# Data directory where the zookeeper snapshot is stored.
dataDir=/tmp/zookeeper
# The port listening for client request
clientPort=2181
默认情况下，zookeeper服务器会监听 2181端口，更详细的信息可去zookeeper官网查阅。
```

## 2. 启动Kafka broker
运行kafka提供的启动kafka服务脚本即可：
```
[root@localhost kafka-0.8]# bin/kafka-server-start.sh config/server.properties
broker配置文件中的重要属性：
# broker的id. 每个broker的id必须是唯一的.
Broker.id=0
# 存放log的目录
log.dir=/tmp/kafka8-logs
# Zookeeper 连接串
zookeeper.connect=localhost:2181
```

## 3. 创建一个仅有一个Partition的topic  

		[root@localhost kafka-0.8]# bin/kafka-create-topic.sh --zookeeper localhost:2181 --replica 1 --partition 1 --topic kafkatopic


## 4. 用Kafka提供的生产者客户端启动一个生产者进程来发送消息  

		[root@localhost kafka-0.8]# bin/kafka-console-producer.sh --broker-list localhost:9092 --topic kafkatopic
其中有两个参数需要注意：

- broker-list:定义了生产者要推送消息的broker地址，以<IP地址:端口>形式
- topic：生产者发送给哪个topic

## 5. 启动一个Consumer实例来消费消息  

		[root@localhost kafka-0.8]# bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic kafkatopic --from-beginning

当你执行这个命令之后，你便可以看到控制台上打印出的生产者生产的消息：
![这里写图片描述](http://img.blog.csdn.net/20140731173902203?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VpZmVuZzMwNTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

和消费者相关的属性配置存放在Consumer.properties文件中，重要的属性有：
     # consumer的group id (A string that uniquely identifies a set of consumers
     # within the same consumer group)
     groupid=test-consumer-group
     # zookeeper 连接串
     zookeeper.connect=localhost:2181

# 二、单节点运行多broker实例  

前面的步骤和单节点运行但broker实例一样

## 1、启动zookeeper
## 2、启动Kafka的broker
要想在一台机器上启动多个broker实例，只需要准备多个server.properties文件即可，比如我们要在一台机器上启动两个broker：
首先我们要准备两个server.properties配置文件
```
server-1
brokerid=1
port=9092
log.dir=/temp/kafka8-logs/broker1    
server-2
brokerid=2
port=9093
log.dir=/temp/kafka8-logs/broker2
```
然后我们再用这两个配置文件分别启动一个broker
```
[root@localhost kafka-0.8]# env JMX_PORT=9999 bin/kafka-server-start.sh config/server-1.properties
[root@localhost kafka-0.8]# env JMX_PORT=10000 bin/kafka-server-start.sh config/server-2.properties
```
<font color=#ff0000>可以看到我们启动是为每个broker都指定了不同的JMX Port，JMX Port主要用来利用jconsole等工具进行监控和排错</font>

## 3.创建一个topic
现在我们要创建一个含有两个Partition分区和2个备份的broker：

     [root@localhost kafka-0.8]# bin/kafka-create-topic.sh --zookeeper localhost:2181 --replica 2 --partition 2 --topic othertopic


## 4.启动Producer发送消息

如果我们要用一个Producer发送给多个broker，唯一需要改变的就是在broker-list属性中指定要连接的broker：

		[root@localhost kafka-0.8]# bin/kafka-console-producer.sh --broker-list localhost:9092,localhost:9093 --topic othertopic

 如果我们要让不同的Producer发送给不同的broker，我们也仅仅需要为每个Producer配置响应的broker-list属性即可。

## 5.启动一个消费者来消费消息
和之前的命令一样

		[root@localhost kafka-0.8]# bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic othertopic --from-beginning

## 三、集群模式（多节点多实例）
介绍了上面两种配置方法，再理解集群配置就简单了，比如我们要配置如下图所示集群：
zookeeper配置文件（zookeeper.properties）：不变
broker的配置配置文件(server.properties)：按照单节点多实例配置方法在一个节点上启动两个实例，不同的地方是zookeeper的连接串需要把所有节点的zookeeper都连接起来
  # Zookeeper 连接串
  zookeeper.connect=node1:2181,node2:2181

