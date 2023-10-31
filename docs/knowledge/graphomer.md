!!! abstract
    Before the Graphomer, there are lots of works attempt to combine the transformer and GNN, such as GAT and GTN etc. But the effect is not ideal.



## Background

* GNN遵循消息传递的框架，可以抽象为aggregate和combine两个步骤

* Transformer

$$
Q=XW_Q,K=XW_K,V=XW_V \\
A=\frac{QK^T}{\sqrt{d_K}},Attention(X)=softmax(A)V
$$
**$W_Q,W_K,W_V$ is learnable**

## Implementation
Graphormer中transformer主要应用在消息传递中消息的计算，也就是aggregate的部分。与GCN只在邻居计算消息不同，Graphormer将每个节点都看成连通的来计算两两节点之间的Attention Bias。