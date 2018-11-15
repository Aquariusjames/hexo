---
layout: java
title: python中的子进程
date: 2018-11-14 09:53:39
tags: python
---
<!-- TOC -->

- [系统命令执行](#系统命令执行)
    - [os包](#os包)
    - [subprocess](#subprocess)
- [多进程](#多进程)
    - [多进程的实现](#多进程的实现)

<!-- /TOC -->


# 系统命令执行

## os包

通常我们可以采用`os.system(cmd)` 或者`os.popen(cmd)`来调用系统命令。

- os.system(cmd): 返回值为0（成功执行）， 其实返回的是一个16位的二进制数，低8位为杀死所调用脚本的信号号码，高位为脚本的退出状态码

``` python

## test.sh
echo 'hello'
exit 3
## 

import os
ret = os.system('./test.sh')
ret = ret >> 8
print(ret) # 3

```
输出：
hello
3

- os.popen(cmd): 该种方法是通过管道的方式来实现命令调用，函数返回一个file-like对象，里面的内容是脚本的标准输出，可以通过read()函数来接收

``` python
import os
ret = os.popen('./test.sh')
print(ret.read())
```
输出：
hello

## subprocess

对子进程进行更好的封装：

``` python
def sub_proc2():
    print('start subprocess')
    p = sp.Popen(['nslookup'], stdin=sp.PIPE, stdout=sp.PIPE, stderr=sp.PIPE)
    out, err = p.communicate(bytes('www.baidu.com', encoding='utf-8'))
    print(out.decode('utf-8'))
    print('exit')

if __name__ == '__main__':
    sub_proc2()
```

# 多进程

## 多进程的实现

1. 多线程实现生产者和消费者模式（Queue）通信
``` python 
from multiprocessing import Process, Queue
import time, random

def produce(q):
    count = 0
    while True:
        time.sleep(random.random() * 3)
        if q.full() is False:
            val = count * count
            print('produce product: ', val)
            q.put(val)
        count += 1

def consume(q):
    while True:
        time.sleep(random.random()*3)
        if q.empty() is False:
            print('consume product: ', q.get())
        else:
            print('q is empty')
            continue


if __name__ == '__main__':
    q = Queue(20)
    proc_produce = Process(target=produce, args=(q,))
    proc_consume = Process(target=consume, args=(q,))
    proc_produce.start()
    proc_consume.start()
```

2. 使用Pipe通信

``` python 
from multiprocessing import Process, Queue, Pipe

def proc1(conn):
    for i in range(10):
        conn.send(i)
        print('send {} to pipe '.format(i))
        time.sleep(1)

def proc2(conn):
    n = 9
    while n > 0:
        result = conn.recv()
        print('receive {} from pip'.format(result))
        n -= 1

if __name__ == '__main__':
    p1, p2 = Pipe(True) # True 表示两个管道是全双工的，均可收发
    process1 = Process(target=proc1, args=(p1,))
    process2 = Process(target=proc2, args=(p2,))
    process1.start()
    process2.start()
    process1.join()
    process2.join()
    p1.close()
    p2.close()
```
