---
title: '[转]Java线程同步，synchronized锁住的是代码还是对象'
tags: [java, 笔记]
categories: [IT, java]
date: 2016-10-24 11:01:27
---

作者：叉叉哥
转载自： [http://blog.csdn.net/xiao__gui/article/details/8188833](http://blog.csdn.net/xiao__gui/article/details/8188833)

在[Java](http://lib.csdn.net/base/javaee "Java EE知识库")中，synchronized关键字是用来控制线程同步的，就是在多线程的环境下，控制synchronized代码段不被多个线程同时执行。synchronized既可以加在一段代码上，也可以加在方法上。

关键是，不要认为给方法或者代码段加上synchronized就万事大吉，看下面一段代码：

``` java
class Sync {  

    public synchronized void test() {  
        System.out.println("test开始..");  
        try {  
            Thread.sleep(1000);  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
        System.out.println("test结束..");  
    }  
}  

class MyThread extends Thread {  

    public void run() {  
        Sync sync = new Sync();  
        sync.test();  
    }  
}  

public class Main {  

    public static void main(String[] args) {  
        for (int i = 0; i < 3; i++) {  
            Thread thread = new MyThread();  
            thread.start();  
        }  
    }  
}  
```

运行结果：

test开始..

test开始..

test开始..

test结束..

test结束..

test结束.._

可以看出来，上面的程序起了三个线程，同时运行Sync类中的test()方法，虽然test()方法加上了synchronized，但是还是同时运行起来，貌&#20284;synchronized没起作用。&nbsp;

将test()方法上的synchronized去掉，在方法内部加上synchronized(this)：

``` java
public void test() {  
    synchronized(this){  
        System.out.println("test开始..");  
        try {  
            Thread.sleep(1000);  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
        System.out.println("test结束..");  
    }  
}  
```

运行结果：

test开始..

test开始..

test开始..

test结束..

test结束..

test结束..

一切还是这么平静，没有看到synchronized起到作用。

实际上，synchronized(this)以及非static的synchronized方法（至于static synchronized方法请往下看），只能防止多个线程同时执行同一个对象的同步代码段。

回到本文的题目上：synchronized锁住的是代码还是对象。答案是：<span style="color:rgb(255,0,0)">synchronized锁住的是括号里的对象，而不是代码</span>。对于非static的synchronized方法，锁的就是对象本身也就是this。

当synchronized锁住一个对象后，别的线程如果也想拿到这个对象的锁，就必须等待这个线程执行完成释放锁，才能再次给对象加锁，这样才达到线程同步的目的。即使两个不同的代码段，都要锁同一个对象，那么这两个代码段也不能在多线程环境下同时运行。

所以我们在用synchronized关键字的时候，能缩小代码段的范围就尽量缩小，能在代码段上加同步就不要再整个方法上加同步。这叫减小锁的粒度，使代码更大程度的并发。原因是基于以上的思想，锁的代码段太长了，别的线程是不是要等很久，等的花儿都谢了。当然这段是题外话，与本文核心思想并无太大关联。

再看上面的代码，每个线程中都new了一个Sync类的对象，也就是产生了三个Sync对象，由于不是同一个对象，所以可以多线程同时运行synchronized方法或代码段。

为了验证上述的观点，修改一下代码，让三个线程使用同一个Sync的对象。

``` java
class MyThread extends Thread {  

    private Sync sync;  

    public MyThread(Sync sync) {  
        this.sync = sync;  
    }  

    public void run() {  
        sync.test();  
    }  
}  

public class Main {  

    public static void main(String[] args) {  
        Sync sync = new Sync();  
        for (int i = 0; i < 3; i++) {  
            Thread thread = new MyThread(sync);  
            thread.start();  
        }  
    }  
}  
```


运行结果：

test开始..

test结束..

test开始..

test结束..

test开始..

test结束..

可以看到，此时的synchronized就起了作用。

那么，如果真的想锁住这段代码，要怎么做？也就是，如果还是最开始的那段代码，每个线程new一个Sync对象，怎么才能让test方法不会被多线程执行。

解决也很简单，只要锁住同一个对象不就行了。例如，<span style="font-family:Calibri">synchronized</span>后的括号中锁同一个<span style="font-family:Calibri">固定</span>对象，这样就行了。这样是没问题，但是，比较多的做法是让<span style="font-family:Calibri">synchronized</span>锁这个类对应的<span style="font-family:Calibri">Class</span>对象。

``` java
class Sync {  

    public void test() {  
        synchronized (Sync.class) {  
            System.out.println("test开始..");  
            try {  
                Thread.sleep(1000);  
            } catch (InterruptedException e) {  
                e.printStackTrace();  
            }  
            System.out.println("test结束..");  
        }  
    }  
}  

class MyThread extends Thread {  

    public void run() {  
        Sync sync = new Sync();  
        sync.test();  
    }  
}  

public class Main {  

    public static void main(String[] args) {  
        for (int i = 0; i < 3; i++) {  
            Thread thread = new MyThread();  
            thread.start();  
        }  
    }  
}  

```

运行结果：

test开始..

test结束..

test开始..

test结束..

test开始..

test结束..

上面代码用<span style="color:red"><span style="font-family:Calibri">synchronized(Sync.class)</span></span><span style="color:red">实现了全局锁的效果</span>。

<span style="font-family:宋体">最后说说</span>static synchronized方法，static方法可以直接类名加方法名调用，方法中无法使用this，所以它锁的不是this，而是类的Class对象，所以，<span style="color:red">static synchronized</span><span style="color:red">方法也相当于全局锁</span>，相当于锁住了代码段。

### 参考链接
[http://www.cnblogs.com/techyc/archive/2013/03/19/2969677.html](http://www.cnblogs.com/techyc/archive/2013/03/19/2969677.html)
