---
title: docker 远程构建
date: 2018-06-20 15:01:58
tags: docker
---

# docker 远程连接
    
docker server和client是分开的，因此支持远程连接

# dockerd 设置

方法1： 修改 /etc/default/docker 文件

        DOCKER_OPTS="-H unix:///var/run/docker.sock -H tcp://0.0.0.0:2375" 

方法2： 修改 /etc/systemd/system/docker.service 文件

        ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2375

重启docker服务

        systemctl daemon-reload && systemctl restart docker

# TSL 认证

如果主机在公网环境，则需要使用TSL认证

参考资料：

- [Docker Daemon连接方式详解](https://www.jianshu.com/p/7ba1a93e6de4)
