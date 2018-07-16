---
title: java面试常考
tags:
  - 笔记
  - java
categories:
  - 笔记
  - java
date: 2018-07-15 10:22:53
---

# java基础

## 方法重载和方法重写

链接：https://www.nowcoder.com/questionTerminal/7b2152a85b9a4ebab6dfda7e995a8491
来源：牛客网

**方法重载发生在编译器，方法重写发生在运行期。**

**方法重写的原则：**

- 重写方法的方法名称、参数列表必须与原方法的相同，返回类型可以相同也可以是原类型的子类型(从Java SE5开始支持)。
- 重写方法不能比原方法访问性差（即访问权限不允许缩小）。
- 重写方法不能比原方法抛出更多的异常。
- 被重写的方法不能是final类型，因为final修饰的方法是无法重写的。
- 被重写的方法不能为private，否则在其子类中只是新定义了一个方法，并没有对其进行重写。
- 被重写的方法不能为static。如果父类中的方法为静态的，而子类中的方法不是静态的，但是两个方法除了这一点外其他都满足重写条件，那么会发生编译错误；反之亦然。即使父类和子类中的方法都是静态的，并且满足重写条件，但是仍然不会发生重写，因为静态方法是在编译的时候把静态方法和类的引用类型进行匹配。
- 重写是发生在运行时的，因为编译期编译器不知道并且没办法确定该去调用哪个方法，JVM会在代码运行的时候作出决定。

**方法重载的原则：**

- 方法名称必须相同。
- 参数列表必须不同（个数不同、或类型不同、参数类型排列顺序不同等）。
- 方法的返回类型可以相同也可以不相同。
- 仅仅返回类型不同不足以成为方法的重载。
- 重载是发生在编译时的，因为编译器可以根据参数的类型来选择使用哪个方法。

** 重写和重载的不同：**
- 方法重写要求参数列表必须一致，而方法重载要求参数列表必须不一致。
- 方法重写要求返回类型必须一致(或为其子类型)，方法重载对此没有要求。
- 方法重写只能用于子类重写父类的方法，方法重载用于同一个类中的所有方法。
- 方法重写对方法的访问权限和抛出的异常有特殊的要求，而方法重载在这方面没有任何限制。
- 父类的一个方法只能被子类重写一次，而一个方法可以在所有的类中可以被重载多次。
- 重载是编译时多态，重写是运行时多态。

## java构造方法

> 在java中，一个新的对象被创建的时候，构造方法会被调用。每一个类都有构造方法。在程序员没有给类提供构造方法的情况下，Java编译器会为这个类创建一个默认的构造方法。

> Java中构造方法重载和方法重载很相似。可以为一个类创建多个构造方法。每一个构造方法必须有它自己唯一的参数列表。
Java不支持像C++中那样的复制构造方法，这个不同点是因为如果你不自己写构造方法的情况下，Java不会创建默认的复制构造方法。

## 接口和抽象类区别

java 类可以继承一个抽象类，实现多个接口，都不能被实例化

- 1. 抽象类

抽象类定义为包含抽象方法的类(*不包含抽象方法，仅用abstract修饰的类也可以是抽象类，只不过没有抽象方法设计成的抽象类没多大意义*)，使用abstrac关键字修饰。

抽象类为继承而设计，抽象方法必须为public或protected,缺省默认为public，抽象类不能被实例化，如果一个类继承一个抽象类，必须实现所有的抽象方法，否则子类也必须定义为抽象类。

- 2. 接口

接口定义为提供别人调用的方法或函数，java中使用interface定义，接口可以含有变量和方法，但是变量会被隐式指定为public static final变量，方法则会被隐式地指定为public abstrac方法，变量和方法也仅仅只能如此，否则会编译错误。

抽象类实现接口的时候，可以不实现接口的抽象方法。

- 3. 区别

| |抽象类 | 接口
---|--- | ---
构造函数| 有| 无
普通成员变量| 有 | 无
非abstract 方法 | 有  | 无
访问修饰符 | public, protected | 默认也只能是public
静态方法 | 有 | 无
静态成员变量 | 访问类型任意 | 默认也只能是public static final
其他| 单继承 | 多实现

**语法层面讲**

