---
layout: python
title: python学习笔记
date: 2018-10-23 11:11:20
tags: python
---

<!-- TOC -->

- [安装](#安装)
    - [python 解释器](#python-解释器)
    - [IDE](#ide)
- [函数](#函数)
    - [可变参数](#可变参数)
    - [关键字参数](#关键字参数)
- [高级特性](#高级特性)
    - [迭代](#迭代)
    - [列表生成式](#列表生成式)
    - [生成器](#生成器)
- [函数式编程](#函数式编程)
    - [高阶函数](#高阶函数)
- [面向对象](#面向对象)
    - [类属性和实例属性](#类属性和实例属性)
    - [@property（装饰器）](#property装饰器)
    - [异常处理](#异常处理)
    - [调试](#调试)
- [进程和线程](#进程和线程)
    - [多进程](#多进程)
    - [多线程](#多线程)
    - [ThreadLocal](#threadlocal)
    - [协程](#协程)
- [参考文档](#参考文档)

<!-- /TOC -->

# 安装

[python 官网](https://www.python.org/downloads/)

[Anoconda(推荐)](https://www.anaconda.com/download/)

## python 解释器

CPython: 命令行的python

ipython: CPython 的增强版，能够自动提示，有很多magic功能

PyPy: 采用JIT技术，动态编译代码，显著提高python的执行速度

Jython: 运行在Java平台的python解释器，可以把Python代码编译成Java字节码执行

## IDE

Pycharm(推荐，可以支持远程解释执行)、Spider

# 函数

python 不支持函数的重载，以后面的定义的函数为准。

``` python

def power(a):
    return a*a

def power(a,n):
    s = 1
    while n > 0:
        n = n - 1
        s = s*a
    return s

```

当调用`power(5)`的时候，发现代码报错，原因是被后面的power函数给重定义了，第一个失效，因此采用默认参数来实现函数的重载

``` python
def power(a,n=2):
    s = 1
    while n > 0:
        n = n - 1
        s = s*a
    return s
```

**注意：**
- 必选参数需要放在前面，默认参数放在后面
- 变换大的参数放前面，变化小的参数放后面
- 默认参数必须指向不变对象（None, str,tuple)

## 可变参数

函数参数前面加一个星号

``` python
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum

calc(1,2,3)
calc(1,2)
```
## 关键字参数

可变参数允许你传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个tuple。而关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。请看示例：

``` python
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)

>>> person('Michael', 30)
name: Michael age: 30 other: {}

>>> person('Bob', 35, city='Beijing')
name: Bob age: 35 other: {'city': 'Beijing'}

# 还可以自己对参数进行过滤
def person(name, age, **kw):
    if 'city' in kw:
        # 有city参数
        pass
    if 'job' in kw:
        # 有job参数
        pass
    print('name:', name, 'age:', age, 'other:', kw)
    
```


# 高级特性

## 迭代

采用 for + 可迭代对象的方式实现

``` python
items = [1,2,3]
for i, item in enumerate(items):
    print(i, item)

```

## 列表生成式

python 内置的非常强大的用来创建list的生成式

``` python
>>> list(range(1, 11))
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

# 筛选出偶数的平方
>>> [x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]

# java语言的a>b？a：b

>>> a if a > b else b
```

## 生成器

列表生成式是一次性创建，而生成器可以边循环边创建,只需要把列表生成式的[]改成()

``` python
>>>g = (x*x for x in range(10))
<generator object <genexpr> at 0x1022ef630>

# 使用next()函数获得generator的下一个返回值

>>> next(g)
0
>>> next(g)
1
...
81
# 没有更多元素时候，抛出异常
>>>next(g)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

生成器遇到`yeild` 就会返回，再次执行`next()`函数就会从上次返回的yeild语句处继续执行

``` python
def odd():
    print('step 1')
    yield 1
    print('step 2')
    yield(3)
    print('step 3')
    yield(5)

>>> o = odd()
>>> next(o)
step 1
1
>>> next(o)
step 2
3
>>> next(o)
step 3
5
>>> next(o)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

# 函数式编程

变量可以指向函数,例如:
```python
import math
f = math.cos
print(f(5))
```

## 高阶函数

将函数作为另一个函数的参数输入，该函数称为高阶函数

``` python
def add(x,y,f):
    return f(x)+f(y)

x = -5
y = 6
f = abs
print(add(x,y,f))
```

# 面向对象

dir(obj) : 查看对象的所有属性和方法
isinstance(obj, class): 判断obj对象是否是class类型
hasattr(obj, att) : 判断对象obj是否有att属性
getattr(obj, att) : 获取对象obj的att属性
setattr(obj, att) : 设置对象obj的att属性

## 类属性和实例属性

实例变量和类变量同名的话，实例变量会覆盖掉类变量
``` python
class A:
    __slot__ = ('name','sex') # 使用turple来限制类能绑定的属性的名称
    name = "sa" # 类属性
    def __init__(self, name):
        self.name = name # 实例属性, 覆盖掉类属性
        self.sex = "male" # 实例属性

a = A("test")
print(a.name) # test
print(A.name) # sa
dle a.name
print(a.name) # sa

```

## @property（装饰器）

对于私有变量，可以采用@property的方法实现把一个方法当做属性进行调用

``` python
class Student(object):

    @property # 负责把一个getter 方法编程属性
    def score(self):
        return self._score

    @score.setter  # 负责把一个setter方法变成属性赋值
    def score(self, value):
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value

s = Student()
s.score = 60
```

## 异常处理

try ... except ... finally ... 的错误处理机制

``` python
try:
    print('try...')
    r = 10 / 0
    print('result:', r)
except ZeroDivisionError as e:
    print('except:', e) # 有异常发生的时候执行
else:
    print('no exceptions') # 无异常发生的时候执行
finally:
    print('finally...') # 一定会执行
print('END')
```

## 调试

1. print打印
2. assert 断言
3. logging 包
4. pdb 调试

`python -m pdb err.py` 启动调试模式，单步执行

或者在err.py里面使用pdb.set_trace()代码，使用python命令执行程序会自动在这一行进入pdb调试模式

``` python
# err.py
import pdb

s = '0'
n = int(s)
pdb.set_trace() # 运行到这里会自动暂停
print(10 / n)

```

pdb 常用命令：
p 变量名： 查看变量
n ：单步执行代码
c ：执行到断点处

# 进程和线程

python 采用了GIL（Global Interpreter Lock）锁的机制,因此单进程多线程的程序只能利用一个CPU核心，想要跑满多核，需要采用多进程编程的方法来实现

## 多进程

采用 os.fork() 或者multiprocessing 的包来实现多进程

[参考链接](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431927781401bb47ccf187b24c3b955157bb12c5882d000)


## 多线程

``` python
import time, threading

# 新线程执行的代码:
def loop():
    print('thread %s is running...' % threading.current_thread().name)
    n = 0
    while n < 5:
        n = n + 1
        print('thread %s >>> %s' % (threading.current_thread().name, n))
        time.sleep(1)
    print('thread %s ended.' % threading.current_thread().name)

print('thread %s is running...' % threading.current_thread().name)
t = threading.Thread(target=loop, name='LoopThread')
t.start()
t.join()
print('thread %s ended.' % threading.current_thread().name)
```

## ThreadLocal

Python 也支持lock，也可以使用threadlocal 来实现线程之间变量的隔离

## 协程

python 中的协程(Coroutine)是通过generator实现的。

优势：
1. 极高的执行效率，子程序之间不用线程切换，由程序自身控制，线程越多，协程的优势越明显
2. 不需要多线程的锁机制。因此采用多进程+协程的方法，能够利用多核CPU的优势。

``` python
def consumer():
    r = ''
    while True:
        n = yield r
        if not n:
            return
        print('[CONSUMER] Consuming %s...' % n)
        r = '200 OK'

def produce(c):
    c.send(None)
    n = 0
    while n < 5:
        n = n + 1
        print('[PRODUCER] Producing %s...' % n)
        r = c.send(n)
        print('[PRODUCER] Consumer return: %s' % r)
    c.close()

c = consumer()
produce(c)
```

asyncio 异步IO的支持

``` python
import asyncio

@asyncio.coroutine
def hello():
    print("Hello world!")
    # 异步调用asyncio.sleep(1):
    r = yield from asyncio.sleep(1)
    print("Hello again!")

# 获取EventLoop:
loop = asyncio.get_event_loop()
# 执行coroutine
loop.run_until_complete(hello())
loop.close()

```

@asyncio.coroutine把一个generator标记为coroutine类型，然后，我们就把这个coroutine扔到EventLoop中执行。

hello()会首先打印出Hello world!，然后，yield from语法可以让我们方便地调用另一个generator。由于asyncio.sleep()也是一个coroutine，所以线程不会等待asyncio.sleep()，而是直接中断并执行下一个消息循环。当asyncio.sleep()返回时，线程就可以从yield from拿到返回值（此处是None），然后接着执行下一行语句。

把asyncio.sleep(1)看成是一个耗时1秒的IO操作，在此期间，主线程并未等待，而是去执行EventLoop中其他可以执行的coroutine了，因此可以实现并发执行。

# 参考文档

- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143208573480558080fa77514407cb23834c78c6c7309000)