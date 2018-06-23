---
title: groovy学习笔记
date: 2018-06-22 15:02:19
tags: groovy
categories:
  - 编程
  - groovy
---

# 概述

Groovy是一种基于Java平台的面向对象语言。 Groovy 1.0于2007年1月2日发布，其中Groovy 2.4是当前的主要版本。 Groovy通过Apache License v 2.0发布。

Groovy的特点
Groovy中有以下特点:

- 同时支持静态和动态类型。
- 支持运算符重载。
- 本地语法列表和关联数组。
- 对正则表达式的本地支持。
- 种标记语言，如XML和HTML原生支持。
- Groovy对于Java开发人员来说很简单，因为Java和Groovy的语法非常相似。
- 您可以使用现有的Java库。
- Groovy扩展了java.lang.Object。

# 用法

groovy 大部分的语法和java相同，并且支持静态类型。

``` groovy
def x = 5
x = "jack"
println x
```

## 数值和表达式

- 支持的数据类型：  
byte short int long float double char boolean String 同java 对应的类封装类型

其中整数是Intgerger类的实例，有小数部分的数据是BigDecimal类的实例

- 特殊运算符：

范围运算符：

``` groovy
def range = 0..5
println range // 输出： [0,1,2,3,4,5]
```

**groovy 支持运算符的[重载](https://www.cnblogs.com/rollenholt/archive/2013/10/02/3349047.html)**

**动态类型：**

def variable_name   
动态类型在运行时确定，不在编译的时候确定，类似java的多态

## 字符串和正则表达式

### 字符串字面值
单引号：所见即所得 
双引号：解释性字符串 
三引号：解释性字符串 多行文本用这个

``` groovy
def age=25
'My age is ${age}'    //My age is ${age}
"My age is ${age}"    //My age is 25
"""//My age is ${age}"""    //My age is 25
"""My 
age 
is \${age}"""    //My age is ${age}
```

### 字符串索引

``` groovy
def greeting='Hello World'
greeting[4]        //o
greeting[-1]    //d
greeting[1..2]    //el
greeting[1..<3]        //el, 等价于greeting[1..2]
greeting[4..2]        //oll
greeting[4,1,6]        //oew
```

groovy 中的字符串是有序序列，单个字符可以通过位置访问，支持负索引。

### 字符串操作

参考链接：[groovy字符串](https://www.w3cschool.cn/groovy/groovy_strings.html)

``` groovy
def greeting='Hello world'
'Hello'+'world'        //Helloworld
'Hello'*3            //HelloHelloHello
greeting-'o world'    //Hell
greeting.size()        //11
greeting.length()    //11
greeting.count('o')        //2
greeting.contains('ell')    //true
```

索引类似java String 的 charAt()方法获取指定位置的字符

### 字符串方法

def message='Hello'
message.center(11)    //返回长度为11，左右两边均使用空格填充的字符串
message.center(3)
message.center(11,'#')
message.eachMatch('.')

### 正则表达式

~’regex’定义正则表达式

def regex=~’cheese’

if(‘cheesecake’=~’cheese’) //左边String对象和右边的正则匹配，返回布尔值

==~ 精确匹配 ‘cheesecake’==~’cheese’

\\\\在正则中表示一个反斜杠字符
类似于java中正则的使用，用单引号括起 
也可以用js的方法，直接使用，不用引号

1 def matcher="\$abc."=~\\\$(.*)\\.
2 def matcher="\$abc."=~/\$(.*)\./

## 方法

``` groovy
def methodName() { 
   //Method code 
}

// 不用显式指定参数名字
def methodName(parameter1, parameter2, parameter3) { 
   // Method code goes here 
}

// 默认参数
def someMethod(para1,para2=0,para3=0){
    // Method code goes here 
}

// 返回值
return 可选，省略了，代码的最后一条语句的值就是方法的返回值

```

groovy 定义方法时不用显示指定参数类型

## groovy 列表、映射、范围

### 列表

在java list基础上进行了一些扩展

[11，12，13，14] - 整数值列表
['Angular'，'Groovy'，'Java'] - 字符串列表
[1，2，[3，4]，5] - 嵌套列表
['Groovy'，21，2.11] - 异构的对象引用列表
[] - 一个空列表

``` groovy
def list = ['hello', 12, 1.5]
println list[0..1]
list << 'world' 
println list[0..-1]

//输出如下：
[hello, 12]
[hello, 12, 1.5, world]

```

 <<  把新元素追加到列表末尾 调用#leftShift
 +   连接两个列表          调用#plus
 -   从列表删除元素        调用#minus
 number[1]=[33,44]        调用#putAt

### 映射

类似 java 的Map

``` groovy

// 1. 访问 调用getAt
def names=[‘ken’:’Barclay,’John’:’Savage’] 
def divisors=[4:[2],6:[3,2],12:[6,4,3,2]] 
names[‘Ken’] //第一种访问写法 
names.Ken //第二种访问写法 
divisors[6]

// 2. 赋值 调用putAt
divisors[6]=[6,3,2,1]

// 3. 空映射
[:] 空映射

// 4. 映射方法
def map=['ken':2745,'john':2746,'sally':2742]
map.get('david',9999)    //9999
map.get('sally')        //2742
map.keySet()            //[david,ken,sally,john]
mp['ken']                //2745
mp.values.asList()
```

### 范围

1..10 - 包含范围的示例
1 .. <10 - 独占范围的示例, 排除最后一个数值
'a'..'x' - 范围也可以由字符组成
10..1 - 范围也可以按降序排列
'x'..'a' - 范围也可以由字符组成并按降序排列。

## 类

如果不声明public/private等访问权限的话，Groovy中类及其变量默认都是public的。

在使用默认修饰符的时候，自动生成隐藏的getter和setter方法，不过也可以直接访问

其余和java差不多

## 闭包

闭包，英文叫Closure，是Groovy中非常重要的一个数据类型或者说一种概念了。闭包，是一种数据类型，它代表了一段可执行的代码。

``` groovy
def aClosure = {//闭包是一段代码，所以需要用花括号括起来..  
    String param1, int param2 ->  //这个箭头很关键。箭头前面是参数定义，箭头后面是代码  
    println"this is code" //这是代码，最后一句是返回值，  
   //也可以使用return，和Groovy中普通函数一样  
}  
```

closure 的定义格式为：

``` groovy
def xxx = {paramters -> code}  

//或者  
def xxx = {无参数，纯code}
```

闭包的访问：

``` groovy
aClosure.call("this is string",100)  

//或者  
aClosure("this is string", 100)
```

闭包的默认参数，it, 类似 this

``` groovy
def fun2 = {
    it-> "dsdsd"
}

println( fun2.call())
```

闭包默认参数的覆盖

``` groovy
def fun3 = {
    -> "dsdsd"
}

println( fun3.call())
fun3.call("d")  //执行这个方法的时候就会报错
```

### 闭包和函数的区别

## 接口

## 特征(trait)

特征是语言的构造，允许

- 行为的组成
- 接口的运行时实现
- 与静态类型检查/编译的兼容性

traint 可以被看做是承载默认实现和状态的接口

traint 的实现

``` groovy
class Example {
   static void main(String[] args) {
      Student st = new Student();
      st.StudentID = 1;
      st.Marks1 = 10; 
      println(st.DisplayMarks());
   } 
} 

trait Marks { 
   int Marks1;
   void DisplayMarks() {
      println("Display Marks");
   } 
} 

class Student implements Marks { 
   int StudentID
}
```

traint 用于行为的构成：

特征可以用于以受控的方式实现多重继承，避免[钻石问题](https://www.cnblogs.com/lenomirei/p/5490714.html)。
在下面的代码示例中，我们定义了两个特征 - Marks和Total。我们的Student类实现了两个特征。由于学生类扩展了这两个特征，它能够访问这两种方法 - DisplayMarks和DisplayTotal。

``` groovy
class Example {
   static void main(String[] args) {
      Student st = new Student();
      st.StudentID = 1;
		
      println(st.DisplayMarks());
      println(st.DisplayTotal()); 
   } 
} 

trait Marks {
   void DisplayMarks() {
      println("Marks1");
   } 
} 

trait Total {
   void DisplayTotal() { 
      println("Total");
   } 
}  

class Student implements Marks,Total {
   int StudentID 
}   
```

扩展特征

特征可能扩展另一个特征，在这种情况下，必须使用extends关键字。在下面的代码示例中，我们使用Marks trait扩展了Total trait。

``` groovy
class Example {
   static void main(String[] args) {
      Student st = new Student();
      st.StudentID = 1;
      println(st.DisplayMarks());
   } 
} 

trait Marks {
   void DisplayMarks() {
      println("Marks1");
   } 
} 

trait Total extends Marks {
   void DisplayMarks() {
      println("Total");
   } 
}  

class Student implements Total {
   int StudentID 
}
```

总的来说，特征可以实现接口，可以被类实现，可以继承其他的特征，相比java, groovy的这一个特性相当于实现了多继承

## xml

MarkupBuilder用于构造整个XML文档。通过首先创建XML文档类的对象来创建XML文档

xml 构建

``` groovy
import groovy.xml.MarkupBuilder 

class Example {
   static void main(String[] args) {
      def mB = new MarkupBuilder()
		
      // Compose the builder
      mB.collection(shelf : 'New Arrivals') {
         movie(title : 'Enemy Behind')
         type('War, Thriller')
         format('DVD')
         year('2003')
         rating('PG')
         stars(10)
         description('Talk about a US-Japan war') 
      }
   } 
}

// 得到如下结果

<collection shelf = 'New Arrivals'> 
   <movie title = 'Enemy Behind' /> 
      <type>War, Thriller</type> 
      <format>DVD</format> 
      <year>2003</year> 
      <rating>PG</rating> 
      <stars>10</stars> 
      <description>Talk about a US-Japan war</description> 
   </movie> 
</collection>

```

xml 解析

``` groovy
import groovy.xml.MarkupBuilder 
import groovy.util.*

class Example {

   static void main(String[] args) { 
	
      def parser = new XmlParser()
      def doc = parser.parse("D:Movies.xml");
		
      doc.movie.each{
         bk->
         print("Movie Name:")
         println "${bk['@title']}"
			
         print("Movie Type:")
         println "${bk.type[0].text()}"
			
         print("Movie Format:")
         println "${bk.format[0].text()}"
			
         print("Movie year:")
         println "${bk.year[0].text()}"
			
         print("Movie rating:")
         println "${bk.rating[0].text()}"
			
         print("Movie stars:")
         println "${bk.stars[0].text()}"
			
         print("Movie description:")
         println "${bk.description[0].text()}"
         println("*******************************")
      }
   }
} 
```

## JSON

| 功能 | 对象
| -- | -- |
JsonSlurper	| JsonSlurper是一个将JSON文本或阅读器内容解析为Groovy数据的类结构，例如地图，列表和原始类型，如整数，双精度，布尔和字符串。
JsonOutput | 此方法负责将Groovy对象序列化为JSON字符串。

json 解析

``` groovy
http.request( GET, TEXT ) {
   headers.Accept = 'application/json'
   headers.'User-Agent' = USER_AGENT
	
   response.success = { 
      res, rd ->  
      def jsonText = rd.text 
		
      //Setting the parser type to JsonParserLax
      def parser = new JsonSlurper().setType(JsonParserType.LAX)
      def jsonResp = parser.parseText(jsonText)
   }
}

// 解析文本数据

class Example {
   static void main(String[] args) {
      def jsonSlurper = new JsonSlurper()
      def object = jsonSlurper.parseText('{ "name": "John", "ID" : "1"}') 
		
      println(object.name);
      println(object.ID);
   } 
}

// 解析整数列表

class Example {
   static void main(String[] args) {
      def jsonSlurper = new JsonSlurper()
      Object lst = jsonSlurper.parseText('{ "List": [2, 3, 4, 5] }')
      lst.each { println it }
   } 
}

// 解析基本数据类型列表

class Example {

   static void main(String[] args) {
      def jsonSlurper = new JsonSlurper()
      def obj = jsonSlurper.parseText ''' {"Integer": 12, "fraction": 12.55, "double": 12e13}'''
		
      println(obj.Integer);
      println(obj.fraction);
      println(obj.double); 
   } 
}

```

json 输出

``` groovy

import groovy.json.JsonOutput 
class Example {
   static void main(String[] args) {
      def output = JsonOutput.toJson([name: 'John', ID: 1])
      println(output);  
   }
}

// 输出
{"name":"John","ID":1}

// 作用于普通 groovy 对象

class Example {
   static void main(String[] args) {
      def output = JsonOutput.toJson([ new Student(name: 'John',ID:1),
         new Student(name: 'Mark',ID:2)])
      println(output);  
   } 
}
 
class Student {
   String name
   int ID; 
}

```

# DSLS

Groovy允许在顶层语句的方法调用的参数周围省略括号。这被称为“命令链”功能。这个扩展的工作原理是允许一个人链接这种无括号的方法调用，在参数周围不需要括号，也不需要链接调用之间的点。

DSL或域特定语言旨在简化以Groovy编写的代码，使得它对于普通用户变得容易理解。以下示例显示了具有域特定语言的确切含义。




参考资料：
- [Groovy 使用完全解析](https://blog.csdn.net/zhaoyanjun6/article/details/70313790)  
- [Groovy基础——接口的实现方式](https://blog.csdn.net/qjbagu/article/details/49725905)
- [Groovy教程](https://www.w3cschool.cn/groovy/groovy_dsls.html)