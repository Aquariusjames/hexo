---
title: Hbase基本操作命令
tags:
  - hbase
  - 命令
categories:
  - 笔记
  - hbase
date: 2017-03-09 13:28:27
---

# 一、基本命令：

- 建表：create 'testtable','coulmn1','coulmn2'

- 也可以建表时加coulmn的属性如：
``` sql
create 'testtable',{NAME => 'coulmn1', BLOOMFILTER => 'NONE', REPLICATION_SCOPE => '0', VERSIONS => '10', COMPRESSION => 'LZO', TTL => '30000', IN_MEMORY => 'false', BLOCKCACHE => 'false'}, {NAME => 'coulmn', BLOOMFILTER => 'NONE', REPLICATION_SCOPE => '0', VERSIONS => '30', COMPRESSION => 'LZO', TTL => '30000', IN_MEMORY => 'true'}  (其中的属性有versions：设置历史版本数，TTL：过期时间，COMPRESSION：压缩方式，当配置lzo的情况)
```

- 删除表：drop 'testtable'   （删除表之前先要禁用表，命令disable 'testtable'）

- 启用和禁用表： enable 'testtable' 和disable 'testtable'

- 其它的基本命令：describe 'testtable'（查看表结构），alert 修改表结构，list 列出所有表。

- 查询前10条数据:

      scan'testtable',{COLUMNS=>'info',LIMIT=>10,STARTROW=>'666632331200000020160305',STOPROW=>'666632331200000020160308'}

## 分区合并

合并两个预分区，合并预分区要提供该区encode值，该值在16010hbase管理web可查

    merge_region 'region1的encode值','region2的encode值'

## 手动触发major_compact动作
cd /opt/hbase-1.2.1/bin
./hbase shell
major_compact 'table'
quit

## 手动触发flush动作
cd /opt/hbase-1.2.1/bin
./hbase shell
flush 'table1'
flush 'table2'
quit

## 每次hbase启动，最好设置一下参数，让hbase区自动均衡
cd /opt/hbase-1.2.1/bin
./hbase shell
balance_switch true

# 二、日常维护的命令

1，major_compact 'testtable'，通常生产环境会关闭自动major_compact(配置文件中hbase.hregion.majorcompaction设为0)，选择一个晚上用户少的时间窗口手工major_compact，如果hbase更新不是太频繁，可以一个星期对所有表做一次major_compact，这个可以在做完一次major_compact后，观看所有的storefile数量，如果storefile数量增加到major_compact后的storefile的近二倍时，可以对所有表做一次major_compact，时间比较长，操作尽量避免高锋期。

2，flush 'testtable'，将所有memstore刷新到hdfs，通常如果发现regionserver的内存使用过大，造成该机的regionserver很多线程block，可以执行一下flush操作，这个操作会造成hbase的storefile数量剧增，应尽量避免这个操作，还有一种情况，在hbase进行迁移的时候，如果选择拷贝文件方式，可以先停写入，然后flush所有表，拷贝文件。

3，balance_switch true或者balance_switch flase，配置master是否执行平衡各个regionserver的region数量，当我们需要维护或者重启一个regionserver时，会关闭balancer，这样就使得region在regionserver上的分布不均，这个时候需要手工的开启balance。

# 三、重启一个regionserver

bin/graceful_stop.sh --restart --reload --debug nodename

这个操作是平滑的重启regionserver进程，对服务不会有影响，他会先将需要重启的regionserver上面的所有region迁移到其它的服务器，然后重启，最后又会将之前的region迁移回来，但我们修改一个配置时，可以用这种方式重启每一台机子，这个命令会关闭balancer，所以最后我们要在hbase shell里面执行一下balance_switch true，对于hbase regionserver重启，不要直接kill进程，这样会造成在zookeeper.session.timeout这个时间长的中断，也不要通过bin/hbase-daemon.sh stop regionserver去重启，如果运气不太好，-ROOT-或者.META.表在上面的话，所有的请求会全部失败。

# 四、关闭下线一台regionserver

bin/graceful_stop.sh --stop  nodename

和上面一样，系统会在关闭之前迁移所有region，然后stop进程，同样最后我们要手工balance_switch true，开启master的region均衡。

# 五、检查region是否正常以及修复

bin/hbase hbck  (检查)

bin/hbase hbck -fix  （修复）

会返回所有的region是否正常挂载，如没有正常挂载可以使用下一条命令修复，如果还是不能修复，那需要看日志为什么失败，手工处理。

# 六、hbase的迁移

1，copytable方式

bin/hbase org.apache.hadoop.hbase.mapreduce.CopyTable --peer.adr=zookeeper1,zookeeper2,zookeeper3:/hbase 'testtable'

目前0.92之前的版本的不支持多版本的复制，0.94已经支持多个版本的复制。当然这个操作需要添加hbase目录里的conf/mapred-site.xml，可以复制hadoop的过来。

2，Export/Import

bin/hbase org.apache.hadoop.hbase.mapreduce.Export testtable /user/testtable [versions] [starttime] [stoptime]

bin/hbase org.apache.hadoop.hbase.mapreduce.Import testtable  /user/testtable

跨版本的迁移，我觉得是一个不错的选择，而且copytable不支持多版本，而export支持多版本，比copytable更实用一些。

3，直接拷贝hdfs对应的文件

首先拷贝hdfs文件，如bin/hadoop distcp hdfs://srcnamenode:9000/hbase/testtable/ hdfs://distnamenode:9000/hbase/testtable/

然后在目的hbase上执行bin/hbase org.jruby.Main bin/add_table.rb /hbase/testtable

生成meta信息后，重启hbase

这个操作是简单的方式，操作之前可以关闭hbase的写入，执行flush所有表（上面有介绍）,再distcp拷贝，如果hadoop版本不一致，可以用hftp接口的方式，我推荐使用这种方式，成本低。
