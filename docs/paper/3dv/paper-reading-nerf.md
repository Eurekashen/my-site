---
title: NeRF
date: 2023-10-02
author: Shuaike Shen
tags: ['Paper reading','NeRF']
categories: 
- Paper reading
- 3D Vision
---

## NeRF想要完成的目标：

- Novel View Synthesis，翻译成新视角合成工作，给定已知视角下的图片和图片的内外参数合成新视角下的图片。
- NeRF 想做这样一件事——不需要中间三维重建的过程，仅根据位姿内参和图像，直接合成新视角下的图像。为此 NeRF 引入了辐射场的概念。NeRF使用MLP把3D场景表示为一个可学习的、连续的辐射场。

## Positional Encoding

深度神经网络倾向于学习低频的参数，但是实际场景中的辐射场基本都是高频的。所以作者提出了Positional Encoding。

## Hierarchical volume sampling

使用体渲染积分就会遇到以下几个问题，虽然可以离散的近似计算积分，但是面临的问题就是如何采样。采样点过多开销过大，采样点过少近似误差有太大。

观的一个想法是，最好尽可能的避免在空缺部分以及被遮挡了的部分进行过多的采样，因为这些部分对最好的颜色贡献是很少的，基于这一想法 NeRF 提出分层采样训练的方式

> 使用两个网络同时进行训练 (后称 coarse 和 fine 网络)， coarse 网络输入的点是通过对光线均匀采样得到的，根据 coarse 网络预测的体密度值，对光线的分布进行估计，然后根据估计出的分布进行第二次重要性采样，然后再把所有的采样点一起输入到 fine 网络进行预测。关于 loss 函数我们同样作为一个开放性的内容，不在此多做讲解了。下面是一个 NeRF 的 Demo 以及我自己训的一个母校的 Demo(由于场景太大，拍摄图像有限，左右两边效果不好)。

* 引用链接

  > https://zhuanlan.zhihu.com/p/380015071