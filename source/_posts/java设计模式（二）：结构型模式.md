---
title: java设计模式（二）：结构型模式
tags:
  - java
  - 设计模式
categories:
  - 笔记
  - java
date: 2017-04-07 22:39:19
---

参考链接：
[Java开发中的23种设计模式详解(转)](http://www.cnblogs.com/maowang1991/archive/2013/04/15/3023236.html)  
[Java经典设计模式之五大创建型模式（附实例和详解）](http://blog.csdn.net/u013142781/article/details/50816245)

## 一、结构型模式分类：
一共七种：适配器模式、装饰器模式、代理模式、外观模式、桥接模式、组合模式、享元模式。其中适配器模式主要分为三类：类的适配器模式、对象的适配器模式、接口的适配器模式。其中的**对象的适配器模式**是各种结构型模式的起源。

![结构型模式](images/design-pattern-struct.png)

## 二、适配器模式：
适配器模式主要分为三类：类的适配器模式、对象的适配器模式、接口的适配器模式。

适配器模式将某个类的接口转换成客户端期望的另一个接口表示，目的是消除由于接口不匹配所造成的类的兼容性问题。有点抽象，我们来看看详细的内容。

应用场景：

- 类的适配器模式：当希望将一个类转换成满足另一个新接口的类时，可以使用类的适配器模式，创建一个新类，继承原有的类，实现新的接口即可。

- 对象的适配器模式：当希望将一个对象转换成满足另一个新接口的对象时，可以创建一个Wrapper类，持有原类的一个实例，在Wrapper类的方法中，调用实例的方法就行。

- 接口的适配器模式：当不希望实现一个接口中所有的方法时，可以创建一个抽象类Wrapper，实现所有方法，我们写别的类的时候，继承抽象类即可。

## 三、装饰模式：

装饰模式：在不必改变原类文件和使用继承的情况下，动态地扩展一个对象的功能。它是通过创建一个包装对象，也就是装饰来包裹真实的对象。

关系图：
<div><img src="http://dl.iteye.com/upload/attachment/0083/1195/e1b8b6a3-0150-31ae-8f77-7c3d888b6f80.jpg" width="" /></div><br/>

装饰模式的特点：

（1） 装饰对象和真实对象有相同的接口。这样客户端对象就能以和真实对象相同的方式和装饰对象交互。
（2） 装饰对象包含一个真实对象的引用（reference）
（3） 装饰对象接受所有来自客户端的请求。它把这些请求转发给真实的对象。
（4） 装饰对象可以在转发这些请求以前或以后增加一些附加功能。这样就确保了在运行时，不用修改给定对象的结构就可以在外部增加附加的功能。在面向对象的设计中，通常是通过继承来实现对给定类的功能扩展。继承不能做到这一点，继承的功能是静态的，不能动态增删。
``` java

# 1. sourceable
public interface Sourceable {  
  public void method();  
}

# 2. source
public class Source implements Sourceable {  
  
  @Override  
  public void method() {  
      System.out.println("the original method!");  
  }  
}

# 3. decorator

public class Decorator implements Sourceable {  
  
  private Sourceable source;  
    
  public Decorator(Sourceable source){  
      super();  
      this.source = source;  
  }  
  @Override  
  public void method() {  
      System.out.println("before decorator!");  
      source.method();  
      System.out.println("after decorator!");  
  }  
}
# 4. test

public class DecoratorTest {  
  
  public static void main(String[] args) {  
      Sourceable source = new Source();  
      Sourceable obj = new Decorator(source);  
      obj.method();  
  }  
} 
```
装饰器模式的应用场景：

1、需要扩展一个类的功能。

2、动态的为一个对象增加功能，而且还能动态撤销。（继承不能做到这一点，继承的功能是静态的，不能动态增删。）

缺点：产生过多相似的对象，不易排错！

## 四、代理模式：

代理模式就是多一个代理类出来，替原对象进行一些操作。代理类就像中介，它比我们掌握着更多的信息。

关系图：
<div align="center"> <img src="http://dl.iteye.com/upload/attachment/0083/1197/ea094ad9-efc5-337d-a8e8-ce9223511144.jpg" width=""/> </div><br>

``` java

# 3. proxy

public class Proxy implements Sourceable {  
  
  private Source source;  
  public Proxy(){  
      super();  
      this.source = new Source();  
  }  
  @Override  
  public void method() {  
      before();  
      source.method();  
      atfer();  
  }  
  private void after() {  
      System.out.println("after proxy!");  
  }  
  private void before() {  
      System.out.println("before proxy!");  
  }  
}

# 4. test

public class ProxyTest {  
  
  public static void main(String[] args) {  
      Sourceable source = new Proxy();  
      source.method();  
  }  
  
}  

```

代理模式的应用场景：

如果已有的方法在使用的时候需要对原有的方法进行改进，此时有两种办法：

1、修改原有的方法来适应。这样违反了“对扩展开放，对修改关闭”的原则。

2、就是采用一个代理类调用原有的方法，且对产生的结果进行控制。这种方法就是代理模式。

使用代理模式，可以将功能划分的更加清晰，有助于后期维护！

## 五、外观模式：

外观模式是为了解决类与类之间的依赖关系的，像spring一样，可以将类和类之间的关系配置到配置文件中，而外观模式就是将他们的关系放在一个Facade类中，降低了类类之间的耦合度，该模式中没有涉及到接口。

关系图：
<div align="center"> <img src="http://dl.iteye.com/upload/attachment/0083/1199/eebe2103-6ced-35f2-8664-3a2e8a557f81.jpg" width=""/> </div><br>


## 六、桥接模式：

在软件系统中，某些类型由于自身的逻辑，它具有两个或多个维度的变化，那么如何应对这种“多维度的变化”？如何利用面向对象的技术来使得该类型能够轻松的沿着多个方向进行变化，而又不引入额外的复杂度？这就要使用Bridge模式。

在提出桥梁模式的时候指出，桥梁模式的用意是**将抽象化(Abstraction)与实现化(Implementation)脱耦，使得二者可以独立地变化**。这句话有三个关键词，也就是抽象化、实现化和脱耦。

- 抽象化：存在于多个实体中的共同的概念性联系，就是抽象化。作为一个过程，抽象化就是忽略一些信息，从而把不同的实体当做同样的实体对待。
- 实现化：抽象化给出的具体实现，就是实现化。
- 脱耦：所谓耦合，就是两个实体的行为的某种强关联。而将它们的强关联去掉，就是耦合的解脱，或称脱耦。在这里，脱耦是指将抽象化和实现化之间的耦合解脱开，或者说是将它们之间的强关联改换成弱关联

关系图：
<div align="center"> <img src="http://dl.iteye.com/upload/attachment/0083/1201/35f0b172-b976-3757-bb51-c65d5c9ce68e.jpg" width=""/> </div><br>

``` java
public interface Sourceable {  
    public void method();  
}  

public class SourceSub1 implements Sourceable {  
  
    @Override  
    public void method() {  
        System.out.println("this is the first sub!");  
    }  
} 

public class SourceSub2 implements Sourceable {  
  
    @Override  
    public void method() {  
        System.out.println("this is the second sub!");  
    }  
}  

public abstract class Bridge {  
    private Sourceable source;  
  
    public void method(){  
        source.method();  
    }  
      
    public Sourceable getSource() {  
        return source;  
    }  
  
    public void setSource(Sourceable source) {  
        this.source = source;  
    }  
}  

public class MyBridge extends Bridge {  
    public void method(){  
        getSource().method();  
    }  
} 

public class BridgeTest {  
      
    public static void main(String[] args) {  
          
        Bridge bridge = new MyBridge();  
          
        /*调用第一个对象*/  
        Sourceable source1 = new SourceSub1();  
        bridge.setSource(source1);  
        bridge.method();  
          
        /*调用第二个对象*/  
        Sourceable source2 = new SourceSub2();  
        bridge.setSource(source2);  
        bridge.method();  
    }  
} 
```

这样，就通过对Bridge类的调用，实现了对接口Sourceable的实现类SourceSub1和SourceSub2的调用。接下来我再画个图，大家就应该明白了，因为这个图是我们JDBC连接的原理，有数据库学习基础的，一结合就都懂了。

<div align="center"> <img src="http://dl.iteye.com/upload/attachment/0083/1203/6f713d07-1409-3312-99c9-fa6b0909f0b2.jpg" width=""/> </div><br>

## 七、组合模式
组合模式，将对象组合成树形结构以表示“部分-整体”的层次结构，组合模式使得用户对单个对象和组合对象的使用具有一致性。掌握组合模式的重点是要理解清楚 “部分/整体” 还有 ”单个对象“ 与 “组合对象” 的含义。

组合模式让你可以优化处理递归或分级数据结构。

《设计模式》：将对象组合成树形结构以表示“部分整体”的层次结构。组合模式使得用户对单个对象和组合对象的使用具有一致性。

涉及角色：

Component：是组合中的对象声明接口，在适当的情况下，实现所有类共有接口的默认行为。声明一个接口用于访问和管理Component子部件。

Leaf：在组合中表示叶子结点对象，叶子结点没有子结点。

Composite：定义有枝节点行为，用来存储子部件，在Component接口中实现与子部件有关操作，如增加(add)和删除(remove)等。

比如现实中公司内各部门的层级关系，请看代码：

Component：是组合中的对象声明接口，在适当的情况下，实现所有类共有接口的默认行为。声明一个接口用于访问和管理Component子部件。

关系图：

<div align="center"> <img src="http://dl.iteye.com/upload/attachment/0083/1205/09cab656-5ff9-380e-9df1-326339ac3509.jpg" width=""/> </div><br>

``` java
public class TreeNode {  
      
    private String name;  
    private TreeNode parent;  
    private Vector<TreeNode> children = new Vector<TreeNode>();  
      
    public TreeNode(String name){  
        this.name = name;  
    }  
  
    public String getName() {  
        return name;  
    }  
  
    public void setName(String name) {  
        this.name = name;  
    }  
  
    public TreeNode getParent() {  
        return parent;  
    }  
  
    public void setParent(TreeNode parent) {  
        this.parent = parent;  
    }  
      
    //添加孩子节点  
    public void add(TreeNode node){  
        children.add(node);  
    }  
      
    //删除孩子节点  
    public void remove(TreeNode node){  
        children.remove(node);  
    }  
      
    //取得孩子节点  
    public Enumeration<TreeNode> getChildren(){  
        return children.elements();  
    }  
}  

public class Tree {  
  
    TreeNode root = null;  
  
    public Tree(String name) {  
        root = new TreeNode(name);  
    }  
  
    public static void main(String[] args) {  
        Tree tree = new Tree("A");  
        TreeNode nodeB = new TreeNode("B");  
        TreeNode nodeC = new TreeNode("C");  
          
        nodeB.add(nodeC);  
        tree.root.add(nodeB);  
        System.out.println("build the tree finished!");  
    }  
}  
```

应用场景：将多个对象组合在一起进行操作，常用于表示树形结构中，例如二叉树，数等。

## 八、享元模式
享元模式的主要目的是实现对象的共享，即共享池，当系统中对象多的时候可以减少内存的开销，通常与工厂模式一起使用。

一提到共享池，我们很容易联想到Java里面的JDBC连接池，想想每个连接的特点，我们不难总结出：适用于作共享的一些个对象，他们有一些共有的属性，就拿数据库连接池来说，url、driverClassName、username、password及dbname，这些属性对于每个连接来说都是一样的，所以就适合用享元模式来处理，建一个工厂类，将上述类似属性作为内部数据，其它的作为外部数据，在方法调用时，当做参数传进来，这样就节省了空间，减少了实例的数量。

关系图：

<div align="center"> <img src="http://dl.iteye.com/upload/attachment/0083/1207/f7aae0dd-b250-3829-bb07-49d87069bfbb.jpg" width=""/> </div><br>

FlyWeightFactory负责创建和管理享元单元，当一个客户端请求时，工厂需要检查当前对象池中是否有符合条件的对象，如果有，就返回已经存在的对象，如果没有，则创建一个新对象，FlyWeight是超类。一提到共享池，我们很容易联想到Java里面的JDBC连接池，想想每个连接的特点，我们不难总结出：适用于作共享的一些个对象，他们有一些共有的属性，就拿数据库连接池来说，url、driverClassName、username、password及dbname，这些属性对于每个连接来说都是一样的，所以就适合用享元模式来处理，建一个工厂类，将上述类似属性作为内部数据，其它的作为外部数据，在方法调用时，当做参数传进来，这样就节省了空间，减少了实例的数量。

举个例子，数据库连接池：

<div align="center"> <img src="http://dl.iteye.com/upload/attachment/0083/1209/53bc0bf4-cafb-3a12-8574-e20a525f2b72.jpg" width=""/> </div><br>


``` java
public class ConnectionPool {  
      
    private Vector<Connection> pool;  
      
    /*公有属性*/  
    private String url = "jdbc:mysql://localhost:3306/test";  
    private String username = "root";  
    private String password = "root";  
    private String driverClassName = "com.mysql.jdbc.Driver";  
  
    private int poolSize = 100;  
    private static ConnectionPool instance = null;  
    Connection conn = null;  
  
    /*构造方法，做一些初始化工作*/  
    private ConnectionPool() {  
        pool = new Vector<Connection>(poolSize);  
  
        for (int i = 0; i < poolSize; i++) {  
            try {  
                Class.forName(driverClassName);  
                conn = DriverManager.getConnection(url, username, password);  
                pool.add(conn);  
            } catch (ClassNotFoundException e) {  
                e.printStackTrace();  
            } catch (SQLException e) {  
                e.printStackTrace();  
            }  
        }  
    }  
  
    /* 返回连接到连接池 */  
    public synchronized void release() {  
        pool.add(conn);  
    }  
  
    /* 返回连接池中的一个数据库连接 */  
    public synchronized Connection getConnection() {  
        if (pool.size() > 0) {  
            Connection conn = pool.get(0);  
            pool.remove(conn);  
            return conn;  
        } else {  
            return null;  
        }  
    }  
}  

```
