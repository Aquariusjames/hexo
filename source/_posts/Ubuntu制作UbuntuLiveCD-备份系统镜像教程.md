---
title: Ubuntu制作UbuntuLiveCD+备份系统镜像教程
tags:
  - ubuntu
  - 教程
categories:
  - 解决方案
  - ubuntu
date: 2017-03-03 15:50:08
---

## 安装UbuntuLiveCD
> 准备：U盘，ubuntu官方镜像文件

### windows解决方案

- 安装UltralSO，打开ubuntu镜像文件，写入U盘
- 安装UNetBootin，打开Ubuntu镜像文件，写入U盘

### linux解决方案

- 插入U盘
- sudo fdisk -l
- cd ubuntu-iso的目录
- sudo dd if=ubuntu-14.04.5-desktop-amd64.iso of=/dev/sdX(sdX表示插入的U盘)

## 备份系统镜像

linux提供的dd命令可以直接复制磁盘的数据

- 使用U盘启动UbuntuLiveCD，类似WinPE系统
- sudo fdisk -l /dev/sdX(sdX代表你要克隆的磁盘)
![磁盘分区图](/images/ubuntu系统备份镜像-1.png)
如图所示，linux系统有三个分区分别是sda8 sda9 sda10,其中sda8 是交换分区，不用备份，只需要备份sda9和sda10空间

``` bash
 sudo dd bs=512 count=1953523712 skip=1646948352 if=/dev/sda of=savedPath/ghost.img
```

- 将ghost.img恢复到新硬盘

``` bash
dd if=savedPath/ghost.img of=/dev/sda
```

- 不要直接在计算机上用本地磁盘启动系统后执行dd命令生成本地磁盘的镜像。而应该用livecd启动计算机。因此计算机运行时会对系统盘产生大量写操作。 直接对运行中的系统盘生成的镜像，在恢复到其他硬盘上时，很可能会无法启动！

### 使用gzip进行压缩和解压缩

gzip参数：
-c 表示输出到stdout
-d  表示解压缩
-1 表示最快压缩
-9 表示最好压缩
默认使用的是-6压缩级别

dd备份命令：
``` bash
sudo dd bs=512 count=1953523712 skip=1646948352 if=/dev/sda | gzip -6 > /ghost.img.gz
```

dd还原命令：
``` bash
gzip -dc /ghost.img.gz.gz | dd of=/dev/sda
```
