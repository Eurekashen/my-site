---
title: AlexNet
date: 2023-10-02
author: Shuaike Shen
tags: ['Paper reading','AlexNet']
categories: 
- Paper reading
- Computer Vision
---

作为深度学习的奠基之作

在ImageNet这个数据集上进行测试拿到了第一名（2012）

使用GPU进行训练，训练这样的深层神经网络在当时是一个开销非常大的任务

<img src="/sreenshortcut/Screenshot 2023-08-17 at 15.42.18.png" style="zoom:100%;" />

图片所示是网络的总体架构，上下两个通路是为了在两个GPU上训练设计的

在当时使用了ReLU激活函数，在当时的条件下ReLU的收敛更快

Overlapping Pooling，一般来说pooling操作是不重叠的，但是在这里pooling操作是有重叠的

- 减少过拟合
  - 数据增强：把图片进行裁剪和旋转、使用PCA对图片的RGB通道做出变化。
  - Dropout：对于全连接层的输出用0.5的概率设为0（本来意图是认为这样相当于训练了很多个不一样的模型，但是后来发现这相当于正则化）
- 测试结果非常好，并且隐藏层的输出向量在语义空间中有非常好的表示，这个向量相似的图片确实是在像素空间中相似的图片。
- 使用一个均值为0、方差是0.01正态分布来初始化每一层的权重。
- 模型可以直接在raw RGB图片上进行操作而不用提前抽取想SIFT特征，推动了之后计算机视觉和深度学习的发展。并且不需要提前使用无标记数据来预热网络。

