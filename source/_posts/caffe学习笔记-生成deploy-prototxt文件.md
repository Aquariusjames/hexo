---
title: "caffe学习笔记-生成deploy.prototxt文件"
tags:
  - caffe
categories:
  - 笔记
  - caffe
date: 2017-02-28 15:33:08
---

### deploy.prototxt文件

``` bash
name: "LeNet"
/*原来训练与测试两层数据层*/
/*layer {
  name: "mnist"
  type: "Data"
  top: "data"
  top: "label"
  include {
    phase: TRAIN
  }
  transform_param {
    scale: 0.00390625
  }
  data_param {
    source: "examples/mnist/mnist_train_lmdb"
    batch_size: 64
    backend: LMDB
  }
}
layer {
  name: "mnist"
  type: "Data"
  top: "data"
  top: "label"
  include {
    phase: TEST
  }
  transform_param {
    scale: 0.00390625
  }
  data_param {
    source: "examples/mnist/mnist_test_lmdb"
    batch_size: 100
    backend: LMDB
  }
}*/

/*被替换成如下*/

layer {
  name: "data"
  type: "Input"
  top: "data"
  input_param { shape: { dim: 1 dim: 1 dim: 28 dim: 28 } }
}

/*卷积层与全连接层中的权值学习率，偏移值学习率，偏移值初始化方式,因为这些值在caffemodel文件中已经提供*/
layer {
  name: "conv1"
  type: "Convolution"
  bottom: "data"
  top: "conv1"
  convolution_param {
    num_output: 20
    kernel_size: 5
    stride: 1
    weight_filler {
      type: "xavier"
    }
  }
}
layer {
  name: "pool1"
  type: "Pooling"
  bottom: "conv1"
  top: "pool1"
  pooling_param {
    pool: MAX
    kernel_size: 2
    stride: 2
  }
}
layer {
  name: "conv2"
  type: "Convolution"
  bottom: "pool1"
  top: "conv2"
  convolution_param {
    num_output: 50
    kernel_size: 5
    stride: 1
    weight_filler {
      type: "xavier"
    }
  }
}
layer {
  name: "pool2"
  type: "Pooling"
  bottom: "conv2"
  top: "pool2"
  pooling_param {
    pool: MAX
    kernel_size: 2
    stride: 2
  }
}
layer {
  name: "ip1"
  type: "InnerProduct"
  bottom: "pool2"
  top: "ip1"
  inner_product_param {
    num_output: 500
    weight_filler {
      type: "xavier"
    }
  }
}
layer {
  name: "relu1"
  type: "ReLU"
  bottom: "ip1"
  top: "ip1"
}
layer {
  name: "ip2"
  type: "InnerProduct"
  bottom: "ip1"
  top: "ip2"
  inner_product_param {
    num_output: 10
    weight_filler {
      type: "xavier"
    }
  }
}

/*删除了原有的测试模块的测试精度层*/

/*输出层的类型由SoftmaxWithLoss变成Softmax，训练是输出时是loss，应用时是prob*/
layer {
  name: "prob"
  type: "Softmax"
  bottom: "ip2"
  top: "prob"
}
```

### 总结
- 去掉前面的训练数据和验证数据层，换成Input类型的层，其中input参数意思如下：

> 第一个：对待识别样本图片进行数据增广的数量，一个图片会变成10个，之后输入到网络进行识别。如果不进行数据增广，可以设置成1。
第二个：图片的通道数，一般灰度图片为单通道，则值为1，如果为非灰度图3通道图片则为3。
第三个：图片的高度，单位像素。
第四个：图片的宽度，单位像素。

- 卷积层与全连接层中的权值学习率，偏移值学习率，偏移值初始化方式,因为这些值在caffemodel文件中已经提供
- 删除了原有的测试模块的测试精度层
- 输出层的类型由SoftmaxWithLoss变成Softmax，训练是输出时是loss，应用时是prob
