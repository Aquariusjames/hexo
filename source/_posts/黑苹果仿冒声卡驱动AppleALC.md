---
title: 黑苹果仿冒声卡驱动AppleALC
tags:
  - 笔记
  - 黑苹果
categories:
  - 笔记
  - mac
date: 2018-07-02 19:58:33
---

参考教程：  
- [黑苹果定制声卡驱动（ALC892为例)](https://www.jianshu.com/p/29a74f0664f1)
- [使用AppleALC声卡仿冒驱动AppleHDA的正确姿势](https://blog.daliansky.net/Use-AppleALC-sound-card-to-drive-the-correct-posture-of-AppleHDA.html)
- [傻瓜式仿冒声卡驱动第二季（仿冒ALC892)](http://bbs.pcbeta.com/viewthread-1685213-1-1.html)
- [[教程] AppleALC使用和修改教程](https://www.haolie.net/thread-664.htm)

资源链接：  
- [vit9696/AppleALC](https://github.com/vit9696/AppleALC)
- [acidanthera/Lilu](https://github.com/acidanthera/Lilu)
- [HDA 工具](https://github.com/daliansky/dell7000/tree/master/hda-tools)
- [zlib 转换器](https://pan.baidu.com/s/1ge40mR5#list/path=%2Fmac%E5%BF%85%E5%A4%87%E8%BD%AF%E4%BB%B6%2F%E5%A3%B0%E5%8D%A1%E5%B7%A5%E5%85%B7%2F%E5%B7%A5%E5%85%B7&parentPath=%2Fmac%E5%BF%85%E5%A4%87%E8%BD%AF%E4%BB%B6%2F%E5%A3%B0%E5%8D%A1%E5%B7%A5%E5%85%B7)


**注意事项：**

该教程可以通过AppleALC修改驱动，然后通过Clover 的方式注入Layout，也可以直接在原生AppleHDA上面修改。

[AppleHDA-10.14.kext.zip](/download/AppleHDA-10.14.kext.zip)

clover注意要设置一个参数：FixHPET,否则无法加载AppleHDA


附：ALC269VC 的confidData的数据格式

```

id_modified:40
01271C40 01271D01 01271EA0 01271F90 
01471C10 01471D01 01471E17 01471F90 01470C02 
01571C20 01571D10 01571E21 01571F00 
01871C30 01871D10 01871E81 01871F00

oneline:
01271C40 01271D01 01271EA0 01271F90 01471C10 01471D01 01471E17 01471F90 01470C02 01571C20 01571D10 01571E21 01571F00 01871C30 01871D10 01871E81 01871F00

```

每组数字构成：
Address+Node+71+[c/d/e/f]+描述数字
描述数字每组一共有8个
分别表示：
1 优先级 
耳机优先级一定要低于内置扬声器，外置麦克风一定要低于内置麦克风
2 lineout 为f，其余0
3 接口颜色
4 接口为 0，表示当接口被检测到时使用。如果是笔记本的话内建的麦克风和扬声器要设成1，即当耳机插入时，内建扬声器静音，耳机0 接口被检测到就是用耳机。
5 接口功能信息
6 链接装置类型
7 接口类型
0为插入接口的，如外置麦克风、耳机等。
(如果codec_dump出来有 [N/A] 的就是无用的port，数字为4。)
9为给笔记本內建，像内置扬声器、内置麦克风等
8 接口位置