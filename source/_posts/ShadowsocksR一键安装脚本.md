---
title: ShadowsocksR一键安装脚本
tags:
  - 笔记
  - shadowsocks
categories:
  - 笔记
date: 2018-06-25 10:56:31
---

# ShadowsocksR一键安装脚本

默认配置：
服务器端口：自己设定（如不设定，默认从 9000-19999 之间随机生成）
密码：自己设定（如不设定，默认为 teddysun.com）
加密方式：自己设定（如不设定，默认为 aes-256-cfb）
协议（Protocol）：自己设定（如不设定，默认为 origin）
混淆（obfs）：自己设定（如不设定，默认为 plain）

客户端下载：
[github shadowsock](https://github.com/shadowsocks)

# 安装步骤

``` bash
wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocksR.sh
chmod +x shadowsocksR.sh
./shadowsocksR.sh 2>&1 | tee shadowsocksR.log
```

# 卸载步骤

    ./shadowsocksR.sh uninstall

# 多用户配置

``` json
{
"server":"0.0.0.0",
"server_ipv6": "[::]",
"local_address":"127.0.0.1",
"local_port":1080,
"port_password":{
    "8989":"password1",
    "8990":"password2",
    "8991":"password3"
},
"timeout":300,
"method":"aes-256-cfb",
"protocol": "origin",
"protocol_param": "",
"obfs": "plain",
"obfs_param": "",
"redirect": "",
"dns_ipv6": false,
"fast_open": false,
"workers": 1
}
```