1）抽象类可以提供成员方法的实现细节，而接口中只能存在public abstract 方法；
2）抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是public static final类型的；
3）接口中不能含有静态代码块以及静态方法，而抽象类可以有静态代码块和静态方法；
4）一个类只能继承一个抽象类，而一个类却可以实现多个接口。

**设计层面上的区别**

1）抽象类是对一种事物的抽象，即对类抽象，而接口是对行为的抽象。抽象类是对整个类整体进行抽象，包括属性、行为，但是接口却是对类局部（行为）进行抽象。举个简单的例子，飞机和鸟是不同类的事物，但是它们都有一个共性，就是都会飞。那么在设计的时候，可以将飞机设计为一个类Airplane，将鸟设计为一个类Bird，但是不能将 飞行 这个特性也设计为类，因此它只是一个行为特性，并不是对一类事物的抽象描述。此时可以将 飞行 设计为一个接口Fly，包含方法fly( )，然后Airplane和Bird分别根据自己的需要实现Fly这个接口。然后至于有不同种类的飞机，比如战斗机、民用飞机等直接继承Airplane即可，对于鸟也是类似的，不同种类的鸟直接继承Bird类即可。从这里可以看出，继承是一个 "是不是"的关系，而 接口 实现则是 "有没有"的关系。如果一个类继承了某个抽象类，则子类必定是抽象类的种类，而接口实现则是有没有、具备不具备的关系，比如鸟是否能飞（或者是否具备飞行这个特点），能飞行则可以实现这个接口，不能飞行就不实现这个接口。

2）设计层面不同，抽象类作为很多子类的父类，它是一种模板式设计。而接口是一种行为规范，它是一种辐射式设计。什么是模板式设计？最简单例子，大家都用过ppt里面的模板，如果用模板A设计了ppt B和ppt C，ppt B和ppt C公共的部分就是模板A了，如果它们的公共部分需要改动，则只需要改动模板A就可以了，不需要重新对ppt B和ppt C进行改动。而辐射式设计，比如某个电梯都装了某种报警器，一旦要更新报警器，就必须全部更新。也就是说对于抽象类，如果需要添加新的方法，可以直接在抽象类中添加具体的实现，子类可以不进行变更；而对于接口则不行，如果接口进行了变更，则所有实现这个接口的类都必须进行相应的改动。

参考文档： https://www.cnblogs.com/dolphin0520/p/3811437.html

## 进程和线程的区别

进程：是并发执行的程序在执行过程中**分配和管理资源**的基本单位，是一个动态概念，竞争计算机系统资源的基本单位。
线程：是进程的一个执行单元，比进程更小的独立运行的基本单位。线程也被称为轻量级进程。

一个程序至少有一个进程，一个进程至少有一个线程。进程有独立的地址空间，一个进程崩溃后，在保护模式下不会对其它进程产生影响，而线程只是一个进程中的不同执行路径。线程有自己的堆栈和局部变量，但线程之间没有单独的地址空间，一个线程死掉就等于整个进程死掉，所以多进程的程序要比多线程的程序健壮，但在进程切换时，耗费资源较大，效率要差一些。但对于一些要求同时进行并且又要共享某些变量的并发操作，只能用线程，不能用进程。

区别：

进程和线程最大的区别在于不同的操作系统资源管理方式：

||线程|进程
---|---|---
地址空间| 同一进程的线程间共享 | 进程间相互独立、不可见
并发性 | 高 | 低
资源开销 | 小，不利于资源管理 | 大，方便资源管理和利用
资源拥有 | 共享CPU 内存 I/O | 进程间资源相互独立
通信 | 管道，消息队列，信号量，共享存储，Socket, Streams | 共享内存，同步，wait/notify,管道通信

如何选用：

对资源的管理和保护要求高，不限制开销和效率时，使用多进程。
要求效率高，频繁切换时，资源的保护管理要求不是很高时，使用多线程。

## 同步方法和同步块的区别

同步方法：默认使用this（成员方法）或者当前类的class对象（类方法）作为锁进行同步，使用关键字 synchronized修饰方法。

同步块：自定义锁，比同步方法要更细颗粒度，我们可以选择只同步会发生同步问题的部分代码而不是整个方法，用synchronized（object）{代码内容}进行修饰。

## 死锁

定义：指多个进程因竞争资源而造成的一种僵局（互相等待），若无外力作用，这些进程都将无法向前推进。

