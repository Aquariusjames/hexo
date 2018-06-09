---
title: 'Ubuntu系统操作命令笔记（持续更新...）'
tags:
  - ubuntu
  - 笔记
categories:
  - IT
  - linux
date: 2017-05-24 09:14:21
---

### 1、修改用户密码
```
sudo passwd  =>>输入new-root-password
```
### 2、切换用户
```
su username  =>>输入待切换用户的密码
```
### 3、提升权限
```
su root (临时提升权限)
sudo gedit /etc/passwd (永久提升权限)
```
### 4、设置环境变量

- 为单个用户设置（～/.bashrc）
- 全局设置（/etc/profile）
- /etc/bashrc. 该文件是为每一个运行的shell用户执行操作，当bashshell被打开是，文件被读取
- ～/bash_profile: 每个用户可以使用该文件输入专用于自己的shell信息
- /etc/environment: 在登录的时候操作系统使用的的第二个文件，系统在读取你的profile之前，设置文件的环境变量

### 5、ssh使用
1.登录远程主机
ssh username@host [-p port]  =>input your host password
equals (ssh -l username host)
2.文件传输
scp username@serverhost:/serverfile /save path (download from server)
scp /local file username@serverhost:/server save path(upload to server)
从服务器下载或者上传整个目录
scp -r username@serverhost:/remote directory /local directory
scp -r /local directory username@serverhost:/remote directory
3.开启无密码ssh登录
client A; server B

- A: ssh-keygen -t rsa (三次回车，在～/.ssh目录下生成id_ras，id_rsa.pub两个文件，分别是私钥和公钥)
- A: cat ~/.ssh/id_ras.pub >> ~/.ssh/authorized-keys（复制公钥到文件authorized-keys）
- A: scp ~/.ssh/id_rsa.pub XXX@remotehost:/home/XXX/id_rsa.pub
- B: cat ~/id_rsa.pub >>~/.ssh/authorized_keys(追加公钥到服务器的authorizied_keys文件中，**注意登录到远程服务器操作此步骤**)
- A: ssh-add  ~/.ssh/id_rsa (添加私钥)
- 更改权限: chmod 700 ~/.ssh; chmod 600 ~/.ssh/authorized_keys

### 6、更改文件权限和所有者
chmod [options] mode files/directory
mode=u,g,o 7=4读+2写+1执行，u用户，g，用户组，o，其他
+，-表示增加或者删除权限r,w,x表示读，写，执行三种权限，对应上面的三个数字。加上-R参数后就可以修改整个目录的文件的权限了。
chown username file/directory 更改文件的所有者

### 7、系统查看指令
tail -n number file 查看文件末尾number行
cat file 查看文件所有内容
ls -lh查看文件详细情
du -lh查看单独文件的大小
df -lh查看磁盘分区
gnome-system-monitor 查看资源管理器

### 8、用户和用户组管理
http://www.cnblogs.com/xd502djj/archive/2011/11/23/2260094.html

### 9、修改locale
http://dgd2010.blog.51cto.com/1539422/1684813
http://blog.csdn.net/myweishanli/article/details/23576847
