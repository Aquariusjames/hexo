---
layout: java
title: hexo 博客遇到的坑--host key verification failed
date: 2018-10-24 08:44:39
tags: 
  - hexo
  - bug
---

使用`hexo d`进行部署的时候，会遇到如下的报错信息：

Host Key Verification failed ....

原因：ssh链接的时候没有加入到known_host 里面去，因此需要执行如下命令：

``` bash
ssh -T git@github.com

The authenticity of host 'github.com (192.30.252.1)' can't be established.
RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
are you sure you want to continue connecting (yes/no)?'

yes

# 如果有自己的服务器的话，同样需要执行
ssh root@your.server.ip 

yes

```

tips：遇到坑，先分析原因，分析不出来就google，==