---
title: '[原]Java学习笔记（一）'
tags: [java, 笔记]
categories: [IT, java]
date: 2016-08-23 12:58:48
---


### **java的自动回收机制**
> java拥有自动回收机制，也可以手动使用finalize()方法进行释放资源。

### **java的参数传递**
``` java
package com.test3;
public class TestReference {
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		A a1=new A(1,2);
		B b=new B(11,22);
		System.out.println(a1.b);
		a1.test(b);
		System.out.println(a1.b); //java中是值传递，输出都是2
	}
}

class A{
	int a,b;
	A(int a,int b){
		this.a=a;
		this.b=b;
	}
	public void test(B b){
		b.a=a;
	}
}

class B{
	int a,b;
	B(int a,int b){
		this.a=a;
		this.b=b;
	}
	public void setA(int a){
		this.a=a;
	}
}
```
从上面的代码看出，java的对象引用的传递和C++的不同，java的引用传递相当于值传递，C++的引用传递相当于传递的是地址。

### **java类的引用**

### **修饰符**

|        |类      |成员变量       |成员方法|
|--------|:------:|:-----------:|---:|
|public  |包外可见|包外可见|包外可见|
|abstract|申明抽象类|X|抽象方法|
|final   |不能有子类|最终域修饰符|最终方法控制符|
|static |X|静态域修饰符|静态方法控制符|
|volatile|X|共享域修饰符|X|
|transient|X|暂时性域修饰符|X|
|private|X|类内调用|类内调用|
|protected|X|父子类之间调用|父子类之间调用|
|native|X|X|本地方法控制符|
|synchronized|X|X|同步方法控制符|
