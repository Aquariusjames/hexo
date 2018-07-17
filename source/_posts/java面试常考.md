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
- Major GC: 定义不清楚，有的地方同FullGC（深入理解java虚拟机），有的地方指的是清理永久代。

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

## 串行(serial)收集器和吞吐量(throughput)收集器的区别是什么？

串行GC：整个扫描和复制过程均采用单线程的方式，相对于吞吐量GC来说简单；适合于单CPU、客户端级别。串行收集器在GC时会停止其他所有工作线程（stop-the-world），CPU利用率是最高的，所以适用于要求高吞吐量（throughput）的应用，但停顿时间（pause time）会比较长，所以对web应用来说就不适合，因为这意味着用户等待时间会加长。

吞吐量GC：采用多线程的方式来完成垃圾收集（新生代），适合于吞吐量要求较高的场合，比较适合中等和大规模的应用程序，关注点在于达到一个可控制的吞吐量，另外吞吐量收集器有自适应调节策略的能力。采用并行收集器停顿时间很短，回收效率高，适合高频率执行。

## java中，对象在什么情况下被回收？

当一个对象到GC Roots不可达时，在下一个垃圾回收周期中尝试回收该对象，如果该对象重写了finalize()方法，并在这个方法中成功自救(将自身赋予某个引用)，那么这个对象不会被回收。但如果这个对象没有重写finalize()方法或者已经执行过这个方法，也自救失败，该对象将会被回收。

## JVM的永久代中会发生垃圾回收么？

垃圾回收不会发生在永久代，如果永久代满了或者是超过了临界值，会触发完全垃圾回收(Full GC)。如果你仔细查看垃圾收集器的输出信息，就会发现永久代也是被回收的。这就是为什么正确的永久代大小对避免Full GC是非常重要的原因。请参考下Java8：从永久代到元数据区
(注：Java8中已经移除了永久代，新加了一个叫做元数据区的native内存区)

## java中的异常

java.lang.Throwable 是所有异常的超类

Error: 是错误
Exception: 是异常

### 分类

**1. 受检查异常**

从程序角度来说，是必须经过捕捉处理的异常，要么使用try...catch,要么使用throws 语句声明抛出，否则编译通不过

例如java.io.IOException

**2. 不受检查异常（运行时异常，RuntimeException）**

运行时发生，即时不用try...catch或者throws语句声明，会编译通过。例如java.lang.NullPointerException
写程序的时候尽量避免

### Error

当程序发生不可控的错误是，通常是通知用户并终止程序的运行。例如OutOfMemoryError,动态链接失败，虚拟机错误等。

## 什么是JDBC

JDBC（Java DataBase Connectivity）,是一套面向对象的应用程序接口（API），制定了统一的访问各类关系数据库的标准接口，为各个数据库厂商提供了标准的实现。通过JDBC技术，开发人员可以用纯Java语言和标准的SQL语句编写完整的数据库应用程序，并且真正地实现了软件的跨平台性。

通常情况下使用JDBC完成以下操作：
1.同数据库建立连接；
2.向数据库发送SQL语句；
3.处理从数据库返回的结果；

