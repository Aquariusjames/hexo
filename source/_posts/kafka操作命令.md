---
title: kafka操作命令
tags:
  - kafka
categories:
  - 笔记
  - kafka
date: 2017-03-16 16:45:49
---
# kafka常用操作命令

``` bash

## 1 创建新的topic
kafka-topic --create --zookeeper zkhost:2181/kafka --replication-factor 1 --partitions 3 --topic topic-name

## 2 查询所有的topic
kafka-topics --list --zookeeper zkhost:2181/kafka

## 3 查看topic详细信息
kafka-topics --describe --zookeeper zkhost:2181/kafka --topic topicname

## 4 生产消息
kafka-console-producer --broker-list kafkahost:9092 --topic topicname

## 5 消费消息
kafka-console-consumer --bootstrap-server kafkahost:9092 --topic topicname

```
