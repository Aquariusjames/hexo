---
layout: java
title: 基于redis的分布式锁
date: 2018-12-08 15:24:11
tags: 
  - redis
  - 分布式锁
---


# 定义

分布式锁是控制分布式系统之间的同步访问共享资源的一种方式。

常规的Lock 和 Synchronized 只能在一个jvm中实现锁同步。

## 分布式锁的条件

- 互斥性：分布式锁需要保证在不同节点的不同线程的互斥
- 可重入性：同一个节点上的同一个线程如果获取了锁之后，能够再次获取这个锁。
- 锁超时：支持超时释放锁，防止死锁
- 高效，高可用：加锁和解锁需要高效，同时也需要保证高可用防止分布式锁失效，可以增加降级。
- 支持阻塞和非阻塞：可以实现超时获取失败，tryLock(long timeOut)
- 支持公平锁和非公平锁

## 分布式锁的实现方案

1、数据库实现（乐观锁）
2、基于zookeeper的实现
3、基于Redis的实现（推荐）
4、自研分布式锁：谷歌的chubby

# 基于redis的分布式锁的实现

## 常用命令

1、 SETNX key value
如果 key 不存在，就试着key对应的字符串value

2、expire key seconds
设置key的过去时间，如果key过期，则会自动删除

3、del Key
删除这个key

## 基本锁

原理：利用redis的setnx，如果不存在某个key则设置，设置成功表示取得锁成功。
缺点：如果互斥锁后的进程在没有执行完就挂了，那么所永远不会被释放

==改进==
设置完setnx之后，利用expire操作保证超时后也能释放，但是setnx和expire不是一个原子操作，可能执行完setnx就挂了，需要使用Lua脚本来实现这个问题。


## 开源实现

Redisson 分布式可重如锁

https://github.com/redisson/redisson 

官网：http://redisson.org