JDBC具有下列优点：
1.JDBC与ODBC(Open Database Connectivity，即开放数据库互连）十分相似，便于软件开发人员理解；
2.JDBC使软件开发人员从复杂的驱动程序编写工作中解脱出来，可以完全专注于业务逻辑开发；
3.JDBC支持多种关系型数据库，大大增加了软件的可移植性；
4.JDBC API是面向对象的，软件开发人员可以将常用的方法进行二次封装，从而提高代码的重用性；

JDBC驱动：
JDBC驱动提供了特定厂商对JDBC API接口类的实现，驱动必须要提供java.sql包下面这些类的实现：Connection, Statement, PreparedStatement,CallableStatement, ResultSet和Driver。

一句话总结，**在使用jdbc前，应该保证相应的Driver类已经被加载到jvm中，并且完成了类的初始化工作就行了**

``` java
Class.forName("com.mysql.jdbc.Driver" ); // 初始化参数指定的类，并返回此类对应的对象
com.mysql.jdbc.Driver driver = new com.mysql.jdbc.Driver();
ClassLoader cl = new ClassLoader(); 
cl.loadClass("com.mysql.jdbc.Driver" ); 
// 以上三种方法其实都可以加载驱动
// 加载完驱动就可以使用驱动管理器来建立链接了
Connection con = DriverManager.getConnection(url,user,psw); 
```

## java RMI

java RMI(Remote Method Invocation), java的远程方法调用是java API对远程过程调用（RPC）提供的面向对象的等价形式，能够让某个java虚拟机上的对象像调用本地对象一样调用另一个java虚拟机中对象的方法，支持直接传输序列化的java对象和分布式垃圾回收。

RMI远程调用步骤：

1，客户对象调用客户端辅助对象上的方法

2，客户端辅助对象打包调用信息（变量，方法名），通过网络发送给服务端辅助对象

3，服务端辅助对象将客户端辅助对象发送来的信息解包，找出真正被调用的方法以及该方法所在对象

4，调用真正服务对象上的真正方法，并将结果返回给服务端辅助对象

5，服务端辅助对象将结果打包，发送给客户端辅助对象

6，客户端辅助对象将返回值解包，返回给客户对象

7，客户对象获得返回值

对于客户对象来说，步骤2-6是完全透明的

参考文档：
- [Java RMI详解](https://blog.csdn.net/a19881029/article/details/9465663)

## RMI 体系的基本原则：

RMI体系结构是基于一个非常重要的行为定义和行为实现相分离的原则。RMI允许定义行为的代码和实现行为的代码相分离，并且运行在不同的JVM上。

## RMI体系结构分层

存根和骨架层(Stub and Skeleton layer)：这一层对程序员是透明的，它主要负责拦截客户端发出的方法调用请求，然后把请求重定向给远程的RMI服务。

远程引用层(Remote Reference Layer)：RMI体系结构的第二层用来解析客户端对服务端远程对象的引用。这一层解析并管理客户端对服务端远程对象的引用。连接是点到点的。

传输层(Transport layer)：这一层负责连接参与服务的两个JVM。这一层是建立在网络上机器间的TCP/IP连接之上的。它提供了基本的连接服务，还有一些防火墙穿透策略。

## 分布式垃圾回收

DGC叫做分布式垃圾回收。RMI使用DGC来做自动垃圾回收。因为RMI包含了跨虚拟机的远程对象的引用，垃圾回收是很困难的。DGC使用引用计数算法来给远程对象提供自动内存管理。

概念：
	1)Java虚拟机中，一个远程对象不仅会被本地虚拟机内的变量引用，还会被远程引用。
	2)只有当一个远程对象不受到任何本地引用和远程引用，这个远程对象才会结束生命周期。

说明：
	1)服务端的一个远程对象在3个地方被引用：
		1>服务端的一个本地对象持有它的本地引用
		2>服务端的远程对象已经注册到rmiregistry注册表中，也就是说，rmiregistry注册表持有它的远程引用。
		3>客户端获得远程对象的存根对象，也就是说，客户端持有它的远程引用。
	2)服务端判断客户端是否持有远程对象引用的方法：
		1>当客户端获得一个服务端的远程对象的存根时，就会向服务器发送一条租约(lease)通知，以告诉服务器自己持有了这个远程对象的引用了。
		2>客户端定期地向服务器发送租约通知，以保证服务器始终都知道客户端一直持有着远程对象的引用。
		3>租约是有期限的，如果租约到期了，服务器则认为客户端已经不再持有远程对象的引用了。

## Serializaton 和 Deserialization

序列化和反序列化的对象必须实现serializable 接口，该接口没有抽象方法，只是为了标注对象可以被序列化，然后使用一个输出流就可以构造一个ObjectOutputStream(对象)，调用writeObject方法就可以将任意对象序列化输出，恢复的话则使用输出流。

``` java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.io.Serializable;

/**
 * 对象序列化测试.
 *
 * @author Xiong Raorao
 * @since 2018-07-17-11:05
 */
public class SerializeTest {

  public static void main(String[] args) throws IOException, ClassNotFoundException {
    A a = new A();
    a.setName("hhh");
    ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("D:\\oos"));
    oos.writeObject(a);
    oos.close();

    ObjectInputStream ois = new ObjectInputStream(new FileInputStream("D:\\oos"));
    A aa = (A) ois.readObject();
    System.out.println(aa.getName());
  }

  static class A implements Serializable {

    private String name;

    public String getName() {
      return name;
    }

    public void setName(String name) {
      this.name = name;
    }
  }
}

```

# Java Web

## 什么是 Servlet?

Servlet是用来处理客户端请求并产生动态网页内容的Java类。Servlet主要是用来处理或者是存储HTML表单提交的数据，产生动态内容，在无状态的HTTP协议下管理状态信息。

