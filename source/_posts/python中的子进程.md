---
layout: java
title: python中的子进程
date: 2018-11-14 09:53:39
tags: python
---


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

## 多进程的应用场景

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

## 多进程的启动

1. 使用函数来启动多进程任务

``` python 
import multiprocessing
import time

def worker(interval):
    n = 5
    while n > 0:
        print("The time is {0}".format(time.ctime()))
        time.sleep(interval)
        n -= 1

if __name__ == "__main__":
    p = multiprocessing.Process(target = worker, args = (3,))
    p.start()
    print "p.pid:", p.pid
    print "p.name:", p.name
    print "p.is_alive:", p.is_alive()
```

2. 使用类来定义多进程任务

``` python
import multiprocessing
import time

class ClockProcess(multiprocessing.Process):
    def __init__(self, interval):
        multiprocessing.Process.__init__(self)
        self.interval = interval

    def run(self):
        n = 5
        while n > 0:
            print("the time is {0}".format(time.ctime()))
            time.sleep(self.interval)
            n -= 1

if __name__ == '__main__':
    p = ClockProcess(3)
    p.start()   

```

3. 使用进程池来启动多进程任务

``` python
import multiprocessing
import time

def func(msg):
    for i in xrange(3):
        print msg
        time.sleep(1)
    return "done " + msg

if __name__ == "__main__":
    pool = multiprocessing.Pool(processes=4)
    result = []
    for i in xrange(10):
        msg = "hello %d" %(i)
        result.append(pool.apply_async(func, (msg, )))
    pool.close()
    pool.join()
    for res in result:
        print res.get()
    print "Sub-process(es) done."
```

## 获取进程的返回结果

1. 通过Queue让两个进程间通信
2. 使用进程池子
3. 通过Manager的方法来返回线程的值

``` python 
def worker(procnum, return_dict):
    '''worker function'''
    print str(procnum) + ' represent!'
    return_dict[procnum] = procnum


if __name__ == '__main__':
    manager = Manager()
    return_dict = manager.dict()
    jobs = []
    for i in range(5):
        p = multiprocessing.Process(target=worker, args=(i,return_dict))
        jobs.append(p)
        p.start()

    for proc in jobs:
        proc.join()
    print return_dict.values()
```

## 多进程的停止

停止方法：process.terminate()

process.join() 值为了等待子进程结束后，主进程可以继续运行，通常用于进程间的同步

os.kill(pid,signal.SIGKILL) 该方法相当于kill -9，杀死进程

``` python
import time
from multiprocessing import Process


def run_forever():
    while 1:
        print(time.time())
        time.sleep(2)


def main():
    p = Process(target=run_forever)
    p.start()
    print('start a process.')
    time.sleep(10)
    if p.is_alive:
        # stop a process gracefully
        p.terminate()
        print('stop process')
        p.join()


if __name__ == '__main__':
    main()
    # 如果主进程不停止，子线程就无法被杀死，会出现zombie 进程
    while True:
      pass
```

## Damon

damon 在process.start()之前使用才有效。
process.daemon = True # 表示当主进程结束的时候，子进程也会跟着结束


参考文档：
- [Python 多进程编程](https://www.cnblogs.com/kaituorensheng/p/4445418.html)