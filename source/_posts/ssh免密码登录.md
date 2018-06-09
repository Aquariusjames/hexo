---
title: ssh免密码登录
date: 2017-02-17 13:45:04
tags:
  - ssh
  - 教程
  - ubuntu
categories:
  - 解决方案
  - ubuntu
---

### 单机配置方式
客户端（A）免密码登录服务器（B）

- A: ssh-keygen -t rsa
- A: cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
- A: scp ~/.ssh/id_rsa.pub XXX@remotehost:/home/XXX/pubfile
- B: ssh-keygen -t rsa
- B: cat ~/pubfile >> ~/.ssh/authorized_keys
- A: ssh-add ~/.ssh/id_rsa
- A: chmod 700 ~/.ssh
- A: chmod 600 ~/.ssh/authorized_keys

### 集群配置方式
``` bash
#在cluster1上操作。并且假设共有3个集群
ssh-keygen -t rsa #生成密钥对
for ip in `seq 2 3`; do ssh-copy-id -i .ssh/id_rsa.pub uname@192.168.100.10$ip; done #这里集群的ip地址为192.168.100.101~103

#同样在cluster2和cluster3上做同样的操作
```

### 安装ansible
ansible是一个很方便的集群管理工作，打通了ssh之后，可以在集群上面操作同样的命令

``` bash
sudo apt-get install ansible
cd /etc/ansible/
vi hosts #修改组名称
ansible groupId -m shell -a 'shell commands;' #shell命令

# 查看某组的目标机器上的当前时间
ansible groupname -a 'date' 数据某组上所有目标机器的当前时间


```
