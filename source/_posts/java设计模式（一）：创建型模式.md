---
title: java设计模式（一）：创建型模式
tags:
  - java
  - 设计模式
categories:
  - 笔记
  - java
date: 2017-04-07 22:39:05
---

[参考链接：http://www.cnblogs.com/maowang1991/archive/2013/04/15/3023236.html](http://www.cnblogs.com/maowang1991/archive/2013/04/15/3023236.html)
http://blog.csdn.net/u013142781/article/details/50816245
## 一、设计模式分类

总体来说设计模式分为三大类：

创建型模式，共五种：工厂方法模式、抽象工厂模式、单例模式、建造者模式、原型模式。

结构型模式，共七种：适配器模式、装饰器模式、代理模式、外观模式、桥接模式、组合模式、享元模式。

行为型模式，共十一种：策略模式、模板方法模式、观察者模式、迭代子模式、责任链模式、命令模式、备忘录模式、状态模式、访问者模式、中介者模式、解释器模式。

其实还有两类：并发型模式和线程池模式。用一个图片来整体描述一下：
![java设计模式之间的关系](/images/java设计模式（一）：创建型模式-1.jpg)

##  二、设计模式的六大原则
- 开闭原则（Open Close Principle）
开闭原则就是说对扩展开放，对修改关闭。在程序需要进行拓展的时候，不能去修改原有的代码，实现一个热插拔的效果。所以一句话概括就是：为了使程序的扩展性好，易于维护和升级。想要达到这样的效果，我们需要使用接口和抽象类，后面的具体设计中我们会提到这点。

- 里氏代换原则（Liskov Substitution Principle）
里氏代换原则(Liskov Substitution Principle LSP)面向对象设计的基本原则之一。 里氏代换原则中说，任何基类可以出现的地方，子类一定可以出现。 LSP是继承复用的基石，只有当衍生类可以替换掉基类，软件单位的功能不受到影响时，基类才能真正被复用，而衍生类也能够在基类的基础上增加新的行为。

- 依赖倒转原则（Dependence Inversion Principle）
这个是开闭原则的基础，具体内容：真对接口编程，依赖于抽象而不依赖于具体。

- 接口隔离原则（Interface Segregation Principle）
这个原则的意思是：使用多个隔离的接口，比使用单个接口要好。还是一个降低类之间的耦合度的意思，从这儿我们看出，其实设计模式就是一个软件的设计思想，从大型软件架构出发，为了升级和维护方便。所以上文中多次出现：降低依赖，降低耦合。

- 迪米特法则（最少知道原则）（Demeter Principle）
一个实体应当尽量少的与其他实体之间发生相互作用，使得系统功能模块相对独立。

- 合成复用原则（Composite Reuse Principle）
原则是尽量使用合成/聚合的方式，而不是使用继承。

## 三、工厂方法模式
### 1、普通工厂模式
建立一个工厂类，对实现了同一接口的类进行实例的创建

``` java
# 创建类的共同接口
======
Active.java
======
public Interface Active{
  public void eat();
  public void spark();
}

# 创建实现类
======
Cat.java
======
public class Cat implements Active{
  @Override
  pubic void eat(){
    System.out.println("I eat fish!");
  }
}

======
Dog.java
======
public class Dog implements Active{
  @Override
  pubic void eat(){
    System.out.println("I eat meat!");
  }
}

# 创建工厂类
======
ActiveFactory.java
======
public class ActiveFactory{
  public Active produce(String type){
    if(type.equals("cat")){
      return new Cat();
    }else if(type.equals("dog")){
      return new Dog();
    }else {
      System.out.println("Error! please intput correct type!");
      return null;
    }
  }

  # test
  public static void main(String[] args){
    ActiveFactory activeFactory = new ActiveFactory();
    Active active = activeFactory.produce("cat");
    active.eat(); //output ===> "I eat fish!"
  }
}

```

### 2.多个工厂方法模式
在普通工厂方法模式中，如果传递的字符串出错，则不能正确创建对象，而多个工厂方法模式是提供多个工厂方法，分别创建对象
``` java
======
ActiveFactory.java
======
public class ActiveFactory{
  public Active produceCat(){
    return new Cat();
  }
  public Active produceDog(){
    return new Dog();
  }

  # test
  public static void main(String[] args){
    ActiveFactory activeFactory = new ActiveFactory();
    Active active = activeFactory.produceCat("cat");
    active.eat(); //output ===> "I eat fish!"
  }

}

```

### 3.静态工厂模式
将多个工厂方法模式中的方法置为静态的，不需要创建实例，直接调用即可
``` java
======
ActiveFactory.java
======
public class ActiveFactory{
  public static Active produceCat(){
    return new Cat();
  }
  public static Active produceDog(){
    return new Dog();
  }

  # test
  public static void main(String[] args){
    Active active = ActiveFactory.produceCat("cat");
    active.eat(); //output ===> "I eat fish!"
  }

}

```

## 四、抽象工厂模式
在一般的工厂模式中，类的创建依赖工厂类，如果要扩展程序，必须对工厂类进行修改，违背了闭包原则，因此
需要用抽象工厂模式，创建多个工厂类，一旦需要增加新的功能，直接增加新的工厂类就可以
``` java
======
Provider.java
======
public interface Provider{
  public Active produce();
}

======
ActiveCatFactory.java
======
public class ActiveCatFactory implements Provider{
  public Active produce(){
    return new Cat();
  }
}

======
ActiveDogFactory.java
======
public class ActiveDogFactory implements Provider{
  public Active produce(){
    return new Dog();
  }
}

```

## 五、单例模式
在Java应用中，单例对象能保证在一个JVM中，该对象只有一个实例存在.

``` java
======
Singleton.java
======
public class Singleton {  

    /* 持有私有静态实例，防止被引用，此处赋值为null，目的是实现延迟加载 */  
    private static Singleton instance = null;  

    /* 私有构造方法，防止被实例化 */  
    private Singleton() {  
    }  

    /* 静态工程方法，创建实例 */  
    public static Singleton getInstance() {  
        if (instance == null) {  
            instance = new Singleton();  
        }  
        return instance;  
    }  

    /* 如果该对象被用于序列化，可以保证对象在序列化前后保持一致 */  
    public Object readResolve() {  
        return instance;  
    }  
}
```
这个类可以满足基本要求，但是，像这样毫无线程安全保护的类，如果我们把它放入多线程的环境下，肯定就会出现问题了，如何解决？我们首先会想到对getInstance方法加synchronized关键字，如下：

``` java
public static synchronized Singleton getInstance() {  
        if (instance == null) {  
            instance = new Singleton();  
        }  
        return instance;  
    }
```
但是，synchronized关键字锁住的是这个对象，这样的用法，在性能上会有所下降，因为每次调用getInstance()，都要对对象上锁，事实上，只有在第一次创建对象的时候需要加锁，之后就不需要了，所以，这个地方需要改进。我们改成下面这个：
``` java
public static Singleton getInstance() {  
        if (instance == null) {  
            synchronized (instance) {  
                if (instance == null) {  
                    instance = new Singleton();  
                }  
            }  
        }  
        return instance;  
    }  
```
改程序在多线程的时候还是有可能发生错误，因此需要进一步改善
``` java
private static class SingletonFactory{           
        private static Singleton instance = new Singleton();           
    }           
    public static Singleton getInstance(){           
        return SingletonFactory.instance;           
    }   
```
[参考链接](http://www.cnblogs.com/maowang1991/archive/2013/04/15/3023236.html)

## 六、建造者模式
工厂类模式提供的是创建单个类的模式，而建造者模式则是将各种产品集中起来进行管理，用来创建复合对象，所谓复合对象就是指某个类具有不同的属性，其实建造者模式就是前面抽象工厂模式和最后的Test结合起来得到的。

``` java
public class Builder {  

    private List<Active> list = new ArrayList<Active>();  

    public void produceCatActive(int count){  
        for(int i=0; i<count; i++){  
            list.add(new Cat());  
        }  
    }  

    public void produceDogActive(int count){  
        for(int i=0; i<count; i++){  
            list.add(new Dog());  
        }  
    }

    public static void main(String[] args){
      Builder builder = new Builder();  
      builder.produceMailSender(10);
    }  
}  

```
## 七、原型模式
原型模式虽然是创建型的模式，但是与工程模式没有关系，从名字即可看出，该模式的思想就是将一个对象作为原型，对其进行复制、克隆，产生一个和原对象类似的新对象。本小结会通过对象的复制，进行讲解。在Java中，复制对象是通过clone()实现的，先创建一个原型类：
``` java
public class Prototype implements Cloneable {  

    public Object clone() throws CloneNotSupportedException {  
        Prototype proto = (Prototype) super.clone();  
        return proto;  
    }  
}  
```
深浅复制的例子：
``` java
public class Prototype implements Cloneable, Serializable {  

    private static final long serialVersionUID = 1L;  
    private String string;  

    private SerializableObject obj;  

    /* 浅复制 */  
    public Object clone() throws CloneNotSupportedException {  
        Prototype proto = (Prototype) super.clone();  
        return proto;  
    }  

    /* 深复制 */  
    public Object deepClone() throws IOException, ClassNotFoundException {  

        /* 写入当前对象的二进制流 */  
        ByteArrayOutputStream bos = new ByteArrayOutputStream();  
        ObjectOutputStream oos = new ObjectOutputStream(bos);  
        oos.writeObject(this);  

        /* 读出二进制流产生的新对象 */  
        ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());  
        ObjectInputStream ois = new ObjectInputStream(bis);  
        return ois.readObject();  
    }  

    public String getString() {  
        return string;  
    }  

    public void setString(String string) {  
        this.string = string;  
    }  

    public SerializableObject getObj() {  
        return obj;  
    }  

    public void setObj(SerializableObject obj) {  
        this.obj = obj;  
    }  

}  

class SerializableObject implements Serializable {  
    private static final long serialVersionUID = 1L;  
}
```
