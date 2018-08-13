---
title: java8习惯用语
tags:
  - 编程
  - java
categories:
  - 编程
  - java
date: 2018-08-09 10:02:40
---

# Java 8 习惯用语

本文介绍了java 8 的常用用法： 2017年 IBM developerWorks 最受欢迎的内容

# Java 中的一种更轻松的函数式编程途径

## 命令式格式

下面代码使用命令式格式寻找数组中的目标元素：
``` java
import java.util.*;
 
public class FindNemo {
  public static void main(String[] args) {
    List<String> names = 
      Arrays.asList("Dory", "Gill", "Bruce", "Nemo", "Darla", "Marlin", "Jacques");
 
    findNemo(names);
  }                 
   
  public static void findNemo(List<String> names) {
    boolean found = false;
    for(String name : names) {
      if(name.equals("Nemo")) {
        found = true;
        break;
      }
    }
     
    if(found)
      System.out.println("Found Nemo");
    else
      System.out.println("Sorry, Nemo not found");
  }
}

```

## 声明式格式

直接采用集合的contains接口来判断是否存在目标元素：
``` java
public static void findNemo(List<String> names) {
  if(names.contains("Nemo"))
    System.out.println("Found Nemo");
  else
    System.out.println("Sorry, Nemo not found");
}
```

## 函数式格式

尽管函数式格式的编程始终是声明式的，但简单地使用声明式编程并不等于函数式编程。这是因为函数式编程合并了声明式方法与高阶函数。图 1 直观地展示了命令式、声明式和函数式编程之间的关系。

图 1. 命令式、声明式和函数式编程的联系

