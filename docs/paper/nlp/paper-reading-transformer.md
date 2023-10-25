---
title: Transformer
date: 2023-10-02
author: Shuaike Shen
tags: ['Paper reading','Transformer']
math: true
categories: 
- Paper reading
- Natural Language Processing
---

2017年在"Attention is all you need"论文中提出，虽然题目说的是“只需要注意力机制就可以了”；但是在很多的后续实验验证都证明了注意力机制在Transformer这个架构中不是单个就能起作用的，其中的MLP、Residual connection一系列其他的操作也是必不可少的。

文章提出了一种“简单”的架构，没有使用recurrence（循环）和卷积操作能够在序列数据上表现的非常好，Transformer最开始是应用在机器翻译任务上的。

- RNN&encoder-decoder架构，RNN也是使用了这样的架构：
  - RNN采用的基于隐变量的序列模型，这样的时序性太强，并行度非常的差——想要计算$h_t$的话必须把先$h_{t-1}$计算出来，而且由于两个输出时间需要的path太长，后面的输出很有可能会“忘记”了之前的信息。
  - attention机制可以一起吃进全部的序列，可以有很好的并行度，所以Transformer这个模型的并行度非常高。
  - RNN的decoder是一个autoregression自回归模型，每次的输出会作为下一次的输入。
- 之前有过网络模型使用卷积操作替代“recurrent”来提取序列数据中的信息，但是由于卷积核的大小是有限制的，无法提取两个较远的向量之间的关系和序列信息。但是卷积的优点是可以有多个channel通道，可以实现提取多种模式的能力。而attention的优点在于可以看到所有的向量，提取全部的相互关系和信息，并且引入多头注意力机制（类比于卷积中的channel）提取不同的模式的特征。

<center>
  - <img src="/sreenshortcut/Screenshot 2023-08-18 at 19.47.16.png" alt="img" style="zoom:30%;" />
</center>

