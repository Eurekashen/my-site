---
title: DALL-E 2
date: 2023-10-02
author: Shuaike Shen
tags: ['Paper reading','DALL-E2']
math: true
categories: 
- Paper reading
- Generative Model
---

DallE2也是基于扩散模型做的图片生成，使用CLIP的image encoder和text encoder来抽取文本和图像的特征。

- 层级式：模型是先生成一张 $64\times 64$的图片，然后通过训练的模型上采样不断的提高分辨率到 $1024\times 1024$

- 生成出来的图片又逼真又多样。

- 模型架构：

  - <img src="/sreenshortcut/Screenshot 2023-08-21 at 16.55.46.png">

  -  图片的上半部分就是一个CLIP模型，下半部分是一个“两阶段”的生成

  - prior：先验模型输入的是text embedding结果网络的变化之后变为image embedding。最巧妙的是这里可以使用CLIP模型对于同样的text embedding生成的image embedding作为ground truth来训练。也使用的是一个diffsuion,但是没有使用U-Net而是一个Transformer的decoder，并且也不是像DDPM一样去预测加的噪声（残差），而是直接去预测原来的图片特征（因为这里是一个序列数据）
  - decoder解码器：把image embedding渲染成图片；decoder中没有使用attention机制
  - 文章说到这样一种两阶段的方式显示的转化为image embedding使得生成效果更加好，但是是不是这样还是有待商榷的，在这之后谷歌的ImageN就没有使用两阶段的方式。在本文中这两个阶段都是使用的diffusion模型。

- Classification guided diffusion：原来GAN生成的图片更加的逼真，因为GAN的目的就是把D骗过去；Classification guided可以牺牲一部分多样性使得生成的图片更加逼真。 具体实现就是在一个数据集上训练一个classifier（一般是加了噪声的ImageNet数据集），在每一步denoise的时候计算一个梯度，使用这个梯度来指导denoise；Classification guided free这个方法是在推理的时候不需要再计算梯度，在训练的时候需要计算一个带分类器的和一个不带分类器的结果，计算这两个结果之间的差别，不断训练更新逼近这两个结果，训练完成之后就不需要再计算了。

- 问题：

  - - 大概是由于CLIP的原因，因为CLIP只是纯纯的去计算图片文本之间的相似程度，并没有理解物体的上下关系。像图中的这个例子就非常明显，只提取出了“红”和“蓝”两种特征，去计算的相似程度。但是模型并不知道上下关系、嵌入关系等。
    - 从下面的这种重建的例子中也可以看出来。
<div style="display: flex; flex-direction: row;">
  <img src="/sreenshortcut/Screenshot 2023-08-21 at 17.29.16.png" alt="Screenshot 2023-08-21 at 17.29.16" style="zoom:20%;" />
  <img src="/sreenshortcut/Screenshot 2023-08-21 at 17.26.15.png" alt="Screenshot 2023-08-21 at 17.26.15" style="zoom:20%;" />
</div>

  - 还有的问题就是在生成一些带文字的图片的时候图片中的文字全是错的，这大概是由于分词文本编码的问题，模型识别的都是词根。还有人发现了很多有趣的现象——模型有一种自己的语言（见讲解视频最后）

    - <img src="/sreenshortcut/Screenshot 2023-08-21 at 17.30.00.png">
- 相关介绍：

  - AE：auot-encoder，自监督训练希望重建原来的x
  - DAE：加了噪声，之后和AE是一样的
  - MAE：加了mask，也是一样的，也说明了图像信息的冗余是非常多的。但是这三种都是得到了一个bottleneck z向量是特征而不是一个分布，所以只能够拿来做分类任务而不能用来采样解决生成任务。
  - VAE：变分自解码器，这就是去预测一个分布，从分布中采样一个z出来
  - VQVAE