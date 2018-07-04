---
title: chrome解决http自动跳转https的问题
tags:
  - 笔记
categories:
  - 笔记
date: 2018-07-03 12:06:39
---

1.地址栏输入： chrome://net-internals/#hsts

2.找到底部Delete domain security policies一栏，输入想处理的域名，点击delete。

![delete](https://images2017.cnblogs.com/blog/37001/201712/37001-20171214102656972-118528744.png)

3.搞定了，再次访问http域名不再自动跳转https了