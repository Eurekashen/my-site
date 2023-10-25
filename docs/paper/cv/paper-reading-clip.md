---
title: CLIP
date: 2023-10-02
author: Shuaike Shen
tags: ['Paper reading','CLIP']
categories: 
- Paper reading
- Computer Vision
---

模型结合了自然语言处理，使用image-text pairs进行预训练，训练之后的模型在大量的数据集上面进行zero-shot的分类预测都可以获得非常好的效果。

- 训练过程是一个对比学习（只需要正负样本的定义，矩阵的对角线上是正样本其余是负样本）判断是否成对，是自监督的。在4亿个image-text对上进行训练，分类的时候通过计算余弦相似度，相似度最大的就是分类输出。
  - <img src="/sreenshortcut/Screenshot 2023-08-17 at 22.21.43.png">
- 突破了category目录的限制，像之前的模型都是在类似于ImageNet（1000类）这样有明确数量的类别的数据集上训练的，如果出现一个不在给定目录里面的样本就抓瞎了。而CLIP结合NLP的prompt很好解决了这样的问题。不需要明确的softmax分类头。上图中使用一个句子作为prompt是为了避免使用单词的歧异性（在论文中给出了多种prompt engineering的方法）。下面的这张图就能很好的说明CLIP zero shot的能力。
  - <img src="/sreenshortcut/Screenshot 2023-08-17 at 22.42.47.png">
- 模型在最后学习到的不是的像ResNet一样单纯的视觉特征和信息，而是包含NLP中的语义信息的多模态特征。输出是文本信息而不是1/N的label，自由度大了很多。
- image_encoder 使用 ResNet or Vision Transformer；text_encoder 使用 CBOW or Text Transformer。采用的是训练好的模型，即使这样CLIP的训练开销仍然是巨大的。

- CLIP的零样本预测上对于“有明显的一个或者多个具体物体被描述”的数据集的效果非常好；但是对于“没有特定的具体物体描述，抽象的概念或者需要具体知识（例如纹理、肿瘤）”的数据集效果不好。所以在这些数据集上使用few-shot而不是zero-shot是更合理的选择。
- out-of-distribution的问题，CLIP在MNIST数据集上的效果甚至不如线性回归。因为MNIST是一个人工合成的数据集，而CLIP是在自然的图片进行训练的。所以总的来说CLIP和其他DL模型一样脆弱。

<center>
<img src="/sreenshortcut/Screenshot 2023-08-17 at 22.30.02.png" alt="Screenshot 2023-08-17 at 22.30.02" style="zoom:30%;" />
</center>

- 可以非常方便的迁移到目标检测、图像生成、语义分割等任务。
- Fine tune和linear probe的区别：
  - Fine tune是把整个模型都放开，利用新的数据更新整个模型的参数权重，自由度比较高。在论文中作者没有使用微调就是因为自由度太高，作者认为微调不足以说明其zero-shot的能力。
  - Linear probe是把整个模型的参数权重全部冻住，在最后加一层softmax分类头，喂入新的数据只训练这最后一层。在论文中提到的类似的还有logistic regression也是冻住模型的参数在最后加逻辑回归。
- 继续加大数据量CLIP的性能依然会继续提升，但是如果想要通过这样的方法比过在专门的数据集上有监督训练的模型计算开销将是不可接受的。
- 模型在强大的同时也有限制，比如“偏见”问题——爬取的数据集可能是带有宗教、性别等偏见的；对于数据的利用率还是低。