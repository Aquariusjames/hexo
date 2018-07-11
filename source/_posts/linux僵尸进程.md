---
title: linux僵尸进程
tags:
  - 笔记
  - linux
categories:
  - 笔记
date: 2018-07-11 10:52:57
---

# linux僵尸进程

原因：

父进程调用fork创建子进程后，子进程运行直至其终止，它立即从内存中移除，但进程描述符仍然保留在内存中（进程描述符占有极少的内存空间）。子进程的状态变成EXIT_ZOMBIE，并且向父进程发送SIGCHLD 信号，父进程此时应该调用 wait() 系统调用来获取子进程的退出状态以及其它的信息。在 wait 调用之后，僵尸进程就完全从内存中移除。因此一个僵尸存在于其终止到父进程调用 wait 等函数这个时间的间隙，一般很快就消失，但如果编程不合理，父进程从不调用 wait 等系统调用来收集僵尸进程，那么这些进程会一直存在内存中。

# 检测僵尸进程

``` bash
$ top 
top - 09:58:31 up 3 min,  2 users,  load average: 0.76, 0.45, 0.19
Tasks: 212 total,   1 running, 210 sleeping,   0 stopped,   1 zombie
%Cpu(s):  6.4 us,  3.1 sy,  0.6 ni, 78.6 id, 11.2 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem:   4037576 total,  1664952 used,  2372624 free,    82416 buffers
KiB Swap:  1998844 total,        0 used,  1998844 free.   916128 cached Mem
```

可以看到，我们系统中有一个僵尸进程（1 zombie）。

# 杀死僵尸进程

``` bash
$ ps aux | grep -w 'Z'
#或者只查看特定的栏目：
$ ps -A -o stat,ppid,pid,cmd | grep -e '^[Zz]'
$ sudo kill -9 ppid
```