---
title: ubuntu安装hadoop和hbase教程
tags:
  - 教程
  - ubuntu
  - hadoop
  - hbase
  - 大数据
categories:
  - 解决方案
  - hadoop
date: 2017-02-17 11:19:07
---

## ubuntu安装hadoop和hbase教程

### 创建hadoop用户和hadoop用户组

    sudo addgroup hadoop
    sudo adduser -ingourp hadoop hadoop
    sudo gedit /etc/sudoers

最后显示结果如下：
![图片1](/images/ubuntu安装hadoop和hbase教程-1.png)

### 安装ssh免密码登录

[见此文](https://xiongraorao.github.io/2017/02/17/ssh%E5%85%8D%E5%AF%86%E7%A0%81%E7%99%BB%E5%BD%95/)

### 配置java环境
[见此文](https://xiongraorao.github.io/2016/12/05/%E5%8E%9F-Ubuntu%E4%B8%8B%E9%85%8D%E7%BD%AEjava-Eclipse%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83/#1、复制文件到待安装目录)

### 安装hadoop2
- 官网下载： [http://mirror.bit.edu.cn/apache/hadoop/common/](http://mirror.bit.edu.cn/apache/hadoop/common/)
- 安装,修改文件拥有者为hadoop,修改权限为774

        sudo chown -R hadoop:hadoop hadoop-install-path
        sudo chmod 774 hadoop-install-path
- 配置环境变量

``` bash
#HADOOP VARIABLES START
export JAVA_HOME=/usr/lib/jvm/java­7­openjdk­amd64 #注意修改路径
export HADOOP_INSTALL=/usr/local/hadoop
export PATH=$PATH:$HADOOP_INSTALL/bin
export PATH=$PATH:$HADOOP_INSTALL/sbin
export HADOOP_MAPRED_HOME=$HADOOP_INSTALL
export HADOOP_COMMON_HOME=$HADOOP_INSTALL
export HADOOP_HDFS_HOME=$HADOOP_INSTALL
export YARN_HOME=$HADOOP_INSTALL
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_INSTALL/lib/native
export HADOOP_OPTS="­Djava.library.path=$HADOOP_INSTALL/lib"
#HADOOP VARIABLES END
```

### 单机配置（非分布式）
执行如下操作：
``` bash
cd /usr/local/hadoop
$ mkdir ./input
$ cp ./etc/hadoop/*.xml ./input # 将配置文件作为输入文件
$ ./bin/hadoop jar ./share/hadoop/mapreduce/hadoop­mapreduce­examples­*.jar grep ./input ./output 'dfs[az.]+'
$ cat ./output/* # 查看运行结果
```
**<span style="color:red;background-color:yellow;">注意hadoop不会覆盖结果文件，因此需要删除./output</span>**

### 伪分布式配置（单台机器模拟多个节点）
需要修改两个配置文件：core-site.xml hdfs-site.xml

-  修改 core-sit.xml
``` xml
<configuration>
        <property>
             <name>hadoop.tmp.dir</name>
             <value>file:/usr/local/hadoop/tmp</value>
             <description>Abase for other temporary directories.</description>
        </property>
        <property>
             <name>fs.defaultFS</name>
             <value>hdfs://localhost:9000</value>
        </property>
</configuration>
```

- 修改配置文件 hdfs-site.xml
``` xml
<configuration>
        <property>
             <name>dfs.replication</name>
             <value>1</value>
        </property>
        <property>
             <name>dfs.namenode.name.dir</name>
             <value>file:/usr/local/hadoop/tmp/dfs/name</value>
        </property>
        <property>
             <name>dfs.datanode.data.dir</name>
             <value>file:/usr/local/hadoop/tmp/dfs/data</value>
        </property>
</configuration>
```
- 执行NameNode初始化

        ./bin/hdfs namenode -format

  成功的话，会看到 “successfully formatted” 和 “Exitting with status 0” 的提示，若为 “Exitting with status 1” 则是出错。

- 开启NameNode 和 DataNode守护进程

        ./sbin/start-dfs.sh
  通过$jps命令可以查看是否启动相关进程（NameNode，DataNode,SecondaryNameNode）


- 成功启动后在浏览器输入[http://localhost:50070](http://localhost:50070)可以查看NameNode和DataNode信息

*注意：启动失败可以尝试如下操作*

``` bash
# 针对 DataNode 没法启动的解决方法
./sbin/stop-dfs.sh   # 关闭
rm -r ./tmp     # 删除 tmp 文件，注意这会删除 HDFS 中原有的所有数据
./bin/hdfs namenode -format   # 重新格式化 NameNode
./sbin/start-dfs.sh  # 重启
```
### 启动YARN
>  伪分布式不一定要启动YARN

启动Yarn来管理资源，负责任务调度

- 修改配置文件 mapred-site.xml

        mv ./etc/hadoop/mapred­site.xml.template ./etc/hadoop/mapred­site.xml
``` xml
<configuration>
 <property>
 <name>mapreduce.framework.name</name>
 <value>yarn</value>
 </property>
</configuration>
```

- 修改配置文件 yarn-site.xml
``` xml
<configuration>
 <property>
 <name>yarn.nodemanager.aux­services</name>
 <value>mapreduce_shuffle</value>
 </property>
</configuration>
```

- 启动yarn

        ./sbin/start­-yarn.sh # 启动YARN
        ./sbin/mr­jobhistory­daemon.sh start historyserver # 开启历史服务器，才能在Web中查看任务运行情况

开启jps，出现NodeManager和ResourceManager两个后台进程，通过web界面[ResourceManager](http://localhost:8088/cluster) 可以查看任务运行情况

- 关闭yarn
        ./sbin/stop-yarn.sh
        ./sbin/mr­jobhistory­daemon.sh stop historyserver

**注意:不启动 YARN 需重命名 mapred­site.xml如果不想启动 YARN，务必把配置文件 mapred­site.xml 重命名，改成 mapred­site.xml.template，需要用时改回来就行。否则在该配置文件存在，而未开启 YARN 的情况下，运行程序会提示 “Retrying connect to server: 0.0.0.0/0.0.0.0:8032” 的错误，这也是为何该配置文件初始文件名为 mapred­site.xml.template。**
