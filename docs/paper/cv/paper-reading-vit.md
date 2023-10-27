---
title: ViT
date: 2023-10-02
author: Shuaike Shen
tags: ['Paper reading','ViT']
math: true
categories: 
- Paper reading
- Computer Vision
---

## Vision Transformer

Transformer在NLP领域大放异彩，直接将Transformer应用在了图像数据上，打破了CV和NLP在模型上的壁垒。让之后的多模态任务只用一个Transformer解决多种数据成为了可能。

在这之后图片分类数据集上表现最好的几个模型都是基于ViT的；而在目标检测例如COCO数据集上表现最好的几个模型是基于Swin Transformer的（可以理解一个多视角的ViT）。

## 应用的困难之处

Attention机制需要计算注意力矩阵，是两两计算的，所以计算的复杂度是 $O(n^2) $，一般来说 $d_k=512$（GPT、BERT）。但是图片分类则需要 $224\times 224$的图片大，计算量太大。

- 相关的解决的办法
  - 不要把整个图片展开为向量送入模型，把例如ResNet等模型提取出的feature map送入模型
  - 其他的attention机制：比如稀疏注意力、轴注意力、孤立注意力（使用类似卷积核的slide window的形式）——但是这些机制都比较特殊，没有对应的加速办法。
  - ViT所采用的是先把图片打碎成一个个的小的patch（这里选用的是 $16\times 16$的大小），编码之后送入模型就和NLP没有区别了。

## 总体架构

<center>
<img src="/sreenshortcut/Screenshot 2023-08-20 at 21.10.30.png">
</center>

- 把图片拆成一个个的小patch来降低计算量
- Transformer Encoder：
  - 这个部分就是一个标准的Transformer，没有针对CV做任何的改动。
- Linear Projection of Flattened Patches：这里就是把每一块小的图片学习成一个Embedding向量。
- Position Encoding：需要把每个小块在整体的图片中是在什么位置编码到向量中（具体实现的时候是做加法而不是做连接，所以对向量的维度是没有影响的）。这里使用的就是1D的位置编码，和其他编码方式的比较在实验结果模块会继续讲。
- CLS：编号为0的就是用作分类的classfication token。（这个地方可能需要看一下BERT才知道是什么东西）
- 最后使用一个MLP分类头映射到分的类别。
- 需要注意的是，模型的训练还是使用的有监督学习的方式，没有使用自监督学习。在这里回忆一下：
  - GPT的预训练是使用的“单词接龙”的方法，需要预测下一个词
  - BERT的预训练使用的是“完形填空”的方式，需要预测被mask掉的词，两种方法的Gound Truth都是原来的词语，所以是自监督的。
- Hybrid：混合模型，先使用CNN抽取出特征，之后把feature map送入Transformer

## 实验结果

<center>
<img src="/sreenshortcut/Screenshot 2023-08-20 at 21.26.51.png">
</center>

- 左图：
  - 可以看到在小的数据集上的时候ViT的效果不如ResNet，但是在预训练的数据集比较大的时候ViT的优势就发挥出来了。
  - 这可能是由于CNN卷积神经网络有很多的"Inductive bias"归纳性偏差，比如说：
    - 局部性：卷积核会看到相邻的东西，捕捉它们之间的信息，这显然是成立的——离得越近相关性就会越高。
    - 平移不变性：卷积核在不同时间遇到相同的输入输出结果是一样的，这也是显然正确的。
  - 但是Transformer就没有这样的先验假设。
- 右图：
  - 使用5-shot对预训练的模型进行逻辑回归。
  - 在数据量比较大的时候ViT的效果才会更好。

<center>
<img src="/sreenshortcut/Screenshot 2023-08-20 at 21.33.55.png">
</center>


图中非常重要的一个信息是在继续堆数据量的时候，模型的表现还会进一步上涨——没有达到所谓的智能顶点

混合模型在一开始确实集众家之长，在数据集到达一定程度的时候混合模型反而不如纯的Transformer

<center>
<img src="/sreenshortcut/Screenshot 2023-08-20 at 21.40.09.png">
</center>


使用1D的位置编码就足够了，也可以学习到2D空间中的信息，所以使用2D的时候和1D没什么差别。

## 局限性

- 不好迁移到不同大小的图片中（不好finetune），因为图片大小一旦变化了，patch的数量一定会发生变化。