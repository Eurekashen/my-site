---
title: ResNet
date: 2023-10-02
author: Shuaike Shen
tags: ['Paper reading','ResNet']
categories: 
- Paper reading
- Computer Vision
---

赢得了2015年的ImageNet数据集上面的竞赛，直到现在依然应用非常的广泛。

通道数channels可以看作是模式——每一个通道就是学习到的一种模式，通道数越多学习到的模式越多。

- 让训练非常深的神经网络成为了可能，并且在网络变得越来越深的时候不会让结果变差。

引入了残差连接：

<center>
<img src="/sreenshortcut/Screenshot 2023-08-17 at 20.06.36.png">
</center>

上图是一个残差块，把输入和输出通过一个shortcut connection连接到一起。

如果没有这样的连接结构是一个plane的网络的话，当网络变的越来越深的时候结果反而会变差。理论上来说非常深的网络结果不会变差因为最后几层学习到的输出如果是0的话就和浅层是一样的。但是在实际中SGD随机梯度下降没有办法做到，深层网络学习到的东西是错误的。

<img src="/sreenshortcut/Screenshot 2023-08-17 at 20.13.42.png">

当网络层数深的时候就训练不动了

注意收敛不代表模型训练好的，是要收敛到理想的位置

加了残差连接之后能够训练动的原因是梯度保持的好（MuLi），因为如果不加由于求导的链式法则当梯度越来越小很容易会梯度消失。加了之后多了一个浅层网络梯度的加法项，使得梯度可以更新。

- 减少训练非常深的网络的计算复杂度
  - 通过使用1*1的卷积增加了通道降低了图片的大小，使得下面👇两种计算方式的理论复杂度是相似的（但是实际上1*1的实现卷积效率比较低）
  - <img src="/sreenshortcut/Screenshot 2023-08-17 at 20.07.04.png"> 

  -  这样就使得训练深层网络不会有爆炸的计算复杂度
- 不同的ResNet网络模型
  - <img src="/sreenshortcut/Screenshot 2023-08-17 at 20.06.52.png">