> 产生死锁的4个必要条件：

- 互斥条件：同一个资源只能有一个进程占有，不能有两个或者两个以上的占有。
- 不可抢占条件：在一个进程所获取的资源在未使用完毕之前，资源申请者不能强行的从资源占有者手中抢夺资源，资源只能由占有者释放。
- 请求和保持条件：进程已经占有了一个资源，但是有申请新的资源；但是新申请的资源已经被别的进程占有了，此时该进程就会阻塞，但是在获取申请的资源之前他还会一直占有已占有的那个资源。
- 循环等待条件：存在一个循环等待序列，p1等待p2,p2等待p3,p3等待p1。形成一个进程循环等待。

> 如何避免死锁：

(1)打破互斥条件：允许进程同时访问某些资源，但是，有的资源不允许被同时访问，就像打印机，这是由资源的本身来决定的，所以这个方法并没有什么实用的价值。

(2)打破不可抢占的条件：就是说允许进程强行从资源的占有者那里抢夺资源。这种方法实现起来很困难，会降低性能。

(3)打破占有申请条件：可以实现资源预先分配策略，在进程运行前一次性向系统申请他所需要的全部资源。如果进程所需的资源不能满足，则不分配任何资源，进程暂时不运行。(问题：1.在很多时候，一个进程在执行之前不可能知道它所有的全部资源，进程在执行的过程中，是动态的。2.资源利用率低。3.降低进程的并发性，因为资源有效，有加上存在浪费，能分配的所需全部资源的进程个数必然很少。)

(4)打破循环等待条件：实行资源的有序分配策略，把资源事先分类编号，按号分配，使进程在申请，占用资源时候不能形成环路，所有进程对资源的请求必须严格按照资源号递增的顺序提出，进程占用了小号的资源，才能申请大号资源。就会形成环路。(缺点：限制进程对资源的请求，同时对系统中的所有资源合理编号也是很有困难的，增加额外的系统开销。)

> 死锁例子：

进程A占有对象1的锁，进程B占有对象2的锁，进程A需要对象2的锁才能继续执行，所以进程A会等待进程B释放对象2的锁，而进程B需要对象1的锁才能继续执行，同样会等待进程A释放对象1的锁，由于这两个进程都不释放已占有的锁，所以导致他们进入无限等待中。

``` java

// LockTest.java
import java.util.Date;
 
public class LockTest {
   public static String obj1 = "obj1";
   public static String obj2 = "obj2";
   public static void main(String[] args) {
      LockA la = new LockA();
      new Thread(la).start();
      LockB lb = new LockB();
      new Thread(lb).start();
   }
}
class LockA implements Runnable{
   public void run() {
      try {
         System.out.println(new Date().toString() + " LockA 开始执行");
         while(true){
            synchronized (LockTest.obj1) {
               System.out.println(new Date().toString() + " LockA 锁住 obj1");
               Thread.sleep(3000); // 此处等待是给B能锁住机会
               synchronized (LockTest.obj2) {
                  System.out.println(new Date().toString() + " LockA 锁住 obj2");
                  Thread.sleep(60 * 1000); // 为测试，占用了就不放
               }
            }
         }
      } catch (Exception e) {
         e.printStackTrace();
      }
   }
}
class LockB implements Runnable{
   public void run() {
      try {
         System.out.println(new Date().toString() + " LockB 开始执行");
         while(true){
            synchronized (LockTest.obj2) {
               System.out.println(new Date().toString() + " LockB 锁住 obj2");
               Thread.sleep(3000); // 此处等待是给A能锁住机会
               synchronized (LockTest.obj1) {
                  System.out.println(new Date().toString() + " LockB 锁住 obj1");
                  Thread.sleep(60 * 1000); // 为测试，占用了就不放
               }
            }
         }
      } catch (Exception e) {
         e.printStackTrace();
      }
   }
}
```

程序输出：

Mon Jul 16 22:40:18 CST 2018LockA 开始执行
Mon Jul 16 22:40:18 CST 2018 LockA 锁住 obj1
Mon Jul 16 22:40:18 CST 2018 LockB 开始执行
Mon Jul 16 22:40:18 CST 2018 LockB 锁住 obj2

**如何解决死锁：**

为了解决这个问题，我们不使用显示的去锁，我们用信号量去控制。

