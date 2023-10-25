---
title: BERT
date: 2023-10-02
author: Shuaike Shen
tags: ['Paper reading','BERT']
categories: 
- Paper reading
- Natural Language Processing
---

- BERT主要有两个重要的创新点:
  - 第一个就是微调，预训练不是BERT独创的，但是BERT让这种方法和使用预训练模型在下游任务上进行微调这种方法“出圈”了。
  - Bidirectional：GPT的训练方法是单词接龙，也就是说模型只能够看到之前的文本信息。BERT的训练方法是“完形填空”，可以看到上下文的信息。这种双向的信息在很多时候都是合法的，比如说在进行选择题或者是问答的时候，也是会从后向前看完整道题才会进行回答；不只是只看上文的信息。
- 两种使用预训练模型的策略：
  - feature-based：代表性的工作是ELMo（是基于RNN的模型）
  - fine-tuning：基于微调的，在大数据集上进行训练，在下游任务上进行微调。
- **模型架构：**

<img src="/sreenshortcut/Screenshot 2023-08-21 at 11.15.45.png" style="zoom:100%;" />

在无标签数据上进行预训练

- BERT只是使用了Transformer的encoder，GPT是使用了decoder；Transformer的训练数据是序列对：Encoder/decoder各有一个输入。但是BERT的输入输出形式不太一样：
  - 在BERT中不管是一个句子还是一个句子对（Q&A）都表示成一个token sequence.
    - ![Screenshot 2023-08-21 at 11.22.56](/sreenshortcut/Screenshot 2023-08-21 at 11.22.56.png)

    - 上面的图很好的展示了在BERT中输入的表示，其中有两种特殊的token：一个是表示句子的类别的CLS token；一个是表示两个句子的间隔的SEP token。 位置编码可以使用相对的位置编码即offset，也可以使用绝对的。 Segment Embedding是表示在哪个句子中的Embedding 最后如果要使用在分类任务上的话，分类结果就是把CLS Embedding过一个FC就可以得到分类结果。在每一个token上都添加了可以学习的Embedding向量——上面图中的所有的Embedding都是通过学习得来的。
  - WordPiece：
    - 切词的办法，如果这个单词的出现概率不高就把这个单词切开看它的字序列，如果自序列的出现概率高的话只保留自序列（提取词根）
  - Mask
    - 把15%切分后的tokenmask掉，但是只这么做会造成在预训练和微调上的mismatching，因为在微调的时候没有MASK token。为了解决这个问题，我们以80%的概率真的把MASK token遮盖掉；10%换成其他任意不相关的token；10%不变。
  - BERT预训练的时候前后两个句子不一定是匹配的，也有可能是随机拿来的——50%是真实前后匹配的句子；50%是随便找的。
- 测试结果
  - <img src="/sreenshortcut/Screenshot 2023-08-21 at 11.45.30.png" style="zoom:100%;" />


  