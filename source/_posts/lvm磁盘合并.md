---
title: lvm磁盘合并
tags:
  - 笔记
  - linux
categories:
  - 笔记
date: 2018-07-03 12:05:07
---

- 显示逻辑卷组
 sudo vgdisplay
- 显示逻辑卷
 sudo lvdisplay
- 显示磁盘
 sudo fdisk -l
- 创建物理卷
 sudo pvcreate /dev/sdb
- 显示物理卷信息
 sudo pvdisplay
- 物理卷/dev/sdb加入卷组vg-root
 sudo vgextend vg-root /dev/sdb
- 增大逻辑卷root 1T
 sudo lvextend -L +1T /dev/ubuntu-vg/root
 sudo lvextend -l +100%FREE /de/ubuntu-vg/root
- 调整文件系统大小
 sudo resize2fs /dev/ubuntu-vg/root
- 查看结果
 df -h 

issue:

1. [remove bad disk](https://serverfault.com/questions/319427/how-to-remove-bad-disk-from-lvm2-with-the-less-data-loss-on-other-pvs)