---
title: '[原]Ubuntu下配置java+Eclipse开发环境'
tags: [ubuntu, java, eclipse, 教程]
categories: [解决方案,ubuntu]
date: 2016-12-05 11:26:29
---

### 1、复制文件到待安装目录
cp /media/ubuntu/娱乐/jdk-8u92-linux-x64.tar.gz /opt/software

### 2、提升文件的操作权限（必须做，否则无法配置成功）
chmod +x /opt/software/jdk-8u92-linux-x64.tar.gz

### 3、解压文件
tar zxvf /opt/software/jdk-8u92-linux-x64.tar.gz -C /opt/software/

### 4、添加环境变量
当前用户的path（/home/username/.bashrc 和 .bash_profile）
全局的path（/etc/bashrc 和/etc/profile）

立即生效的方法 source 配置文件
```
export JAVA_HOME=/opt/software/jdk1.8.0_92
export JRE_HOME=/opt/software/jdk1.8.0_92/jre
export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
```

修改默认的JDK（**注意替换路径，如果能直接显示sun的jdk的话就不用操作**）
sudo update-alternatives --install "/usr/bin/java" "java" "/usr/java/jdk_8u60/bin/java" 300
sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/java/jdk_8u60/bin/javac" 300
sudo update-alternatives --install "/usr/bin/javaws" "javaws" "/usr/java/jdk_8u60/bin/javaws" 300

重要参考链接：[http://www.linuxidc.com/Linux/2015-09/122689.htm](http://www.linuxidc.com/Linux/2015-09/122689.htm)

### 5、解压eclipse
### 6、创建快捷方式
sudo gedit /usr/share/applications/eclipse.desktop
然后写入以下语句（绝对不能有空格，否则很苦逼的说）
[Desktop Entry]

Encoding=UTF-8

Name=eclipse

Comment=Eclipse IDE

Exec=/usr/local/eclipse/eclipse_SDK/eclipse

Icon=/usr/local/eclipse/eclipse_SDK/icon.xpm

Terminal=false

StartupNotify=true

Type=Application

Categories=Application;Development;

Exec=/usr/local/eclipse/eclipse_SDK/eclipseIcon=/usr/local/eclipse/eclipse_SDK/icon.xpm 这个地方要修改为你的eclipse安装目录。

### 7、手动把eclipse.desktop拷贝到桌面上（注意系统如果是中文，则/home/username/桌面/为桌面目录）
sudo cp /usr/share/applications/eclipse.desktop /home/ubuntu/桌面

### 8、修改图标文件的访问权限，普通用户没有root权限
sudo chmod 777 /home/ubuntu/桌面/eclipse.desktop
chmod u+x /home/ubuntu/桌面/eclipse.desktop
