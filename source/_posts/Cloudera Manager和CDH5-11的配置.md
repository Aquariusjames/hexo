---
title: ubuntu14.04下 Cloudera Manager和CDH5.11的配置
tags:
  - hadoop
  - CDH5
  - Cloudera Manager
categories:
  - 解决方案
  - hadoop
date: 2017-05-23 11:25:25
---

本教程用于搭建Clodera Manager和CDH5的大数据分析平台的搭建工作

## 1.参考链接：
http://www.cnblogs.com/codedevelop/p/6762555.html （博客园）
http://blog.csdn.net/a921122/article/details/51939692 （csdn）
http://blog.csdn.net/hualiu163/article/details/46659375 （CDH5.33 详细）
http://www.aboutyun.com/thread-9075-1-1.html （在线安装，不推荐）

基本思路：
集群主机ip配置，关闭防火墙，设置ssh无密码登录，统一root密码，集群时间同步，安装oracle jdk，主节点安装mysql，并初始化scm数据库，下载parcel包，进入控制台设置等

## 2.注意的问题：
jdbc的问题：
安装hive时，需要将mysql驱动包复制到/opt/cloudera/parcels/CDH/lib/hive/lib/目录下，并且需要把mysql驱动复制到/usr/share/java/mysql-connector-java.jar下

目录权限问题，ssh免密码登录问题，mysql远程访问设置。。。

注意要添加root用户的访问host，不然后面初始化会出问题
grant all privileges on *.* to 'root'@'n1' identified by 'xxxx' with grant option;

## 3、官方安装教程
官方手动安装教程（强烈建议按照官方教程来操作，可以少走很多坑，阿西吧）：
https://www.cloudera.com/documentation/enterprise/latest/topics/cm_ig_install_path_c.html#id_v4k_pnn_25
system requirement:
https://www.cloudera.com/downloads/manager/5-11-0.html

## 4.主节点安装mysql5.6
在线安装：http://blog.csdn.net/chenpy/article/details/50344085
离线安装：http://blog.csdn.net/linlinv3/article/details/51774040
注意要设置远程登录（具体参照官方教程：[MySQL设置](https://www.cloudera.com/documentation/enterprise/latest/topics/cm_ig_mysql.html#cmig_topic_5_5_2)）

创建数据库：
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

master节点交换内存设置：
http://blog.chinaunix.net/uid-20051192-id-3557817.html

这里注意下，官网教程上面的reboot后自动启动的命令有点问题，直接启动和命令启动的时候运行的进程不一致
update-rc.d -f cloudera-scm-server remove
update-rc.d -f cloudera-scm-agent remove
貌似不能随便去掉这个过程，否则后面又会出现这个问题


安装kafka的问题：
首先参考官方教程，分配kafka parcel包，然后安装，第一次安装会遇到问题，解决方案如下：
http://www.aboutyun.com/thread-19903-1-1.html
