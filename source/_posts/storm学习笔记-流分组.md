---
title: storm学习笔记--流分组
tags:
  - storm
  - 大数据
categories:
  - 笔记
  - storm
date: 2017-02-28 10:18:54
---

### storm 数据流分组
- Shuffle grouping:随机分组，保证每个bolts得到相同数量的tuple
- Fields grouping：根据上一个bolts发出的tuple的字段来分组，同一字段的tuple被分到同一个Task里面
- Partical Key grouping： 同Fields grouping,增加负载均衡策略
- All grouping: 每个bolt的task都执行一遍上一级的bolt发射的tuples，慎重选择
- Global grouping：全局分组，tuple被分配到bolt中的一个task，实现事务性的topology，一般来讲所有的tuple都被分配到拥有**最小task_id**的bolt任务处理
- None grouping：不分组，不关注并行处理、负载均衡策略时采用该方式分组，目前等同于shuffle grouping，另外storm会把bolt任务和他的上游提供数据的任务安排在同一个线程下
- Direct grouping：指定分組，只有被声明为DirectStream的消息流才能用这种分组方法，而且这种消息tuple必须使用emitDirect方法来发射tuple
- Local or shuffle grouping：如果目标bolt在一个work进程中一个或者多个task，那么tuples将会被随机分派到这个进程中的tasks，否则，如同shuffle grouping

### storm 分组策略及代码实现
[参考链接](http://www.cnblogs.com/kqdongnanf/p/4634607.html)
