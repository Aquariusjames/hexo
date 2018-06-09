---
title: '[原]Ubuntu 14.04 64位系统安装cuda8.0+cudnn7.5+opencv+caffe 血泪教程'
tags: [ubuntu, 教程, caffe, cuda, opencv]
categories: [解决方案,ubuntu]
date: 2017-01-14 16:57:09
---

## Ubuntu 14.04 64位系统安装caffe 血泪教程

### 1.安装环境
> 硬件配置：
  笔记本：神舟战神 Z6-i78154S2
  CPU: Intel Core i7-4720HQ
  显卡: Intel 集成显卡 + GTX 960M
  硬盘: SSD + HDD
  软件配置：
  操作系统：Win10 (SSD) + Ubuntu 14.04（内核版本：4.4.0-31-generic ，使用$ uname -r 可以查看）

### 2.安装Ubuntu 14.04 64位操作系统
由于我电脑有两块硬盘，SSD已经安装了Win10，因此在HDD里面划分150G用于安装Ubuntu系统，启动文件还是在SSD中。
1、需要准备的软件：[ubuntu14.04系统镜像](http://www.ubuntu.org.cn/download/desktop)，EasyBCD软件，用于在windows上面安装ubuntu系统引导。
2、进入windows操作系统，安装easyBCD软件，打开磁盘管理器，压缩卷（空间大小不少于8G，用于存放Ubuntu系统），删除卷，使之成为“未分配”状态（黑色）
3、把Ubuntu系统镜像拷贝到C盘根目录下，把ubuntu系统镜像文件中casper目录下 initrd.lz 和 vmlinuz.efi 拷贝出来放到C盘根目录下。
4、运行EasyBCD软件，“添加新条目”==》”NeoGrub"==》“安装”==》“配置”
输入一下内容：
```  bash
title Install Ubuntu
root (hd0,0)
kernel (hd0,0)/vmlinuz.efi boot=casper iso-scan/filename=/ubuntu-14.04.5-desktop-amd64.iso ro quiet splash locale=zh_CN.UTF-8
initrd (hd0,0)/initrd.lz
```
menu.list格式一般有四行，kernel到……UTF-8需要放在一行，且每个参数需要用空格隔开，注意kernel后面的文件名要和下载的文件名一致。

-  title xxx, bootloader进去之后看到的菜单选项名称，必须保留
-  root (hd[n],[m])，root开头,然后一个空格,加一个分区名称(hd[n­],[m­])。表示 iso、vmlinuz.efi和 initrd.lz 的绝对路径，其中n表示iso文件所在的盘符序号，m表示第几个分区，n和m都是从0开始算起
- kernel (hd[n],[m]), 以kernel 开头,然后加一个空格,并在其后给定vmlinuz.efi文件存放路径,这个命令行的作用是告诉计算机将使用(hd[n­1],[m­1])分区下的 linux 目录中的 kernel 内核来启动。ro表示只读。filename后面的iso务必与目标iso文件命名一致。
- initrd (hd[n,hd[m])/initrd.lz ,he kernal 行类似，致命安装的文件放在哪个分区和哪个目录中

**至此，windows上所有的准备工作完成**
5、重启选择NeoGrub引导加载器。进入ubuntu安装界面，桌面有一个“安装” 和“实例”，注意不要急着安装。
6、Ctrl+ALt+t，打开终端，输入一下命令取消isodevice光驱分区，否则会有挂载错误。
``` bash
sudo umount -l /isodevice/
```
执行之后，点击桌面安装图标
7、Ubuntu系统“安装类型”选择“其他选项”，我们进行手动分区

- 首先设置Swap挂载点：大小通常和自己的内存一样或者两倍，如果物理内存大，逻辑分区，也可以不用设置。
- 设置boot分区：不一定需要分出来，看你把引导挂在那个位置，逻辑分区，设置建议大小200～300MB，逻辑分区
- 设置“/”分区，剩下全部大小，逻辑分区
- 设置“安装系统引导的设备”：如果有/boot分区，则选择/boot分区，没有设置的话，可以放在其他位置。
（*提示：如果先设置了主分区，后面就默认全是主分区了，以后重装系统的话需要将基本磁盘转为动态磁盘才可以将硬盘划分为未分配状态，比较麻烦，建议全部设置为逻辑分区*）

8、一路Next，安装成功，重启先进入windows系统，打开EasyBCD==”添加新条目“++》”Linux/BSD"==>"GRUB 2"==>"名称自己定义"==>"驱动器和上一步设置的安装系统引导设备一致"==》“添加条目”。在编辑引导菜单里面可以修改引导顺序或者删除NeoGrub引导。重启进入Ubuntu

参考链接：
http://blog.csdn.net/xlf13872135090/article/details/24093203 http://www.jb51.net/os/windows/298507.html


### 3.安装cuda8.0
 1.更改系统默认源
打开软件更新器=》Ubuntu软件==》下载自==》其他站点==》选择最佳服务器，我选择的是<span style="color:red;background:yellow">mirros.hust.edu.cn</span>，这里说明一下，如果不更改系统默认源，后面安装cuda的依赖很多都找不到。
2.准备安装文件：
cuda8.0.44，从Nvida官网下载，[下载链接](https://developer.nvidia.com/cuda-downloads) ，经过多次尝试，使用runfile (local)模式安装成功可能性要高些。下载cuDNN，[下载链接](https://developer.nvidia.com/rdp/cudnn-download) ，需要注册审核，可以从网上其他地方下载。
3.安装开发依赖包，工具软件
``` bash
sudo apt-get install build-essential
sudo apt-get install vim cmake git
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libboost-all-dev libhdf5-serial-dev libgflags-dev libgoogle-glog-dev liblmdb-dev protobuf-compiler
```
更新gcc到4.9版本，如果是gcc5.0版本，需要进行降级。
``` bash
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get install gcc-4.9
sudo apt-get install g++-4.9

更新软链接
sudo su
cd ../../usr/bin
ln -s /usr/bin/g++-4.9 /usr/bin/g++ -f
ln -s /usr/bin/gcc-4.9 /usr/bin/gcc -f
```
**其他注意事项参加cuda的[官方文档](http://developer.download.nvidia.com/compute/cuda/8.0/secure/prod/docs/sidebar/CUDA_Installation_Guide_Linux.pdf?autho=1484380026_e83f106b188ee507f40a3d687a4f0877&file=CUDA_Installation_Guide_Linux.pdf), 遇到问题首先查看官方文档**
4.检查电脑环境是否具备安装cuda条件

- 检查电脑GPU是否是CUDA-capable
``` bash
终端输入 $ lspci | grep -i nvidia
如果显示自己的NVIDIA GPU版本信息，则表示没问题，可以去CUDA官网查看自己的GPU版本是否在支持的列表中
```
- 检查Linux版本是否支持（Ubuntu14.04没问题)
- 检查自己的系统的内核是否支持,内核可以用$ uname -r 命令查看，官网给出的内核版本是最低内核版本要求

5.安装cuda8.0

(a)禁用nouveau
``` bash
$ lsmod | grep nouveau
如果有输出则表示nouveau正在加载
因此我们需要禁用,方法如下：
在/etc/modprobe.d目录下添加文件blacklist-nouveau.conf
输入以下内容
blacklist nouveau
options nouveau modeset=0
打开终端运行
$ sudo update-initramfs -u
重启后再次运行
$ lsmod | grep nouveau
如果没有输出，则禁用成功
```

(b) 重启电脑，到达登录界面时，进入text mode ,按Ctrl+Alt+F1，登录账户，关闭图形化界面
``` bash
$ sudo service lightdm stop
```

(c) cd到cuda安装文件路径，运行 $ sudo sh cuda_8.0.44_linux.run
根据提示一步步操作，注意不要安装openGL（**如果你的电脑和我一样是双显卡，且主显卡为非NVIDIA的GPU，选择no，否则可以yes**），否则会覆盖系统集成先看的openGL,导致无法进入桌面
**注意：如果没有安装oenpGL，则在后面编译cuda自带的samples会报错，缺少can not find /usr/ld  -lglut，这个是由于没有安装openGL造成的，因此需要手动安装**

(d)安装成功后显示installed，如果安装失败，在/tmp目录下有cuda的安装日志，可以查看日志排错。
> 我第一次安装遇到了 ”缺少 libGLU.so libXmu.so libGL.so 依赖“的问题, 解决方法：
> sudo apt-get install freeglut3-dev build-essential libx11-dev libxmu-dev libxi-dev libgl1-mesa-glx libglu1-mesa libglu1-mesa-dev
> sudo update-initramfs -u
> sudo ldconfig //环境变量立即生效

(e)输入$ sudo service lightdm start 重启图形化界面，如果能够成功登录，cuda安装没问题了

(f) 重启电脑，检查 Device Node Verification
检查路径“/dev"是否存在 nvidia*的多个文件，没有的话，参考[官方文档](http://developer.download.nvidia.com/compute/cuda/8.0/secure/prod/docs/sidebar/CUDA_Installation_Guide_Linux.pdf?autho=1484380026_e83f106b188ee507f40a3d687a4f0877&file=CUDA_Installation_Guide_Linux.pdf)的操作步骤，进行添加

(g) 设置环境变量
``` bash
$ sudo vim /etc/profile
在文件末尾添加以下两行
$ export PATH=/usr/local/cuda-8.0/bin:$PATH
$ export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64
使文件生效
$ source /etc/profile

动态链接库设置
创建文件：
sudo vim /etc/ld.so.conf.d/cuda.conf
写入：/usr/local/cuda/lib64
保存之后使其立即生效：
sudo ldconfig -v
```

(h)安装cuda完毕后的检查工作

- 检查NVIDIA 驱动是否安装成功: $ cat /etc/driver/nvidia/version, 安装成功则输出版本号信息
- 检查cuda Toolkit：$ nvcc -V 会输出CUDA的版本信息
- 尝试编译cuda提供的例子

> cd ~/NVIDIA_CUDA-8.0_Samples
> make all -j4
> cd bin/x86_64/linux/release/
> ./deviceQuery
> 如果显示了设备信息，则cuda安装成功

**由于我之前没有安装openGL，因此报告”/usr/bin/ld: 找不到 -lglut“错误**
解决方案如下:
$ sudo apt-get install freeglut3-dev

至此，CUDA8.0的安装结束

### 4.安装cuDNN

进入到cuDNN的文件目录，执行以下命令
``` bash
tar -zxvf cudnn-7.5-linux-x64-v5.0-ga.tgz
cd cuda
sudo cp lib64/* /usr/local/cuda/lib64/
sudo cp include/cudnn.h /usr/local/cuda/include/

更新软链接：
cd /usr/local/cuda/lib64/
sudo chmod +r libcudnn.so.5.0.5
sudo ln -sf libcudnn.so.5.0.5 libcudnn.so.5
sudo ln -sf libcudnn.so.5 libcudnn.so
sudo ldconfig

```

### 5.安装Intel MKL或者Atlas
MKL需要收费，这里安装Atlas
``` bash
sudo apt-get install libatlas-base-dev
```

### 6.安装OpenCV（不是必须的）
根据官网的安装步骤来进行安装：[官网链接](http://docs.opencv.org/2.4/doc/tutorials/introduction/linux_install/linux_install.html),

安装编译opencv的环境：
sudo apt-get install build-essential make cmake git libgtk2.0-dev pkg-config python python-dev python-numpy libavcodec-dev libavformat-dev libswscale-dev libjpeg-dev libpng-dev libtiff-dev

git下载opencv源码：
``` bash
cd ~
git clone https://github.com/itseez/opencv
cd opencv
git checkout 2.4.13.2  # 你需要安装哪个版本就切换到哪个分支上
sudo mkdir build
cd build
sudo cmake -j4 -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local ..
sudo make -j4
sudo make -j4 install
sudo ldconfig
```

### 7.安装caffe所需要的Python环境
有两种方法：

- **方法一：去Anaconda官网下载安装包**
切换到文件所在目录，执行
``` bash
bash Anaconda-2.3.0-Linux-x86_64.sh
添加Anaconda Library Path
在 /etc/ld.so.conf 中新建anaconda.conf
添加以下路径(实际为你的anaconda安装路径)
/home/username/anaconda/lib
sudo vim /etc/profile
export LD_LIBRARY_PATH="/home/username/anaconda/lib:$LD_LIBRARY_PATH"
source /etc/profile
```

- **方法二：直接手动安装Python的依赖包**
``` bash
sudo apt-get install python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose python-dev python-sklearn python-skimage python-h5py python-protobuf python-leveldb python-networkx  python-gflags cython ipython python-yaml
```

使用方法一或者方法二安装完caffe的Python依赖后，执行git命令，下载caffe
``` bash
cd ~
git clone https://github.com/BVLC/caffe.git
```
进入caffe目录下的python 文件夹，安装requirement里面的包，执行命令
``` bash
sudo su
for req in $(cat requirements.txt); do pip install $req; done
```
安装完成后，退出root权限
exit

### 8.编译caffe
首先需要修改配置文件
``` bash
cd ~/caffe
cp Makefile.config.example Makefile.config
vim Makefile.config
```
需要修改两处：
（1）使用cuDNN
> USE_CUDNN := 1 这里去掉#，取消注释为USE_CUDNN := 1
（2）修改python包目录（**如果使用方法二安装python依赖的需要修改，使用方法一不需要**）
PYTHON_INCLUDE := /usr/include/python2.7 \
　　/usr/lib/python2.7/dist-packages/numpy/core/include
改为
PYTHON_INCLUDE := /usr/include/python2.7 \
　　/usr/local/lib/python2.7/dist-packages/numpy/core/include
因为新安装的python包目录在这里： /usr/local/lib/python2.7/dist-packages/

接下来就到了激动人心的编译时刻了
``` bash
make all -j4
make test
make runtest
make pycaffe
```
这个时候进入caffe下的python目录，试试python wrapper有没有安装好
``` bash
$python
$ import caffe
```
如果不报错，则安装成功
