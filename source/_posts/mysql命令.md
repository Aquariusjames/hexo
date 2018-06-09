---
title: mysql命令
tags:
  - mysql
  - 数据库
categories:
  - 笔记
  - mysql
date: 2017-04-24 19:50:40
---

## 1、mysql的安装
[linux下mysql安装-博客园](https://www.cnblogs.com/xinjing-jingxin/p/8025805.html)


## 2、用户管理
启动服务：
net start mysqld
链接数据库：
mysql -uroot -p
修改root用户密码：
mysqladmin -u root password "new_password";

### 2.1新建用户
mysql -uroot -p //登录mysql
mysql> insert into mysql.user(Host,User,Password) values("localhost","test",password("1234"));
//localhost表示只能在本机登录，改为"%"则可以远程登录

或者：
mysql> CREATE USER 'username'@'host' IDENTIFIED BY 'password';

### 2.2用户授权
grant 权限 on 数据库.* to 'username'@'host' identified by 'password'
权限：select, update, all, delete, create, drop

### 2.3删除用户
Delete FROM user Where User='test' and Host='localhost';
flush privileges;
drop database 用户数据库名;
drop user 用户名@'%'
drop user 用户名@‘localhost’

### 2.4修改用户密码
mysql>update mysql.user set password=password('新密码') where User="test" and Host="localhost";
mysql>flush privileges;

在5.7版本的mysql中，没有“password”这个字段，因此需要使用“authentication_string”这个字段来替换“password”字段

或者采用以下命令：ALTER USER 'root'@'localhost'IDENTIFIED
 BY '********'

### 2.5撤销用户权限
REVOKE 权限 ON databasename.tablename FROM 'username'@'host';

### 2.6常用命令
show databases;//列出所有数据库
use 'databasename';//切换数据库
show tables;//列出所有表
describe tablename;//显示数据表结构
drop database 数据库名;//删除数据库
drop database 表名;//删除表

### 2.7重置root密码
- 修改安装目录下的my.ini文件，添加一行：skip-grant-tables
- 重启mysql服务：
windows(管理员模式):net stop mysql; net start mysql;

- mysql -uroot -p(直接回车登录)
- update mysql.user set authentication_string=password('新密码') where User="root"
- 注释my.ini文件中skip-grant-tables这一行
- 重启mysql服务

## 3.常见问题
- 远程连接问题：
> 解决方案如下：
1、授权
>mysql>grant all privileges on *.*  to  'root'@'%'  identified by 'youpassword'  with grant option;
mysql>flush privileges;
2、修改/etc/mysql/my.conf
找到bind-address = 127.0.0.1这一行,注释即可

- 重启mysql服务
``` bash
[root@localhost /]# service mysqld start (5.0版本是mysqld)
[root@szxdb etc]# service mysql start (5.5.7版本是mysql)
```