### servlet的体系结构：

所有的Servlet都必须要实现的核心的接口是javax.servlet.Servlet。每一个Servlet都必须要直接或者是间接实现这个接口，或者是继承javax.servlet.GenericServlet或者javax.servlet.http.HTTPServlet。最后，Servlet使用多线程可以并行的为多个请求服务。

参考文档： 
- [servlet的体系结构](http://blog.csdn.net/a236209186/article/details/51262646)

### servlet的生命周期和工作原理

1. 初始化阶段，调用init()方法
2. 响应用户请求的方法，调用service()方法，自己实现doGet和doPost方法
3. 终止阶段，调用destroy()方法

![](/images/20170928102245610.png)

1：Web Client向Servlet容器(tomcat)发出Http请求 
2：Servlet容器接收Web Client的请求 
3：Servlet容器创建一个HttpRequest对象，将Web Client请求的信息封装到这个对象中。 
4：Servlet容器创建一个HttpResponse对象 
5：Servlet容器调用HttpServlet对象的service方法，把HttpRequest对象与HttpResponse对象作为参数传递给HttpServlet对象。 
6：HttpServlet调用HttpRequest对象的有关方法，获取Http请求信息 
7：HttpServlet调用HttpResponse对象的有关方法，生成响应数据 
8：Servlet容器把HttpServlet的响应结果传入Web Client。

## HTTP响应结构

http响应由三个部分组成：

- 状态码(Status Code)：描述了响应的状态。可以用来检查是否成功的完成了请求。请求失败的情况下，状态码可用来找出失败的原因。如果Servlet没有返回状态码，默认会返回成功的状态码HttpServletResponse.SC_OK。
- HTTP头部(HTTP Header)：它们包含了更多关于响应的信息。比如：头部可以指定认为响应过期的过期日期，或者是指定用来给用户安全的传输实体内容的编码格式。如何在Serlet中检索HTTP的头部看这里。
- 主体(Body)：它包含了响应的内容。它可以包含HTML代码，图片，等等。主体是由传输在HTTP消息中紧跟在头部后面的数据字节组成的。

## cookie和session

cookie是web服务器发送给浏览器的一块信息。浏览器会在本地文件中给每一个web服务器存储cookie。以后浏览器在给特定的Web服务器发请求的时候，同时会发送所有为该服务器存储的cookie，例如密码填充等。

seesion是存储在服务器端的信息，客户端访问服务器的时候，服务器把客户端信息已某种形式记录在服务器上。客户端浏览器再次访问服务器的时候， 服务器只需要在session中查找该用户的状态就可以了。如果说cookie机制是通过检查客户身上的“通信证”，那么session机制就是通过检查服务器上的“客户明细表”来确认客户身份。

区别：

- 存储位置：cookie在客户端浏览器存储，session在服务器端存储
- 安全性：cookie安全性低，session安全性高
- 数据量：单个cookie只能保存不大于4k的数据，且只能是string，很多浏览器一个站点最多保存20个cookie，session可以存储任意的java对象
 
将登陆信息等重要信息存放为SESSION
其他信息如果需要保留，可以放在COOKIE中

## HTTP隧道

HTTP隧道是一种利用HTTP或者是HTTPS把多种网络协议封装起来进行通信的技术。因此，HTTP协议扮演了一个打通用于通信的网络协议的管道的包装器的角色。把其他协议的请求掩盖成HTTP的请求就是HTTP隧道。

## sendRedirect()和forward()方法有什么区别？

sendRedirect()方法会创建一个新的请求，而forward()方法只是把请求转发到一个新的目标上。重定向(redirect)以后，之前请求作用域范围以内的对象就失效了，因为会产生一个新的请求，而转发(forwarding)以后，之前请求作用域范围以内的对象还是能访问的。一般认为sendRedirect()比forward()要慢。

## URL编码和URL解码

URL编码是负责把URL里面的空格和其他的特殊字符替换成对应的十六进制表示，反之就是解码。

## JSP如何被处理的

客户端通过浏览器发送jsp请求，服务器端接受到请求后，判断是否是第一次请求该页面，或者该页面是否改变，若是，服务器将jsp页面翻译为servlet，jvm将servlet编译为.class文件，字节码文件加载到服务器内存上执行，服务器将处理结果以.html页面的形式返回给客户端，若该页面不是第一次请求，则省略翻译和编译的步骤，直接执行。

