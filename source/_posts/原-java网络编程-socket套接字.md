---
title: '[原]java网络编程--socket套接字'
tags: [java, 笔记]
categories: [IT, java]
date: 2016-10-11 15:18:06
---

## **Java 网络编程**
网络编程是指编写运行在多个设备（计算机）的程序，这些设备都通过网络连接起来。
java.net包中J2SE的API包含有类和接口，它们提供低层次的通信细节。你可以直接使用这些类和接口，来专注于解决问题，而不用关注通信细节。
java.net包中提供了两种常见的网络协议的支持：

- TCP： TCP是传输控制协议的缩写，它保障了两个应用程序之间的可靠通信。通常用于互联网协议，被称TCP / IP。
- UDP:UDP是用户数据报协议的缩写，一个无连接的协议。提供了应用程序之间要发送的数据的数据包。

**Socket 编程:** 这是使用最广泛的网络概念，它已被解释地非常详细。
套接字使用TCP提供了两台计算机之间的通信机制。 客户端程序创建一个套接字，并尝试连接服务器的套接字。当连接建立时，服务器会创建一个Socket对象。客户端和服务器现在可以通过对Socket对象的写入和读取来进行进行通信。

当使用套接字建立TCP链接时有4个步骤：

- 服务器实例化一个ServerSocket对象
- 服务器调用ServerSocket类的accept()方法等待客户端的连接
- 服务器正在等待的过程中，一个客户端实例化一个Socket对象，指定服务器和端口号，发送连接请求
- 在服务器端，accept()方法返回一个Socket的引用，该socket和客户端的socket相连接

**URL 处理:** 这部分会在另外的篇幅里讲，点击这里更详细地了解在Java语言中的URL处理。

示例程序:

``` java
//服务器端程序，文件名HelloServer.java

import java.net.*;
import java.io.*;
public class HelloServer extends Thread
{
	private ServerSocket serverSocket;
	//constructor
	public HelloServer(int port) throws IOException
	{
		//1.创建绑定到特定端口的服务器套接字
		serverSocket=new ServerSocket(port);
		serverSocket.setSoTimeout(20000);//休眠20ms
	}
	@Override
	public void run()
	{
		while(true)
		{
			try
			{
				System.out.println("Waiting for client on prot "
					 +serverSocket.getLocalPort()+"...");

				//2.服务器端调用ServerSocket类的accept（）方法，侦听并接受到此套接字的连接。
				Socket server=serverSocket.accept();

				System.out.println("Just connected to "+server.getRemoteSocketAddress());
				//返回此套接字绑定的端点的地址，如果尚未绑定则返回 null。

				//服务器端接收来数据
				DataInputStream in=new DataInputStream(server.getInputStream());
				System.out.println(in.readUTF());

				//服务端发送数据
				DataOutputStream out=new DataOutputStream(server.getOutputStream());
				out.writeUTF("Thanks for connneciton to"+ server.getLocalSocketAddress()
				+"\nGoodbye!");

				//关闭客户端
				server.close();
			}catch(SocketTimeoutException s)
			{
				System.out.println("Socket timed out!");
				break;
			}catch(IOException e)
			{
				e.printStackTrace();
				break;
			}
		}

	}
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		int port=6063;
		try
		{
			Thread t=new HelloServer(port);
			t.start();
		}catch(IOException e)
		{
			e.printStackTrace();
		}
	}

}
```  

``` java
//客户端程序，文件名HelloClient.java

import java.net.*;
import java.io.*;

public class HelloClient {

	public static void main(String[] args) {
		// TODO Auto-generated method stub

		//1.设置服务器名称和端口
		String serverName="localhost";
		int port=6063;

		try
		{
			System.out.println("Connecting to "+serverName+ " on port" +port);

			//2.建立socket对象，与服务器通信
			Socket client=new Socket(serverName,port);
			System.out.println("Just connected to "+client.getRemoteSocketAddress());

			//客户端发送数据给服务器端
			OutputStream outToServer=client.getOutputStream();
			DataOutputStream out=new DataOutputStream(outToServer);
			out.writeUTF("hello from"+client.getLocalSocketAddress());

			//客户端接收来自服务器端的数据
			InputStream inFromServer=client.getInputStream();
			DataInputStream in=new DataInputStream(inFromServer);
			System.out.print("server says " +in.readUTF());
			client.close();
		}catch(IOException e)
		{
			e.printStackTrace();
		}
	}

}
```

**编译运行结果：**
![开启两个命令框，最终实现了客户端程序和服务器端程序的通信](http://img.blog.csdn.net/20161011151705700)
