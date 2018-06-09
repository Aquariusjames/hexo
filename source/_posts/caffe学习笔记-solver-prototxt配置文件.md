---
title: caffe学习笔记--solver.prototxt配置文件
tags:
  - caffe
  - 深度学习
  - 卷积神经网络
categories:
  - 笔记
  - caffe
date: 2017-02-28 09:07:50
---

## solver配置选项

在Deep Learning中，往往loss function是非凸的，没有解析解，我们需要通过优化方法来求解。solver的主要作用就是交替调用前向（forward)算法和后向（backward)算法来更新参数，从而最小化loss，实际上就是一种迭代的优化算法。

到目前的版本，caffe提供了六种优化算法来求解最优参数，在solver配置文件中，通过设置type类型来选择。

- Stochastic Gradient Descent (type: "SGD"),
- AdaDelta (type: "AdaDelta"),
- Adaptive Gradient (type: "AdaGrad"),
- Adam (type: "Adam"),
- Nesterov’s Accelerated Gradient (type: "Nesterov") and
- RMSprop (type: "RMSProp")

## 参数含义
net: "examples/AAA/train_val.prototxt"   #训练或者测试配置文件
test_iter: 40   #完成一次测试需要的迭代次数
test_interval: 475  #测试间隔
base_lr: 0.01  #基础学习率
lr_policy: "step"  #学习率变化规律
gamma: 0.1  #学习率变化指数
stepsize: 9500  #学习率变化频率
display: 20  #屏幕显示间隔
max_iter: 47500 #最大迭代次数
momentum: 0.9 #动量
weight_decay: 0.0005 #权重衰减
snapshot: 5000 #保存模型间隔
snapshot_prefix: "models/A1/caffenet_train" #保存模型的前缀
solver_mode: GPU #是否使用GPU

1. 训练样本
  总共:121368个
  batch_szie:256
  将所有样本处理完一次（称为一代，即epoch)需要：121368/256=475 次迭代才能完成
  所以这里将test_interval设置为475，即处理完一次所有的训练数据后，才去进行测试。所以这个数要大于等于475.
  如果想训练100代，则最大迭代次数为47500；


2. 测试样本
  同理，如果有1000个测试样本，batch_size为25，那么需要40次才能完整的测试一次。 所以test_iter为40；这个数要大于等于40.

3. 学习率
  学习率变化规律我们设置为随着迭代次数的增加，慢慢变低。总共迭代47500次，我们将变化5次，所以stepsize设置为47500/5=9500，即每迭代9500次，我们就降低一次学习率。
