---
title: java核心技术（一）
tags:
  - java
categories:
  - 笔记
  - java
date: 2017-04-21 09:06:51
---

### java基本程序设计结构

#### 1.java字符串

1. java编译器让不可变的字符串共享，所有的不可变字符串会存放在公共的存储池中，字符串变量指向存储池中的字符串的相应位置。类似于c++中的字符串指针，一旦字符串没人用，java自动回收机制会自动回收该字符串。

2. 如何判断String字符串是否为空？
``` java
case 1: 判断java字符串是否为空
======
if(str.length()==0)
或 if(str.equals(""))

case 2: 判断是否为null
======
if(str == null)
这两种方式不一样，null表示当前的String变量没有与对象关联。

```

3. 常用String API
 - trim() 去掉原始字符串头尾空格
 - lasetIndexOf(String str) 返回str匹配的第一个子串的开始位置
 - toLowerCase() toUpperCase()

#### 2.java输入输出

1. 读取文本输入
``` java
Scanner in = new Scanner(System.in);
String name = in.nextLine();//输入如果有空格采用该种方法
String sex = in.next();//输入空格隔开调用该方法
int age = in.nextInt();//读取整数,nextDouble类似返回双精度浮点数
```

2. 读取密码输入
``` java
Console cons = System.console();//console对象每次只能读取一行输入
String user = cons.readLine("User name: ");
char[] passwd = cons.readPassword("Password: ");
```
3. 大数值的计算
java的基本数据类型：

类型  |  字节
--|--
int  |  4
short  | 2
logn | 8
byte | 1
float | 4
double | 8

NaN：当数据溢出时显示无穷大

java提供了BigInteger和BigDecimal两个类，可以处理包含任意长度数字序列的数值，分别实现了任意精度的整数运算和浮点数运算。

4. java数组
for each循环: for(T element: collection<T>){};
匿名数组 new String[] {"ss","rr"};
**java中允许数组长度为0，但数组长度为0不等于null**
new elementType[0];// 不是null
数组拷贝：Arrays.copyOf(T[] arrayVar, int lenth);

#### java对象和类

1. java运行内存管理
[http://www.importnew.com/21463.html](http://www.importnew.com/21463.html)
[http://www.cnblogs.com/gw811/archive/2012/10/18/2730117.html](http://www.cnblogs.com/gw811/archive/2012/10/18/2730117.html)
[堆和栈的区别](http://blog.csdn.net/hairetz/article/details/4141043/)

2. 访问时间
Date()类：
``` java
Date date = new Date();
date.getTime();// = System.currentTimeMillis()

//还可以使用 LocalDate()和Calendar()这两个类

```

3. java编译
javac 类名.java
java有两种编译方式：显示编译和隐式编译。显示编译某个文件时，如果该文件调用了其他的类，则会对引用的类进行编译操作，如果被引用的类被修改了，则会重新编译新版本的.class文件

4. java封装
java类的成员都应该设置为私有的，否则很危险。通常使用共有的方法来对私有成员进行读写。成员方法大部分设计为共有方法，一旦设计成公有的，不能随便删除，因为其他的代码可能依赖它。

5. 静态导入包
``` java
import static java.lang.System.*;
out.println("hello world!");//静态导入包，不用加类名前缀就可以直接访问方法。
```
