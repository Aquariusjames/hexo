---
title: linux磁盘分区和挂载
tags:
  - linux
categories:
  - 解决方案
  - linux
date: 2017-03-18 10:11:57
---

## 磁盘分区

### 显示磁盘和分区情况
sudo fdisk -l

``` bash
Disk /dev/xvda: 42.9 GB, 42949672960 bytes
255 heads, 63 sectors/track, 5221 cylinders, total 83886080 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00054506

    Device Boot      Start         End      Blocks   Id  System
/dev/xvda1   *        2048    75495423    37746688   83  Linux
/dev/xvda2        75497470    83884031     4193281    5  Extended
/dev/xvda5        75497472    83884031     4193280   82  Linux swap / Solaris

Disk /dev/xvdb: 107.4 GB, 107374182400 bytes
255 heads, 63 sectors/track, 13054 cylinders, total 209715200 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

Disk /dev/xvdb doesn\'t contain a valid partition table

```

### 对磁盘进行分区操作
sudo fdisk /dev/xvdb

``` bash
Command (m for help): m     ## 打印命令列表
Command action
   a   toggle a bootable flag   # 将分区标记为可启动盘
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition       # 删除一个分区
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition\'s system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)
```
输入n，增加硬盘一个新分区
选择e，指定分区为扩展分区（extended）
出现Partition number(1-4)时，输入１表示只分一个区
输入p，打印分区表
``` bash
Disk /dev/xvdb: 107.4 GB, 107374182400 bytes
255 heads, 63 sectors/track, 13054 cylinders, total 209715200 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0xf88fabb6

    Device Boot      Start         End      Blocks   Id  System
/dev/xvdb1            2048   209715199   104856576    5  Extended
```
### 硬盘格式化操作
sudo mkfs -t ext4 /dev/xvdb

``` bash
mkfs 命令的语法如下：
mkfs [-V] [-t fstype] [fs-options] filesys
说明：
-V   显示简要的使用方法。
-t    指定要建立何种文件系统，如：ext3, ext4。
fs   指定建立文件系统时的参数。
-v   显示版本信息与详细的使用方法。
```

### 挂载硬盘分区
sudo mount -t ext4 /dev/xvdb /deb/sdb #指定硬盘分区文件系统为ext4，将/dev/xvdb 分区挂载到/dev/sdb

sudo df -h #查看分区挂载情况
``` bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1       36G  2.3G   32G   7% /
none            4.0K     0  4.0K   0% /sys/fs/cgroup
udev            476M   12K  476M   1% /dev
tmpfs            98M  392K   97M   1% /run
none            5.0M  4.0K  5.0M   1% /run/lock
none            486M     0  486M   0% /run/shm
none            100M     0  100M   0% /run/user
/dev/xvdb        99G   60M   94G   1% /dev/sdb
```
``` bash
# mount命令详解
mount [-afFnrsvw] [-t vfstype] [-L label]  [-o options] device dir
mount [-lhv]
说明：
-a    加载文件/etc/fstab中设置的所有设备。
-f     不实际加载设备。可与-v等参数同时使用以查看mount的执行过程。
-F    需与-a参数同时使用。所有在/etc/fstab中设置的设备会被同时加载，可加快执行速度。
-t vfstype   指定加载的文件系统类型，如：ext3, ext4。
-L label      给挂载点指定一个标签名称。
-l     显示分区的label。
-h    显示帮助信息。
-v    显示mount的版本信息。
device       要挂载的分区或文件。如果device是一个文件，挂载时须加上 -o loop参数。
dir             分区的挂载点。
```
### 设置开机自动挂载
sudo vi /etc/fstab

UUID=XXXXX /dev/sdb ext4 defaults 0 3

UUID获取命令： ls -l /dev/disk/by-uuid/  或者 blkid /dev/xvdb


fstab配置详解：
/etc/fstab 中一共有６列：
- file system：指定要挂载的文件系统的设备名称（如：/dev/sdb）。也可以采用UUID，UUID可以通过使用blkid命令来查看（如：blkid  /dev/sdb）指定设备的UUID号。
- mount point：挂载点。就是自己手动创建一个目录，然后把分区挂载到这个目录下。
- type：用来指定文件系统的类型。如：ext3, ext4, ntfs等。
- option dump：0表示不备份；１表示要将整个中的内容备份。此处建议设置为0。
- pass：用来指定fsck如何来检查硬盘。0表示不检查；挂载点为分区／（根分区）必须设置为1，其他的挂载点不能设置为1；如果有挂载ass设置成大于1的值，则在检查完根分区后，然后按pass的值从小到大依次检查，相同数值的同时检查。如：/home　和 /boot　的pass 设置成2，/devdata 的pass 设置成3，则系统在检查完根分区，接着同时检查/boot和/home，再检查/devdata。