信号量可以控制资源能被多少线程访问，这里我们指定只能被一个线程访问，就做到了类似锁住。而信号量可以指定去获取的超时时间，我们可以根据这个超时时间，去做一个额外处理。

对于无法成功获取的情况，一般就是重复尝试，或指定尝试的次数，也可以马上退出。

``` java
import java.util.Date;
import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;
 
public class UnLockTest {
   public static String obj1 = "obj1";
   public static final Semaphore a1 = new Semaphore(1);
   public static String obj2 = "obj2";
   public static final Semaphore a2 = new Semaphore(1);
 
   public static void main(String[] args) {
      LockAa la = new LockAa();
      new Thread(la).start();
      LockBb lb = new LockBb();
      new Thread(lb).start();
   }
}
class LockAa implements Runnable {
   public void run() {
      try {
         System.out.println(new Date().toString() + " LockA 开始执行");
         while (true) {
            if (UnLockTest.a1.tryAcquire(1, TimeUnit.SECONDS)) {
               System.out.println(new Date().toString() + " LockA 锁住 obj1");
               if (UnLockTest.a2.tryAcquire(1, TimeUnit.SECONDS)) {
                  System.out.println(new Date().toString() + " LockA 锁住 obj2");
                  Thread.sleep(60 * 1000); // do something
               }else{
                  System.out.println(new Date().toString() + "LockA 锁 obj2 失败");
               }
            }else{
               System.out.println(new Date().toString() + "LockA 锁 obj1 失败");
            }
            UnLockTest.a1.release(); // 释放
            UnLockTest.a2.release();
            Thread.sleep(1000); // 马上进行尝试，现实情况下do something是不确定的
         }
      } catch (Exception e) {
         e.printStackTrace();
      }
   }
}
class LockBb implements Runnable {
   public void run() {
      try {
         System.out.println(new Date().toString() + " LockB 开始执行");
         while (true) {
            if (UnLockTest.a2.tryAcquire(1, TimeUnit.SECONDS)) {
               System.out.println(new Date().toString() + " LockB 锁住 obj2");
               if (UnLockTest.a1.tryAcquire(1, TimeUnit.SECONDS)) {
                  System.out.println(new Date().toString() + " LockB 锁住 obj1");
                  Thread.sleep(60 * 1000); // do something
               }else{
                  System.out.println(new Date().toString() + "LockB 锁 obj1 失败");
               }
            }else{
               System.out.println(new Date().toString() + "LockB 锁 obj2 失败");
            }
            UnLockTest.a1.release(); // 释放
            UnLockTest.a2.release();
            Thread.sleep(10 * 1000); // 这里只是为了演示，所以tryAcquire只用1秒，而且B要给A让出能执行的时间，否则两个永远是死锁
         }
      } catch (Exception e) {
         e.printStackTrace();
      }
   }
}
```

## finalize()方法

finalize()方法什么时候被调用?析构函数(finalization)的目的是什么？

调用时机：当垃圾回收器要宣告一个对象死亡时，至少要经过两次标记过程：如果对象在进行可达性分析后发现没有和GC Roots相连接的引用链，就会被第一次标记，并且判断是否执行finalizer( )方法，如果对象覆盖finalizer()方法且未被虚拟机调用过，那么这个对象会被放置在F-Queue队列中，并在稍后由一个虚拟机自动建立的低优先级的Finalizer线程区执行触发finalizer( )方法，但不承诺等待其运行结束。

finalization的目的：对象逃脱死亡的最后一次机会。（只要重新与引用链上的任何一个对象建立关联即可。）但是不建议使用，运行代价高昂，不确定性大，且无法保证各个对象的调用顺序。可用try-finally或其他替代。

GC决定回收某对象的时候，就会运行对象的finalize()方法，一般情况下不用重写该方法。

最主要的用途是，回收特殊渠道申请的内存，**native方法**。例如使用JNI调用C++程序的时候，finalize()方法就回收这部分的内存。

## System.gc()和Runtime.gc()会做什么事情？

这两个方法用来提示JVM要进行垃圾回收。但是，立即开始还是延迟进行垃圾回收是取决于JVM的。

## JVM内存分配策略、各个代区、GC类别

### 各个代区

> 年轻代(Young Generation)：

