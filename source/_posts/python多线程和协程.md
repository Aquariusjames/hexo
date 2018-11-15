---
layout: java
title: python多线程和协程
date: 2018-11-15 10:07:34
tags: 
  - python
  - 多线程
---

<!-- TOC -->

- [多线程](#多线程)
    - [实现方法](#实现方法)
    - [线程通信和线程同步](#线程通信和线程同步)
    - [队列](#队列)
- [协程](#协程)
- [参考文档](#参考文档)

<!-- /TOC -->

# 多线程

## 实现方法

python 中的多线程通常有两种实现方法，直接指定函数和继承线程类

1. 直接指定线程目标函数

``` python
import threading
import time

def run():
    time.sleep(1)
    print('do something else')

t = threading.Thread(target=run, args=())
t.start()

```

2. 继承线程类

``` python
import threading
import time

class MyThread(threading.Thread):
    def __init__(self, arg):
        super(MyThread, self).__init__()  # 注意：一定要显式的调用父类的初始化函数。
        self.arg = arg

    def run(self):  # 定义每个线程要运行的函数
        time.sleep(1)
        print('the arg is:%s\r' % self.arg)
t = MyThread()
t.start()
```

## 线程通信和线程同步

多线程通信可以通过多种方法，常见的有线程间的wait和notify机制，共享内存之类的。线程间的同步可以采用线程锁来实现


1. 通知等待机制

首先看看java是如何实现线程wait 和 notify的。

``` java
package com.oceanai.test;

import java.util.ArrayDeque;
import java.util.Queue;

/**
 * 生产者和消费者实现.
 *
 * @author Xiong Raorao
 * @since 2018-11-15-11:15
 */
public class ProduceTest {


  public static void main(String[] args) {
    Queue<Integer> q = new ArrayDeque<>();
    Object obj = new Object();
    int count = 0;
    Thread produceThread = new Thread(() -> {
      synchronized (obj){
        while(true){
          try {
            Thread.sleep(1000);
          } catch (InterruptedException e) {
            e.printStackTrace();
          }
          if(q.size() > 10){
            try {
              System.out.println("[生产者]:队列已满，等待");
              obj.wait();
            } catch (InterruptedException e) {
              e.printStackTrace();
            }
          }else {
            int val = (int) (Math.random()*100);
            System.out.println("[生产者]: 收到通知，继续生产。。。");
            q.add(val);
            System.out.println("[生产者]: 生产: " + val);
            obj.notify();
          }
        }
      }
    });

    Thread consumeThread = new Thread(()->{
      synchronized (obj){
        while (true){
          try {
            Thread.sleep(1000);
          } catch (InterruptedException e) {
            e.printStackTrace();
          }
          if(q.isEmpty()){
            try {
              System.out.println("[消费者]:队列为空，等待");
              obj.wait();
            } catch (InterruptedException e) {
              e.printStackTrace();
            }
          }else{
            System.out.println("[消费者]:收到通知，继续消费");
            int val = q.poll();
            System.out.println("[消费者]:消费: " + val);
            obj.notify();
          }
        }
      }
    });
    produceThread.start();
    consumeThread.start();
  }
}

```

java的synchronized关键字绑定了一个对象锁,利用对象的wait()和notify()机制来实现消费者和生产者模型

由于Python对象没有隐式地和一个锁关联，因此需要利用Lock来实现,实现类为Condition

``` python
# -*- coding: utf-8 -*-    
import threading  
class Q(object):  
    def __init__(self):  
        self.n=0  
        self.valueSet=False  
        #相对于java,这里的要自己声明  
        self.cv=threading.Condition()  
          
    def get(self):  
        cv=self.cv  
        #先得到锁  
        cv.acquire()  
        if not self.valueSet:  
            cv.wait()  
        print "Got:",self.n  
        self.valueSet=False  
        cv.notify()  
        #放弃锁  
        cv.release()  
        return self.n  
          
    def put(self,n):  
        cv=self.cv  
        #先得到锁  
        cv.acquire()  
        if self.valueSet:  
            cv.wait()  
        self.n=n;  
        self.valueSet=True  
        print "Put:",n  
        #放弃锁  
        cv.notify()  
        cv.release()  
          
class Producer(threading.Thread):  
    def __init__(self,q):  
        threading.Thread.__init__(self)  
        self.q=q  
        self.start()  
          
    def run(self):  
        i=0  
        while i<7:  
            i+=1  
            self.q.put(i)      
              
class Consumer(threading.Thread):  
    def __init__(self,q):  
        threading.Thread.__init__(self)  
        self.q=q  
        self.start()  
          
    def run(self):  
        i=0  
        while i<7:  
            i+=1  
            self.q.get()  
              
if __name__=="__main__":  
    q=Q()  
    Producer(q)  
    Consumer(q)           

```

2. 锁机制

python 中的锁有两种实现，一种是`threading.Lock() `，另一种是`threading.RLock() `， 区别在于，RLock在同一个线程中可以多次被获取和释放，acquire和release函数需要成对出现。Lock在一个线程里面只能被获取一次，否则会发生死锁，一直等待。

``` python 
import threading
summary = 0
lock = threading.Lock()

def add():
    global summary
    # global lock
    lock.acquire()
    for i in range(100000):
        summary += 1
        summary -= 1
    lock.release()

def start():
    for t in range(100):
        t = threading.Thread(target=add)
        t.start()
    print('summary = ', summary)

start()

```

使用with直接获取锁

``` python 

def add():
    global summary
    # global lock
    with lock:
        for i in range(100000):
            summary += 1
            summary -= 1
```


3. 守护线程

python线程启动之前可以设置线程的`daemon`属性，默认Daemon为False。

Daemon = False: 当主线程执行完毕，会一直等待子线程执行完。
Daemon = True: 当主线程执行完毕，会立即结束程序。

## 队列

python 自带的queue实现了线程安全的队列，一共实现了三种队列：
- 先进先出(FIFO)
- 后进先出(LIFO)
- 优先队列(Priority)

常见方法：
Queue.Queue(maxsize=0)   FIFO， 如果maxsize小于1就表示队列长度无限
Queue.LifoQueue(maxsize=0)   LIFO， 如果maxsize小于1就表示队列长度无限
Queue.PriorityQueue(Queue)  优先队列，升序排列，要求Queue的元素为元组(priority number, data)
Queue.qsize()   返回队列的大小 
Queue.empty()   如果队列为空，返回True,反之False 
Queue.full()   如果队列满了，返回True,反之False
Queue.get([block[, timeout]])   读队列，timeout等待时间 
Queue.put(item, [block[, timeout]])   写队列，timeout等待时间 
Queue.queue.clear()   清空队列

# 协程

协程：用户级别的线程，能够从函数A中断，然后跳转到另外一个函数执行，并且拥有自己的栈空间。

> 线程是系统级别的，它们是由操作系统调度；协程是程序级别的，由程序员根据需要自己调度。我们把一个线程中的一个个函数叫做子程序，那么子程序在执行过程中可以中断去执行别的子程序；别的子程序也可以中断回来继续执行之前的子程序，这就是协程。

协程的优缺点：

**协程的优点：**

　　（1）无需线程上下文切换的开销，协程避免了无意义的调度，由此可以提高性能（但也因此，程序员必须自己承担调度的责任，同时，协程也失去了标准线程使用多CPU的能力）

　　（2）无需原子操作锁定及同步的开销

　　（3）方便切换控制流，简化编程模型

　　（4）高并发+高扩展性+低成本：一个CPU支持上万的协程都不是问题。所以很适合用于高并发处理。

**协程的缺点：**

　　（1）无法利用多核资源：协程的本质是个单线程,它不能同时将 单个CPU 的多个核用上,协程需要和进程配合才能运行在多CPU上.当然我们日常所编写的绝大部分应用都没有这个必要，除非是cpu密集型应用。

　　（2）进行阻塞（Blocking）操作（如IO时）会阻塞掉整个程序

``` python 

#! /usr/bin/env python
# -*- coding:utf-8 -*-
# Author: "Zing-p"
# Date: 2017/5/12


def consumer(name):
    print("要开始啃骨头了...")
    while True:
        print("\033[31;1m[consumer] %s\033[0m " % name)
        bone = yield
        print("[%s] 正在啃骨头 %s" % (name, bone))


def producer(obj1, obj2):
    obj1.send(None)    # 启动obj1这个生成器,第一次必须用None  <==> obj1.__next__()
    obj2.send(None)    # 启动obj2这个生成器,第一次必须用None  <==> obj2.__next__()
    n = 0
    while n < 5:
        n += 1
        print("\033[32;1m[producer]\033[0m 正在生产骨头 %s" % n)
        obj1.send(n)
        obj2.send(n)


if __name__ == '__main__':
    con1 = consumer("消费者A")
    con2 = consumer("消费者B")
    producer(con1, con2)

```

![python实现协程](https://images2015.cnblogs.com/blog/986023/201705/986023-20170512144725551-803097173.png)


# 参考文档

- [协程及Python中的协程](https://www.cnblogs.com/zingp/p/5911537.html)