- Transformer架构：
  - Input Embedding&Output Embedding:
    - 输入的是一个句子，首先要把这个句子tokenize打散成一个个的token，每个token会编码成一个向量（向量的长度是一个超参数）；Output Embedding的过程和上述过程相反。
  - Encoder:
    - Nx代表块encoder&decoder重复了多少次，也是一个可调的超参数。
    - 首先编码器和解码器中都使用了残差连接（为了方便模型保证输入和输出是一样形状的，把维度都设置为 $$d_{model}=512$$，使得可以进行残差连接）；
    - Layer Normalization：对于二维的矩阵的列是feature、行是batch。bacthnorm（一个feature包含所有的batch）是把矩阵的按照列变化为均值为0方差为1；layernorm（一个batch包含所有的feature）是对行做同样的事情。对于序列模型是一个三维矩阵，多一个seq(n)的维度。做norm就是按照层来做，每次做normalization都是取一层来做归一化。
    - Attention：注意力机制，函数的输入会有 $$Q,K,V$$分别代表query、key、value；最后的输出就是value的加权和，所以output的维度是和value是一样的。query和key会计算相似度，相似度越高的key对应的value附近的权重就会更大。
    - Attention function：
      - <img src="/sreenshortcut/Screenshot 2023-08-18 at 19.47.22.png" alt="img" style="zoom:60%;" />

      -    $$Attention(Q, K, V ) = softmax(\frac{QK^T}{\sqrt{d_k}} )V$$

      -    可以使用上述矩阵乘法来实现注意力分数的计算，矩阵乘法非常好并行计算。  $$QK^T$$是在计算dot product，做一个softmax回归之后把计算出的相关性都投射到0～1的范围内。最后乘以矩阵 $$V$$得到最后的输出。最后除 $$d_k$$的原因是：dk比较大的时候相似度的差距会比较大，会更向两端靠拢。这样导致梯度会小，训练的时候会训练不动，所以除以一个值是不错的选择。

      -    一般有两种注意力函数：一种是加法的、一种是乘法的；乘法的和上面提到的基本没有区别，只是没有在最后除以 $$d_k$$。
    - Multi-head：上面的注意力函数实际上没有可以学习的参数，先使用一个linear层投影到较低的维度h（head头的数量）次（投影的权重是可以学习的，如果直接用dot product没有什么可以学的东西），做h次注意力函数。把每个函数的输出并在一起再投影回来得到输出。这里有点像卷积的输出有不同的通道，可以学习到不同的模式。 $$MultiHead(Q, K, V )=Concat(head_1, ..., head_h)W^O$$ Where $$head_i = Attention(QW^Q_i , KW^K_i ,VW^V _i)$$看着是很多小矩阵的计算，实际上是可以使用是一个大矩阵来计算的——有很高的并行度。
    - self-attention：自注意力机制，可以看到在encoder中attention块的输入 $$K、Q、V$$是一样的，。三者都是n个长为d的向量，Q和K算相似度，一定自己和自己计算相似度是最大的。
    - Position-wise Feed-Forward：其实本质上就是一个MLP，使用相同的mlp对每一个向量都作用一次，这就是Position-wise（MLP是共享的，权重是一样的）。RNN和Transformer都是使用一个MLP来进行到语义空间的映射，二者的区别在于如何提取历史序列信息。RNN是一个一个采集的，Transformer是采集的全局的序列信息。
  - Decoder:
    - 其实和Encoder中的模块内容几乎是一样的，唯一有区别的地方在于mask，因为在实际中是不知道自己之后的信息的，所以为了使得在训练和测试中decoder的行为是一致的，需要mask掉。Query和Key是等长（因为注意力机制的关键就是计算K、Q之间的相似性得到权重）的，在t时刻：k1~kt、q1～qt，但是注意力机制的时候会和所有做计算，加了mask是在计算value的加权和的时候把后面都弄成0.
    - K、V来自于encoder的输出，KQV都是n个长为d的向量，n是句子的长度，d是下面设置的dk。输出是value的加权和。
  - Positional Encoding：
    - 区别时序信息和序列信息，attention中是没有任何的时序信息的，计算的是value的加权和。序列信息对于这句话中每个词在哪一个位置是不关心的，把这句话的所有单词打乱输出结果不会变的。所以需要引入“位置编码”。RNN通过的是上一个时刻的输出作为下一个时刻的输入来传递的时序信息。attention在输入中加入时序信息。
- Byte-pair encoding：把单词的词根提取出来，减少字典的大小
- Residual Dropout：在子层的输出上进行dropout
- Label Smoothing：softmax的输出很难逼近于1，这里是说置信度为0.1的时候就可以认为是想要要的那个词了。这会损失perplexity（困惑度）。

对比的计算复杂度：

<center>
<img src="/sreenshortcut/Screenshot 2023-08-18 at 19.47.32.png">
</center>

复杂度越低越好，顺序计算越小越好，路径（从一个点的信息跳到另一个点要多少步）越短越好。n是句子的长度（token的数量）。RNN想要的路径太长，信息走着走着可能会走丢了。attention一次会看到全局的信息，信息丢失的概率比较小。

<center>
<img src="/sreenshortcut/Screenshot 2023-08-18 at 19.47.44.png">
</center>

位置编码的具体计算公式，一个token在embedding层表示为一个长为512的向量，也用一个长为512的向量来表示其在句子中的位置——使用周期不一样的sin和cos函数算出来的。

- 超参数列表：

N是模型的块要堆多少层、d_model是模型的宽度（每一个token进来表示成多大的一个向量）、d_ff表示MLP隐藏层输入的大小、h是注意力头的个数、d_k是一个头里面key和value的长度，P是dropout的概率、最后一个是label  smoothing时label的置信

<center>
<img src="/sreenshortcut/Screenshot 2023-08-18 at 19.47.58.png">
</center>