所有新生对象首先放在年轻代中。年轻代的目标就是尽可能快速的收集掉那些生
命周期短的对象。年轻代分三个区。一个Eden区，两个 Survivor区(一般而言)。大部分对象在
Eden区中生成。当Eden区满时，还存活的对象将被复制到Survivor区（两个中的一个），当这
个 Survivor区满时，此区的存活对象将被复制到另外一个Survivor区，当这个Survivor去也满了
的时候，从第一个Survivor区复制过来的并且此时还存活的对象，将被复制“年老区
(Tenured)”。需要注意，Survivor的两个区是对称的，没先后关系，所以同一个区中可能同时
存在从Eden复制过来对象，和从前一个Survivor复制过来的对象，而复制到年老区的只有从第
一个Survivor去过来的对象。而且，Survivor区总有一个是空的。同时，根据程序需要，
Survivor区是可以配置为多个的（多于两个），这样可以增加对象在年轻代中的存在时间，减
少被放到年老代的可能。

> 年老代(Old Generation)：

在年轻代中经历了N次垃圾回收后仍然存活的对象，就会被放到年老代中。因此，可以认
为年老代中存放的都是一些生命周期较长的对象。

> 永久代(Permanent Generation):

用于存放静态文件，如今Java类、方法等。持久代对垃圾回收没有显著影响，但是有些应 
用可能动态生成或者调用一些class，例如Hibernate 等，在这种时候需要设置一个比较大的持 
久代空间来存放这些运行过程中新增的类。持久代大小通过-XX:MaxPermSize=<N>进行设置.

### GC类别

- Minor GC: 从年轻代空间（包括 Eden 和 Survivor 区域）回收内存被称为 Minor GC, 因大多数新生对象生命周期很短，所以MinorGC通常很频繁，回收速度也较快;
- FullGC: 针对整个新生代、老生代、元空间（metaspace，java8以上版本取代永久代(perm gen)）的全局范围的GC, 执行频率低，回收速度慢。
- Major GC: 定义不清楚，有的地方同FullGC，有的地方指的是清理永久代。

### 分配策略

1. 对象优先在Eden分配

一般情况下，对象会在新生代的Eden区分配，Eden区没有足够空间时，虚拟机会 发起一次MinorGC；当MinorGC时，若无法放入survivor空间，就会再通过分配担保机制转移到老年代中；

2. 大对象直接进入老年代

大对象是指需要连续内存空间的对象，最典型的大对象是那种很长的字符串以及数组。
经常出现大对象会提前触发垃圾收集以获取足够的连续空间分配给大对象。
-XX:PretenureSizeThreshold，大于此值的对象直接在老年代分配，避免在 Eden 区和 Survivor 区之间的大量内存复制。

3. 长期存活的对象进入老年代

通过 -XX:MaxTenuringThreshold参数设置；每MinorGC一次还存活在Survivor中，则年龄加1；

4. 动态对象年龄判定

虚拟机并不是永远地要求对象的年龄必须达到 MaxTenuringThreshold 才能晋升老年代，如果在 Survivor 区中相同年龄所有对象大小的总和大于 Survivor 空间的一半，则年龄大于或等于该年龄的对象可以直接进入老年代，无需等到 MaxTenuringThreshold 中要求的年龄。

5. 空间分配担保

在发生 Minor GC 之前，虚拟机先检查老年代最大可用的连续空间是否大于新生代所有对象总空间，如果条件成立的话，那么 Minor GC 可以确认是安全的；如果不成立的话虚拟机会查看 HandlePromotionFailure 设置值是否允许担保失败，如果允许那么就会继续检查老年代最大可用的连续空间是否大于历次晋升到老年代对象的平均大小，如果大于，将尝试着进行一次 Minor GC，尽管这次 Minor GC 是有风险的；如果小于，或者 HandlePromotionFailure 设置不允许冒险，那这时也要改为进行一次 Full GC。

### 触发条件

MinorGC: Eden区满时触发；FullGC也会伴随有MinorGC；通常会使得Old gen变大；

FullGC：
a. MinorGC触发前，检查历次进入老年代的平均大小，若小于则FullGC；
b. 如果有永久代（perm gen），在不足哆分配时，触发FullGC；
c. 调用System.gc()，提醒JVM FullGC，但不可控；