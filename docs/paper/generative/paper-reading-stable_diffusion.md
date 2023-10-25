---
title: Latent diffusion model
date: 2023-10-02
author: Shuaike Shen
tags: ['Paper reading','Stable diffusion']
categories: 
- Paper reading
- Generative Model
---

> DDPM最开始的实现是没有使用传统的注意力机制的，就是使用的一个U-Net；在后续的版本中引入了attention机制

- 总结：原来的Diffusion Models都是直接在像素空间进行的操作，这会消耗大量的计算资源，计算复杂度非常的高。stable diffusion是在latent空间上进行的操作，可以在有限的资源上训练出来。stable diffusion使用了交叉注意力机制，可以condition控制diffusion的生成；可以用在补全、生成、超分等多种任务上。
- GAN的结果多样性不如diffusion model，并且会有训练不稳定和模式坍塌（只会生成几个单一图片，没有多样性可言）的问题。DM属于likelihood-based models（似然模型），不会有上面的问题，并且可以通过充分利用共享的参数来拟合复杂的分布，而不需要像自回归AR模型一样有大量的参数
- DMs会倾向于在模型感知不到的细节上消耗太多的计算资源，尽管使用了一些downsampling的措施，但是在RGB的像素空间上评估和计算梯度也是非常消耗计算资源的。
  - 所以想到把模型放到低维度的latent空间上训练
  - 分成两个阶段