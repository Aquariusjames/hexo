---
title: '[原]解决 Ubuntu14.04 系统WiFi经常掉线问题'
tags: [ubuntu, wifi, 系统bug]
categories: [解决方案,ubuntu]
date: 2017-01-17 08:49:43
---

首先使用$lspci -vv >> filename 命令来查看本机的硬件信息，查看网卡部分可以看到网卡驱动型号。我安装的Ubuntu14,04.5系统内核是4.4.0-31-generic, 系统自带的无线网卡驱动是Realt
ekRTL8723BE. 由于内核版本和无线网卡驱动版本不兼容导致无线网卡过一段时间就会出现自动休眠省电，因此需要进行设置来保证无线网卡的正常使用。

 解决方案：
 找到/etc/modprobe.d/rtl8723be.conf (没有的话自己新建)，
 写入下面的代码：
```  bash
 options rtl8723be ips=0 fwlps=0 swlps=0 swenc=1
```
 保存后重启就ok

 说明：

 - ips, "using no link power save (default 1 is open）
不使用链接省电 默认是1 默认是开启 就是启用省电。

 - fwlps, "using linked fw control power save (default 1 is open）
链接FW控制省电 默认是1 就是打开省电设置

 - swenc, "using hardware crypto (default 0 [hardware]）
硬件加密设置 默认是0
