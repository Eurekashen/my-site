---
title: GNN
date: 2023-10-02
author: Shuaike Shen
tags: ['Paper reading','GNN']
categories: 
- Paper reading
- Graph
---

博客链接如下：

https://distill.pub/2021/gnn-intro/

把图中的vertex、edge和全局信息都编码为embedding向量

<img src="/sreenshortcut/Screenshot 2023-08-21 at 21.42.38.png">

不同种类的数据都可以表示成为图的形式：图片数据的像素就是图的一个节点、像素之间是相邻的就可以表示为两个节点之间的一条边。 而对于文本数据每一个token就可以看作是一个节点；而相邻的token就可以看作是一条边。

- 三种任务：在图上的任务、节点上的、边上的。
- 在图数据上使用机器学习的挑战：
  - 使用邻接矩阵表示图的话是一个稀疏矩阵，而使用GPU来计算稀疏矩阵的效率非常的低，这一直是一个待解决的问题。
  - 如果使用邻接矩表示，同一个图有多种矩阵的表示形式，交换任意的行、列对于图是没有影响的，在进行机器学习的时候需要考虑这种不变性。
  - 所以使用邻接表的形式来存储图数据，使用V、E、G三个向量表示图的特征信息。
- GNN：
  -  下图就是一个简单的GNN网络

  - <img src="/sreenshortcut/Screenshot 2023-08-21 at 21.51.55.png">

  - GNN block实际上就是一个MLP，注意这里是把V、G、U三个向量分别送入的。最后把节点的向量表示送入一个FC层得到分类结果。（注意不管有多少个顶点只有一个全连接层，所有的顶点共享一个FC的参数）
  - Pooling information汇聚操作
  - <img src="/sreenshortcut/Screenshot 2023-08-21 at 21.57.52.png">

  -  如果没有顶点的向量，可以使用和这个顶点连接的所有边的向量聚合来表示这个顶点的特征。

  - 上面这样的问题在于我们并没有使用图的结构信息——使用信息传递来解决
    - <img src="/sreenshortcut/Screenshot 2023-08-21 at 22.03.01.png">

    -   把本身顶点的向量和他的邻居的向量加在一起送入MLP，其实这个思路有点像在矩阵上面做卷积了——可以传递图中节点比较长距离的特征。
  - 可以在边和顶点之间传递信息：
    - <img src="/sreenshortcut/Screenshot 2023-08-21 at 22.06.02.png">

    -   如果维度不同的话可以使用投影的操作把维度弄成一样的。
  - 全局信息U的作用：抽象的“顶点”，和所有的顶点相连、和所有的边相连。
    - <img src="/sreenshortcut/Screenshot 2023-08-21 at 22.11.13.png">

    -   在最后预测的时候可以把周围的相近向量拿过来一起预测——这有点像attention机制。