![](https://www.ibm.com/developerworks/cn/java/j-java8idioms1/fig1.jpg)

该例子实现了统计站点访问次数的功能：
``` java
package com.raorao.java.base;

import java.util.HashMap;
import java.util.Map;
import java.util.stream.Stream;

/**
 * hash 的函数式编程用法，统计网站访问次数.
 *
 * @author Xiong Raorao
 * @since 2018-08-09-10:18
 */
public class MapMergeTest {

  public static void main(String[] args) {
    String[] websites = new String[] {"https://www.baidu.com", "https://www.baidu.com",
        "https://www.baidu.com", "https://www.google.com", "https://www.google.com",
        "https://www.sina.com.cn"};
    Map<String, Integer> map1 = new HashMap<>();
    Map<String, Integer> map2 = new HashMap<>();
    merge(map1, websites);
    incrementPageVisit(map2, websites);
    map1.forEach((k,v)-> System.out.print("k= " +k + ", v= " + v + "\t"));
    System.out.println();
    map2.forEach((k,v)-> System.out.print("k= " +k + ", v= " + v + "\t"));
  }

  public static void merge(Map<String, Integer> map, String[] args) {
    Stream.of(args).forEach((e) -> map.merge(e, 1, (oldValue, value) -> (oldValue + value)));
  }

  public static void incrementPageVisit(Map<String, Integer> pageVisits, String[] pages) {
    for (String page : pages) {
      if (!pageVisits.containsKey(page)) {
        pageVisits.put(page, 0);
      }
      pageVisits.put(page, pageVisits.get(page) + 1);
    }
  }
}

```

merge 函数中使用了map的merge接口，merge函数有四个参数，前面两个参数分别是要处理的key，初始值（如果键不存在，则赋值1），第三个参数（一个拉姆达表达式）接收 map 中该键对应的值作为其参数，并且将该值作为变量传递给 merge 方法中的第二个参数。（拉姆达表达式第二个参数为统计后的新值，实际上就是递增计数）

下面看看Map.merge()实现的源码，merge函数直接在Map接口中作为默认函数实现的（java 8 引入了接口的默认函数），其实就是把key的旧值得到，然后加上第二个参数。

``` java
default V merge(K key, V value,
            BiFunction<? super V, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(remappingFunction);
        Objects.requireNonNull(value);
        V oldValue = get(key);
        V newValue = (oldValue == null) ? value :
                   remappingFunction.apply(oldValue, value);
        if(newValue == null) {
            remove(key);
        } else {
            put(key, newValue);
        }
        return newValue;
    }
```

[参考链接](https://www.ibm.com/developerworks/cn/java/j-java8idioms1/index.html)


## 函数组合与集合管道模式

利用java 8 stream的特性，实现集合的自定义遍历

``` java

package agiledeveloper;
 
public class Car {
  private String make;
  private String model;
  private int year;
   
  public Car(String theMake, String theModel, int yearOfMake) {
    make = theMake;
    model = theModel;
    year = yearOfMake;
  }                   
   
  public String getMake() { return make; }
  public String getModel() { return model; }
  public int getYear() { return year; }
}

public class Iterating {
  public static List<Car> createCars() {
    return Arrays.asList(
      new Car("Jeep", "Wrangler", 2011),
      new Car("Jeep", "Comanche", 1990),
      new Car("Dodge", "Avenger", 2010),
      new Car("Buick", "Cascada", 2016),
      new Car("Ford", "Focus", 2012),
      new Car("Chevrolet", "Geo Metro", 1992)
    );
}

public static List<String> getModelsAfter2000UsingPipeline(
   List<Car> cars) {
   return 
     cars.stream()
         .filter(car -> car.getYear() > 2000)
         .sorted(Comparator.comparing(Car::getYear))
         .map(Car::getModel)
         .collect(toList());
 }
```
[参考链接](http://www.ibm.com/developerworks/cn/java/j-java8idioms2/index.html)

## 传统 for 循环的函数式替代方案

传统的for循环会产生垃圾变量,代码量较大。采用函数式for循环更加简洁

例子：循环产生多个线程

``` java
IntStream.range(0, 5)
       .forEach(i -> 
         executorService.submit(() -> System.out.println("Running task " + i)));
```

封闭范围（闭区间）

``` java
IntStream.rangeClosed(0, 5)
```

跳过值（等差或者自己实现跳过的方法）

``` java
IntStream.iterate(1, e -> e + 3)
  .limit(34)
  .sum()
```

逆向迭代

``` java
IntStream.iterate(7, e -> e - 1)
     .limit(7)
```

[参考链接](https://www.ibm.com/developerworks/cn/java/j-java8idioms3/index.html)

## 提倡有帮助的编码

使用函数式编码可以让代码更便于理解。下面是找出一个数组中名字长度为4的例子，使用函数式编程非常简洁易懂。

``` java
List<String> names = Arrays.asList("Jack", "Jill", "Nate", "Kara", "Kim","Jullie", "Paul", "Peter");
         
System.out.println(
  names.stream()
    .filter(name -> name.length() == 4)
    .collect(Collectors.joining(", ")));

```

## 传递表达式（pass-through lambdas）的替代方案(使用方法引用)

``` java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
 
numbers.stream()
  .filter(e -> e % 2 == 0)
  .forEach(e -> System.out.println(e));

```

第二行forEach 循环里面对于元素并没有进行实际的操作，只是传入了元素的值，因此在这种情况下可以采用元素的方法引用来代替。

``` java
numbers.stream()
  .filter(e -> e % 2 == 0)
  .forEach(System.out::println);
```

[参考链接](http://www.ibm.com/developerworks/cn/java/j-java8idioms4/index.html)

### 实例方法的实参传递

![](https://www.ibm.com/developerworks/cn/java/j-java8idioms5/figure1.jpg)

我们使用以下格式将此 lambda 表达式替换为方法引用 System.out::println：referenceToInstance::methodName。

例如传递一个this上面的实参

``` java
public class Example {
  public int increment(int number) {
    return number + 1;
  }
 
  //...
}

Stream
.map(e -> increment(e)) 
.map(e -> this.increment(e))
.map(this::increment)
```

### 静态方法的实参传递

ClassName::staticMethodName

``` java
.map(Integer::valueOf)
```

### 将形参传递给目标

![](https://www.ibm.com/developerworks/cn/java/j-java8idioms5/figure2.jpg)

这个和上面的静态方法的实参传格式是一样的，但是这里是将**形参作为实例方法的调用目标**。

``` java
.map(e -> e.doubleValue())
```

在这个示例中，形参 e（我们假设其推断类型为 Integer）是对 doubleValue 方法的调用的目标。上图中给出了这种传递 lambda 表达式的结构。

### 传递构造函数调用

``` java
.collect(toCollection(() -> new LinkedList<Double>()));
```

代码的目的是获取一个数据 Stream ，将它精减或收集到一个 LinkedList 中。toCollection 方法接受一个 Supplier 作为其实参。Supplier 不接受任何形参，因此 () 为空。它返回一个 Collection 实例，该实例在本例中是 LinkedList。


![](https://www.ibm.com/developerworks/cn/java/j-java8idioms5/figure3.jpg)

``` java
.collect(toCollection(LinkedList::new));
```

### 传递多个实参

``` java
.reduce(0, (total, e) -> Integer.sum(total, e)));
```

![](https://www.ibm.com/developerworks/cn/java/j-java8idioms5/figure4.jpg)

``` java
.reduce(0, Integer::sum));
```

[参考链接](https://www.ibm.com/developerworks/cn/java/j-java8idioms5/index.html)

## 为什么完美的 lambda 表达式只有一行

[参考链接](https://www.ibm.com/developerworks/cn/java/j-java8idioms6/index.html)

## 函数接口

Thread 类的构造函数想要一个实现 Runnable 的实例。在本例中，我们传递了一个 lambda 表达式，而不是传递一个对象。我们可以选择向各种各样的方法和构造函数传递 lambda 表达式，包括在 Java 8 之前创建的一些方法和构造函数。这很有效，因为 lambda 表达式在 Java 中表示为函数接口。

``` java
Thread thread = new Thread(() -> System.out.println("In another thread"));
```

函数接口有 3 条重要法则：

一个函数接口只有一个抽象方法。
在 Object 类中属于公共方法的抽象方法不会被视为单一抽象方法。
函数接口可以有默认方法和静态方法。

**任何满足单一抽象方法法则的接口，都会被自动视为函数接口。这包括 Runnable 和 Callable 等传统接口，以及您自己构建的自定义接口。**

### 内置函数接口

除了已经提到的单一抽象方法之外，JDK 8 还包含多个新函数接口。最常用的接口包括 Function<T, R>、Predicate<T> 和 Consumer<T>，它们是在 java.util.function 包中定义的。Stream 的 map 方法接受 Function<T, R> 作为参数。类似地，filter 使用 Predicate<T>，forEach 使用 Consumer<T>。该包还有其他函数接口，比如 Supplier<T>、BiConsumer<T, U> 和 BiFunction<T, U, R>。

可以将内置函数接口用作我们自己的方法的参数。例如，假设我们有一个 Device 类，它包含方法 checkout 和 checkin 来指示是否正在使用某个设备。当用户请求一个新设备时，方法 getFromAvailable 从可用设备池中返回一个设备，或在必要时创建一个新设备。

``` java
public void borrowDevice(Consumer<Device> use) {
  Device device = getFromAvailable();
   
  device.checkout();
   
  try {
    use.accept(device);      
  } finally {
    device.checkin();
  }
}
```
borrowDevice 方法：

- 接受 Consumer<Device> 作为参数。
- 从池中获取一个设备（我们在这个示例中不关心线程安全问题）。
- 调用 checkout 方法将设备状态设置为 checked out。
- 将设备交付给用户。

在完成设备调用后返回到 Consumer 的 accept 方法时，通过调用 checkin 方法将设备状态更改为 checked in。

下面给出了一种使用 borrowDevice 方法的方式：

new Sample().borrowDevice(device -> System.out.println("using " + device));
因为该方法接收一个函数接口作为参数，所以传入一个 lambda 表达式作为参数是可以接受的。

### 自定义函数接口

尽管最好尽量使用内置函数接口，但有时需要自定义函数接口。

要创建自己的函数接口，需要做两件事：

使用 @FunctionalInterface 注释该接口，这是 Java 8 对自定义函数接口的约定。
确保该接口只有一个抽象方法。
该约定清楚地表明该接口应接收 lambda 表达式。当编译器看到该注释时，它会验证该接口是否只有一个抽象方法。

使用 @FunctionalInterface 注释可以确保，如果在未来更改该接口时意外违反抽象方法数量规则，您会获得错误消息。这很有用，因为您会立即发现问题，而不是留给另一位开发人员在以后处理它。没有人希望在将 lambda 表达式传递给其他人的自定义接口时获得错误消息。

### 创建自定义函数接口

作为一个示例，我们将创建一个 Order 类，它有一系列 OrderItem 以及一个转换并输出它们的方法。我们首先创建一个接口。

下面的代码将创建一个 Transformer 函数接口。

``` java
@FunctionalInterface
public interface Transformer<T> {
  T transform(T input);
}
```

该接口用 @FunctionalInterface 注释做了标记，表明它是一个函数接口。因为该注释包含在 java.lang 包中，所以没有必要导入。该接口有一个名为 transform 的方法，后者接受一个参数化为 T 类型的对象，并返回一个相同类型的转换后对象。转换的语义将由该接口的实现来决定。

这是 OrderItem 类：

``` java
public class OrderItem {
  private final int id;
  private final int price;
   
  public OrderItem(int theId, int thePrice) {
    id = theId;
    price = thePrice;
  }
   
  public int getId() { return id; }
  public int getPrice() { return price; }
   
  public String toString() { return String.format("id: %d price: %d", id, price); }
}
```
OrderItem 是一个简单的类，它有两个属性：id 和 price，以及一个 toString 方法。

现在来看看 Order 类。

``` java
import java.util.*;
import java.util.stream.Stream;
 
public class Order {
  List<OrderItem> items;
   
  public Order(List<OrderItem> orderItems) {
    items = orderItems;
  }
   
  public void transformAndPrint(
    Transformer<Stream<OrderItem>> transformOrderItems) {
     
    transformOrderItems.transform(items.stream())
      .forEach(System.out::println);
  }
}
```
transformAndPrint 方法接受 Transform<Stream<OrderItem> 作为参数，调用 transform 方法来转换属于 Order 实例的订单项，然后按转换后的顺序输出这些订单项。

这是一个使用该方法的样本：

``` java
import java.util.*;
import static java.util.Comparator.comparing;
import java.util.stream.Stream;
import java.util.function.*;
 
class Sample {     
  public static void main(String[] args) {
    Order order = new Order(Arrays.asList(
      new OrderItem(1, 1225),
      new OrderItem(2, 983),
      new OrderItem(3, 1554)
    ));
     
     
    order.transformAndPrint(new Transformer<Stream<OrderItem>>() {
      public Stream<OrderItem> transform(Stream<OrderItem> orderItems) {
        return orderItems.sorted(comparing(OrderItem::getPrice));
      }
    });
  }
}
```
我们传递一个匿名内部类作为 transformAndPrint 方法的参数。在 transform 方法内，调用给定流的 sorted 方法，这会对订单项进行排序。这是我们的代码的输出，其中显示了按价格升序排列的订单项：

id: 2 price: 983
id: 1 price: 1225
id: 3 price: 1554
lambda 表达式的强大功能
在任何需要函数接口的地方，我们都有 3 种选择：

传递一个匿名内部类。
传递一个 lambda 表达式。
在某些情况下传递一个方法引用而不是 lambda 表达式。
传递匿名内部类的过程很复杂，我们只能传递方法引用来替代直通 lambda 表达式。考虑如果我们重写对 transformAndPrint 函数的调用，以使用 lambda 表达式来代替匿名内部类，将会发生什么：

order.transformAndPrint(orderItems -> orderItems.sorted(comparing(OrderItem::getPrice)));
与我们最初提供的匿名内部类相比，这简洁得多且更容易阅读。

[参考链接](https://www.ibm.com/developerworks/cn/java/j-java8idioms7/index.html)

## Java 知道您的类型

java 类型推断

[参考链接](https://www.ibm.com/developerworks/cn/java/j-java8idioms8/index.html)

## 级联 lambda 表达式

类似建造者模式一样，处理完对象然后返回出来

首先，考虑区分高阶函数与常规函数的规则：

- 常规函数

可以接收对象
可以创建对象
可以返回对象

- 高阶函数

可以接收函数
可以创建函数
可以返回函数

``` java
List<Integer> result1 = numbers1.stream()
  .filter(e -> e > 50)
  .collect(toList());
```

[参考链接](https://www.ibm.com/developerworks/cn/java/j-java8idioms9/index.html)

## 使用闭包捕获状态

在 Java™ 编程中，我们以不严格地使用术语 lambda 表达式来表示 lambda 表达式和闭包。但在某些情况下，理解它们的区别很重要。lambda 表达式是无状态的，而闭包是带有状态的。将 lambda 表达式替换为闭包，是一种管理函数式程序中的状态的好方法。

**闭包：是引用了自由变量的函数。这个函数通常被定义在另一个外部函数中，并且引用了外部函数中的变量。**

java 中闭包有多种方式：

1. 内部类
2. .new : 显示创建内部类的实例
3. .this : 显式引用外部类的变量
4. 局部内部类
5. 匿名内部类
6. final 关键字

闭包的价值：是引用了自由变量的函数。这个函数通常被定义在另一个外部函数中，并且引用了外部函数中的变量。

``` java
public static void print() {
  String location = "World";
   
  Runnable runnable = () -> System.out.println("Hello " + location);
   
  runnable.run();
}
```

得益于 lambda 表达式，代码变得更简洁，但 location 的范围和绑定没有更改。lambda 表达式中的变量 location 被绑定到 lambda 表达式的词法范围中的变量 location。严格来讲，此代码中的 lambda 表达式是一个闭包。

### 闭包携带状态

``` java
class Sample {
  public static void call(Runnable runnable) {
    System.out.println("calling runnable");
                    
    //level 2 of stack
    runnable.run();
  } 
   
  public static void main(String[] args) { 
    int value = 4;  //level 1 of stack
    call(
      () -> System.out.println(value) //level 3 of stack
    );
  }
}
```

此代码中的闭包使用了来自它的词法范围的变量 value。如果 main 是在堆栈级别 1 上执行的，那么 call 方法的主体会在堆栈级别 2 上执行。因为 Runnable 的 run 方法是从 call 内调用的，所以该闭包的主体会在级别 3 上运行。如果 call 方法要将该闭包传递给另一个方法（进而推迟调用的位置），则执行的堆栈级别可能高于 3。