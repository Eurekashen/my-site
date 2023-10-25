---
title: MAE
date: 2023-10-02
author: Shuaike Shen
tags: ['Paper reading','MAE']
categories: 
- Paper reading
- Computer Vision
---

文章的题目就说了MAE是一个scalable（可扩展）的视觉学习器。 MAE就是一个BERT的CV版本，BERT是mask掉词语的token、而MAE是把一部分的图片mask掉。所谓的autoencoder中的auto的意思是“自”，而不是“自动”的意思——也就是输入的x和输出的y是一个东西，通过encoder-decoder架构自监督的训练。

- asymmetric encoder-decoder architecture：非对称的编码器-解码器架构；随机的mask掉一部分patch，边卖钱只会看到没有被mask的patch，这样也可以节约计算资源、降低计算开销。和AE、DAE一样，MAE学习的也是一个中间的在latent space的bottleneck表征，而不是一个概率模型，所以不能够用来做生成。模型迁移到下游任务的表现非常好，而且相对来说训练速度比较快（寻来拿ViT-Huge）
- Motivation：
  - 有标号的数据非常的稀缺，训练的时候也容易过拟合
  - 在NLP领域自监督训练训练已经非常的成熟了，像之前说的GPT、BERT。之前也有人想要把BERT迁移到CV领域上来，但是效果并不好——分析为什么不好：首先第一点在过去的CV领域卷积神经网络大行其道，卷积操作不好实现所谓的mask和位置编码（因为卷积的窗口滑动本身是一个连续的过程），但是在ViT出现之后这就不是问题了；信息的密度差异，文本数据的信息密度远低于图像数据，文本只能够mask少部分，但是图片却可以mask掉75%。文本数据还有大量的语义信息（上下文），而图片的信息是非常局部的（从相邻的一些patch就可以补全mask的patch）——为了解决这个问题，把mask的比例设置的非常高；
  - deocder的设计，MAE重建的是原始像素，原始像素的语义层级是比较低的，BERT重建的是单词—语义层级是比较高的。BERT最后只是一个MLP、MAE的decoder可能就需要一个网络。

<img src="/sreenshortcut/image (1).png">

MAE根据非常少的patch也可以重建的大差不差。

- Related：其实MAE可以看作是一个DAE的特殊情况——DAE是给图片加噪声，噪声大到一定程度的时候就相当于把patch mask掉了。相关的工作有iGPT、BEiT等。
- 随机采样mask：采样一部分，把剩下的mask掉。
- MAE encoder&deocder：encoder是一个ViT，但是只把没有mask的patch当作输入。deocder的输入是全部的patch。被mask的token是一个共享的、可学习的向量。都加入了位置编码，decoder则是另一种形式的Transformer block.MAE的decoder仅仅在预训练的时候起作用，迁移到下游任务微调的时候只有encoder起作用。
- 像素的重建：重构最后的像素，输出一个长的向量，最后reshape一下。使用MSE作为损失函数。只在被mask掉的块上面做损失函数，没有被盖掉的因为输入的时候已经看到了像素信息就不做损失函数了。
- Masking Ratio：

<center>
  - <img src="/sreenshortcut/image (2).png">
</center>

  -  可以看到linear probing对于比例是更加敏感的，mask的比例恰当的时候学习到的信息更多
- 一些实验结果：
  -  训练1000个epoch acc也是在变大的，过拟合也不是非常的严重。一般就训练200epoch就会出现过拟合现象

<center>
<img src="/sreenshortcut/image (3).png">
</center>

- 关于微调：下面这张图是在进行不同程度的微调的结果，底部的层的参数和任务的相关度没有非常的高，上面的层和任务的相关度更高，可以多调一调。靠近底部的层相对来说提取的特征是低级特征（较为具体的特征，和具体任务相关性不是非常高的特征）、而靠近输出的层提取的是比较抽象和高级的特征（和具体的任务的相关性会更加的高）。

<center>
<img src="/sreenshortcut/image (4).png">
</center>

- 是重构原始的像素还是重构dVAE的token。两者的差距非常的少，但是直接重构像素的办法更加的直观、更加的方便。所以选择直接重构原始像素。

<center>
<img src="/sreenshortcut/image (5).png">
</center>

- 迁移到下游任务上的结果

<!-- <div style="display: flex; flex-direction: row;"> -->
<center>
<img src="/sreenshortcut/image (6).png" alt="img" style="zoom:85%;" />
<img src="/sreenshortcut/image (7).png" alt="image (7)" style="zoom:95%;" />
</center>
<!-- </div> -->
