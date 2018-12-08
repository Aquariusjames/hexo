---
title: java锁和监视器
tags:
  - 笔记
  - java
categories:
  - 笔记
  - java
date: 2018-07-16 19:34:40
---

在JVM的规范中，有这么一些话：  “在JVM中，每个对象和类在逻辑上都是和一个监视器相关联的，为了实现监视器的排他性监视能力，JVM为每一个对象和类都关联一个锁（内置锁），锁住了一个对象，就是获得对象相关联的监视器” 

**锁为监视器的实现提供了必要的支持。**

java 提供了synchronized关键字来支持内置锁。synchronized 关键字可以放在方法的前面、对象的前面、类的前面。

# 同步方法中的锁

当线程调用同步方法时，它自动获得这个方法所在对象的内在锁，并且方法返回时释放锁，如果发生未捕获的异常，也会释放锁。

当调用静态同步方法时，因为静态方法和类相关联，线程获得和这个类关联的Class对象的内在锁。
使用内在锁后，把deposit方法和withdraw方法修改为同步方法，就可以避免线程干扰。

``` java
public synchronized void deposit(int amount) {
balance = balance + amount;
}
public synchronized void withdraw(int amount) {
balance = balance - amount;
}
```

# 同步语句

同步语句必须指定提供内在锁的对象，其基本用法如下：

``` java
synchronized（提供锁的对象）{
临界代码
}
用同步语句修改BankAccount类中的方法如下：
public void deposit(int amount) {
synchronized (this) {
balance = balance + amount;
}
}
public void withdraw(int amount) {
synchronized (this) {
balance = balance - amount;
}
}
```

# 同步类

把synchronized关键字放在类的前面，这个类中的所有方法都是同步方法。

# 可重入同步

线程可以获得他已经拥有的锁，运行线程多次获得同一个锁，就是可以重入（reentrant）同步。这种情况通常是同步代码直接或者间接的调用也包含了同步代码的方法，并且两个代码集都使用同一个锁。如果没有可重入同步，那么，同步代码就必须采取很多额外的预防措施避免线程阻塞自己。java java.util.concurrent 包中的  ReentrantLock 即为可重入锁

``` java
private Lock bankLock = new ReentrantLock();
…
  public double getTotalBalance()
   {
      bankLock.lock();
      try
      {
         double sum = 0;

         for (double a : accounts)
            sum += a;

         return sum;
      }
      finally
      {
         bankLock.unlock();
      }
   }

```

# Lock 和 synchronized 的选择

- Lock是一个接口，而synchronized是java的关键字，synchronized是内置的语言实现
- synchronized在发生异常的时候，会自动释放锁，不会发生死锁，而Lock在发生异常的时候，如果没有主动的去释放锁（unlock)，则会造成死锁的现象，因此Lock需要用try-catch-finally，在块中释放锁
- Lock可以让等待锁的线程响应中断(tryLock(time))，synchronized不行，使用synchronized锁的线程会一直等待下去，不能响应中断。
- 通过Lock可以知道有没有成功获取锁，但是synchronized无法办到
- Lock可以提高多个线程进行读操作的效率

# 参考资料

- [锁和监视器之间的区别 – Java并发](https://www.cnblogs.com/keeplearnning/p/7020287.html)
- [java 内在锁（intrinsic lock）或者监视器锁（monitor lock）](https://blog.csdn.net/fujunsfzh/article/details/53517536)