---
layout: java
title: python协程和异步
date: 2018-12-11 14:21:03
tags:
  - python
  - 异步
---

<!-- TOC -->

1. [概念梳理](#概念梳理)
    1. [协程](#协程)
        1. [定义](#定义)
        2. [协程的实现方法](#协程的实现方法)
2. [定义一个协程](#定义一个协程)
3. [异步获取结果](#异步获取结果)
4. [绑定回调函数](#绑定回调函数)
5. [阻塞和await](#阻塞和await)
6. [协程嵌套](#协程嵌套)
7. [协程的挂起](#协程的挂起)
8. [协程的停止](#协程的停止)
9. [不同线程的事件循环](#不同线程的事件循环)
10. [新线程协程](#新线程协程)

<!-- /TOC -->

<a id="markdown-概念梳理" name="概念梳理"></a>
# 概念梳理

Python 在3.4中引入了协程的概念，详见[asyncio](https://docs.python.org/3.6/library/asyncio.html)

<a id="markdown-协程" name="协程"></a>
## 协程

<a id="markdown-定义" name="定义"></a>
### 定义

协程，coroutine, 是一种用户态的轻量级线程。在调用一个函数的时候，可以发生中断，跳转到别的子程序执行，别的子程序也可以中断回来执行之前的子程序，这就是`协程`

协程拥有自己的寄存器和上下文的栈，协程调度切换时，将寄存器上下文和栈保存到其他地方，在切回来的时候，恢复先前保存的寄存器上下文和栈。**因此：协程能保留上一次调用时的状态（即所有局部状态的一个特定组合），每次过程重入时，就相当于进入上一次调用的状态，换种说法：进入上一次离开时所处逻辑流的位置。**

优点：

1. 无需线程上下文切换的开销，协程避免了无意义的调度，由此可以提高性能（但也因此，程序员必须自己承担调度的责任，同时，协程也失去了标准线程使用多CPU的能力）

1. 无需原子操作锁定及同步的开销
1. 方便切换控制流，简化编程模型
1. 高并发+高扩展性+低成本：一个CPU支持上万的协程都不是问题。所以很适合用于高并发处理。

缺点：
1. 无法利用多核资源，本质上是单线程切换
2. 阻塞操作的时候，会阻塞掉整个程序

<a id="markdown-协程的实现方法" name="协程的实现方法"></a>
### 协程的实现方法

``` python 
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

在asyncio中，使用async关键字来定义协程对象，这里的协程指的是能够异步返回结果的函数，通过事件循环机制来获取结果。

**asyncio中的关键字：**

event_loop 事件循环：程序开启一个无限循环，把一些函数注册到事件循环上，当满足事件发生的时候，调用相应的协程函数

coroutine 协程：协程对象，指一个使用async关键字定义的函数，它的调用不会立即执行函数，而是会返回一个协程对象。协程对象需要注册到事件循环，由事件循环调用。

task 任务：一个协程对象就是一个原生可以挂起的函数，任务则是对协程进一步封装，其中包含了任务的各种状态

future: 代表将来执行或没有执行的任务的结果。它和task上没有本质上的区别

async/await 关键字：python3.5用于定义协程的关键字，async定义一个协程，await用于挂起阻塞的异步调用接口。

<a id="markdown-定义一个协程" name="定义一个协程"></a>
# 定义一个协程

``` python

# 定义一个协程
async def add(a,b):
    asyncio.sleep(1)
    print('add result: ', a+b)
    return a+b

start = time.time()
loop = asyncio.get_event_loop()
coroutine = add(1,2)
loop.run_until_complete(coroutine) # 这里也可以返回执行结果
print('time: ', time.time() - start)

```
asyncio.get_event_loop：创建一个事件循环，然后使用run_until_complete将协程注册到事件循环，并启动事件循环

<a id="markdown-异步获取结果" name="异步获取结果"></a>
# 异步获取结果

过添加task来获取结果。
task添加有两种方法：
- loop.create_task(coroutine)创建task,
- asyncio.ensure_future(coroutine)创建task

``` python

# 定义一个task，用于获取协程结果

start = time.time()
loop = asyncio.get_event_loop()
coroutine = add(1,2)
task = loop.create_task(coroutine) # 可以通过asyncio.ensure_future()来创建task
print(task)
loop.run_until_complete(task)
print(task.result())
print('time: ', time.time() - start)
```

<a id="markdown-绑定回调函数" name="绑定回调函数"></a>
# 绑定回调函数

绑定回调，在task执行完成的时候可以获取执行的结果，回调的最后一个参数是future对象，通过该对象可以获取协程返回值。

``` python
start = time.time()
loop = asyncio.get_event_loop()
loop2 = asyncio.get_event_loop()
print('id diff: ', id(loop), id(loop2)) # 同一个线程占有相同的内存空间
coroutine = add(1,2)
#task = loop.create_task(coroutine) # 可以通过asyncio.ensure_future()来创建task
task = asyncio.ensure_future(coroutine, loop=loop) # loop 是线程特定，因此可以不用传递
print(task)
task.add_done_callback(callback)
loop.run_until_complete(task)
print('time: ', time.time() - start)

```

<a id="markdown-阻塞和await" name="阻塞和await"></a>
# 阻塞和await

使用async可以定义协程对象，使用await可以针对耗时的操作进行挂起，就像生成器里的yield一样，函数让出控制权。协程遇到await，事件循环将会挂起该协程，执行别的协程，直到其他的协程也挂起或者执行完毕，再进行下一个协程的执行

``` python
async def async_add(a, b):
    # asyncio.sleep(1)
    c = 0
    while c < 1000000:
        c +=1
    return a + b


async def hello():
    print('Hello world: ', 'time=', time.time())
    r = await async_add(1, 3)  # 模拟做别的事情
    print('异步返回的结果：', r, 'time: ', time.time())


def run():
    loop = asyncio.get_event_loop()
    loop.run_until_complete(asyncio.wait([hello() for x in range(4)])) # 使用await是因为run_until_complete 是一个阻塞的函数

```

<a id="markdown-协程嵌套" name="协程嵌套"></a>
# 协程嵌套

使用async可以定义协程，协程用于耗时的io操作，我们也可以封装更多的io操作过程，这样就实现了嵌套的协程，即一个协程中await了另外一个协程，如此连接起来。

``` python
import asyncio
import time


now = lambda: time.time()

async def do_some_work(x):
    print("waiting:",x)
    await asyncio.sleep(x)
    return "Done after {}s".format(x)

async def main():
    coroutine1 = do_some_work(1)
    coroutine2 = do_some_work(2)
    coroutine3 = do_some_work(4)
    tasks = [
        asyncio.ensure_future(coroutine1),
        asyncio.ensure_future(coroutine2),
        asyncio.ensure_future(coroutine3)
    ]

    dones, pendings = await asyncio.wait(tasks)
    for task in dones:
        print("Task ret:", task.result())

    # results = await asyncio.gather(*tasks)
    # for result in results:
    #     print("Task ret:",result)


start = now()

loop = asyncio.get_event_loop()
loop.run_until_complete(main())
print("Time:", now()-start)
```

<a id="markdown-协程的挂起" name="协程的挂起"></a>
# 协程的挂起

不在main协程函数里处理结果，直接返回await的内容，那么最外层的run_until_complete将会返回main协程的结果。 将上述的代码更改为：

``` python
import asyncio
import time


now = lambda: time.time()

async def do_some_work(x):
    print("waiting:",x)
    await asyncio.sleep(x)
    return "Done after {}s".format(x)

async def main():
    coroutine1 = do_some_work(1)
    coroutine2 = do_some_work(2)
    coroutine3 = do_some_work(4)
    tasks = [
        asyncio.ensure_future(coroutine1),
        asyncio.ensure_future(coroutine2),
        asyncio.ensure_future(coroutine3)
    ]
    return await asyncio.gather(*tasks)

start = now()

loop = asyncio.get_event_loop()
results = loop.run_until_complete(main())
for result in results:
    print("Task ret:",result)

print("Time:", now()-start)

```

或者返回使用asyncio.wait方式挂起的协程

``` python
import asyncio
import time


now = lambda: time.time()

async def do_some_work(x):
    print("waiting:",x)
    await asyncio.sleep(x)
    return "Done after {}s".format(x)

async def main():
    coroutine1 = do_some_work(1)
    coroutine2 = do_some_work(2)
    coroutine3 = do_some_work(4)
    tasks = [
        asyncio.ensure_future(coroutine1),
        asyncio.ensure_future(coroutine2),
        asyncio.ensure_future(coroutine3)
    ]
    return await asyncio.wait(tasks)

start = now()

loop = asyncio.get_event_loop()
done,pending = loop.run_until_complete(main())
for task in done:
    print("Task ret:",task.result())

print("Time:", now()-start)
```

也可以使用asyncio的as_completed方法

``` python
import asyncio
import time


now = lambda: time.time()

async def do_some_work(x):
    print("waiting:",x)
    await asyncio.sleep(x)
    return "Done after {}s".format(x)

async def main():
    coroutine1 = do_some_work(1)
    coroutine2 = do_some_work(2)
    coroutine3 = do_some_work(4)
    tasks = [
        asyncio.ensure_future(coroutine1),
        asyncio.ensure_future(coroutine2),
        asyncio.ensure_future(coroutine3)
    ]
    for task in asyncio.as_completed(tasks):
        result = await task
        print("Task ret: {}".format(result))

start = now()

loop = asyncio.get_event_loop()
loop.run_until_complete(main())
print("Time:", now()-start)
```

<a id="markdown-协程的停止" name="协程的停止"></a>
# 协程的停止

协程的停止其实就是对future的停止，这个地方和concurrent包里面的futures差不多

future对象有几个状态：

- Pending
- Running
- Done
- Cacelled

创建future的时候，task为pending，事件循环调用执行的时候当然就是running，调用完毕自然就是done，如果需要停止事件循环，就需要先把task取消。可以使用asyncio.Task获取事件循环的task

``` python
import asyncio
import time


now = lambda :time.time()


async def do_some_work(x):
    print("Waiting:",x)
    await asyncio.sleep(x)
    return "Done after {}s".format(x)

coroutine1 =do_some_work(1)
coroutine2 =do_some_work(2)
coroutine3 =do_some_work(2)

tasks = [
    asyncio.ensure_future(coroutine1),
    asyncio.ensure_future(coroutine2),
    asyncio.ensure_future(coroutine3),
]

start = now()

loop = asyncio.get_event_loop()
try:
    loop.run_until_complete(asyncio.wait(tasks))
except KeyboardInterrupt as e:
    print(asyncio.Task.all_tasks())
    for task in asyncio.Task.all_tasks():
        print(task.cancel())
    loop.stop()
    loop.run_forever()
finally:
    loop.close()

print("Time:",now()-start)
```

启动事件循环之后，马上ctrl+c，会触发run_until_complete的执行异常 KeyBorardInterrupt。然后通过循环asyncio.Task取消future。可以看到输出如下：

``` python
Waiting: 1
Waiting: 2
Waiting: 2
^C{<Task finished coro=<do_some_work() done, defined at /app/py_code/study_asyncio/simple_ex10.py:13> result='Done after 1s'>, <Task pending coro=<do_some_work() running at /app/py_code/study_asyncio/simple_ex10.py:15> wait_for=<Future pending cb=[Task._wakeup()]> cb=[_wait.<locals>._on_completion() at /usr/local/lib/python3.5/asyncio/tasks.py:428]>, <Task pending coro=<do_some_work() running at /app/py_code/study_asyncio/simple_ex10.py:15> wait_for=<Future pending cb=[Task._wakeup()]> cb=[_wait.<locals>._on_completion() at /usr/local/lib/python3.5/asyncio/tasks.py:428]>, <Task pending coro=<wait() running at /usr/local/lib/python3.5/asyncio/tasks.py:361> wait_for=<Future pending cb=[Task._wakeup()]>>}
False
True
True
True
Time: 1.0707225799560547
```

True表示cannel成功，loop stop之后还需要再次开启事件循环，最后在close，不然还会抛出异常

循环task，逐个cancel是一种方案，可是正如上面我们把task的列表封装在main函数中，main函数外进行事件循环的调用。这个时候，main相当于最外出的一个task，那么处理包装的main函数即可。

<a id="markdown-不同线程的事件循环" name="不同线程的事件循环"></a>
# 不同线程的事件循环

很多时候，我们的事件循环用于注册协程，而有的协程需要动态的添加到事件循环中。一个简单的方式就是使用多线程。当前线程创建一个事件循环，然后在新建一个线程，在新线程中启动事件循环。当前线程不会被block。

``` python
import asyncio
from threading import Thread
import time

now = lambda :time.time()

def start_loop(loop):
    asyncio.set_event_loop(loop)
    loop.run_forever()

def more_work(x):
    print('More work {}'.format(x))
    time.sleep(x)
    print('Finished more work {}'.format(x))

start = now()
new_loop = asyncio.new_event_loop()
t = Thread(target=start_loop, args=(new_loop,))
t.start()
print('TIME: {}'.format(time.time() - start))

new_loop.call_soon_threadsafe(more_work, 6)
new_loop.call_soon_threadsafe(more_work, 3)
```
启动上述代码之后，当前线程不会被block，新线程中会按照顺序执行call_soon_threadsafe方法注册的more_work方法， 后者因为time.sleep操作是同步阻塞的，因此运行完毕more_work需要大致6 + 3

<a id="markdown-新线程协程" name="新线程协程"></a>
# 新线程协程

``` python
import asyncio
import time
from threading import Thread

now = lambda :time.time()


def start_loop(loop):
    asyncio.set_event_loop(loop)
    loop.run_forever()

async def do_some_work(x):
    print('Waiting {}'.format(x))
    await asyncio.sleep(x)
    print('Done after {}s'.format(x))

def more_work(x):
    print('More work {}'.format(x))
    time.sleep(x)
    print('Finished more work {}'.format(x))

start = now()
new_loop = asyncio.new_event_loop()
t = Thread(target=start_loop, args=(new_loop,))
t.start()
print('TIME: {}'.format(time.time() - start))

asyncio.run_coroutine_threadsafe(do_some_work(6), new_loop)
asyncio.run_coroutine_threadsafe(do_some_work(4), new_loop)
```