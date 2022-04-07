---
title: Paper-A Simple Framework for Contrastive Learning of Visual Representations
date: 2022年3月31日
tags: [对比学习, 无监督]
categories: [论文阅读]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/b36a188093daf2e64a217a84bf183201-nKO_1QyFh9o-2edcfd.jpg
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg
---

## A Simple Framework for Contrastive Learning of Visual Representations(SimCLR)



> 论文方向：图像领域
>
> 论文来源：2020 International Conference on Machine Learning
>
> 论文链接：https://arxiv.org/abs/2002.05709
>
> 论文代码：https://github.com/google-research/simclr

## 摘要

这篇文介绍了SimCLR，用于视觉表示对比学习的简单框架。这里作者化简最近提出的对比自监督学习算法，不再需要专门的架构和存储库。对于为什么对比学习任务能学到有用的表示，作者们系统的研究了自己的框架，给出以下三个说明：

- 数据增强的组合在定义有效任务中有效的预测任务中有着关键作用；
- 在表示学习和对比损失之间引入非线性变化大大提高了表示学习的质量；
- 对比学习相比于有监督学习，受益于更大的batch sizes和更多的training steps。

通过上面这些发现，作者能够大大的优化以前在ImageNet上进行自监督和半监督的学习方法。

## 1. 介绍

在没有人工监督的情况下的学习有效的视觉表示方法是一个长久的问题，一般分类如下两类：生成式(generative)和判别式(discriminative)。生成方法学习在输入空间中生成或以其他方式 建模像素。判别方法使用类似于监督学习的目标函数来学习表示，但训练网络执行网络前置任务（Pretex task的好处就是简化了原任务的求解，在深度学习里就是避免了人工标记样本，实现无监督的语义提取），其中输入和标签都来自未标记的数据集。许多这样的方法依赖于启发式来设计网络前置任务。

为了了解什么能够实现良好的对比表示学习，作者系统地研究了他们的框架的主要组成部分并表明：

- 多多个数据增强操作的组合对于定义产生有效表示的对比预测任务至关重要。同时，无监督的对比学习更受益于更强的数据增强。
- 在表示和对比损失之间引入可学习的非线性变换大大提高了学习表示的质量。
- 具有对比交叉熵损失的表示学习受益于归一化嵌入和适当的调整温度参数([temperature parameter](https://zhuanlan.zhihu.com/p/132785733))
- 对比学习相比于有监督学习，受益于更大的 batch sizes 和更长时间的训练。并且也更受益于更深更宽的网络。

## 2. 方法

### 2.1 The Contrastive Learning Framework

SimCLR 在潜在空间中通过对比损失最大化同一个数据不同增强视图之间的一致性来表示学习。在这个框架下一共包括了四个组件，如图所示：

![image-20220331210653510](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/31/d5400b85d6a71807b9df6206c02a1389-image-20220331210653510-662af6.png)



- 一个随机数据增强模块，他随机转换任何给定数据实例，产生表示同一个示例的两个相关视图，表示为 $x_i$ 和 $x_j$ ，我们将其视为正对(positive pair)。该文章中，作者使用了三个简单的随机这随机增强：随机裁剪，然后将大小调整回原始大小、随机颜色失真和随机高斯模糊。如第 3 节所示，随机裁剪和颜色失真的组合对于获得良好的性能至关重要。
- 使用神经网络基础的编码器 $f(\cdot)$ 从增强数据中提取表示向量。这个框架可以任意选择网络架构，没有限制。该文选择简单，采用常用的 ResNet。 $\boldsymbol{h}_i = f(\widetilde {\boldsymbol{x}}_i)=ResNet(\widetilde {\boldsymbol{x}}_i)$ ，这里的 $\boldsymbol{h}_i \in \mathbb R^d$ 是经过了平均池化层后的输出。
- 使用已将小型的神经网络projection head $g(\cdot)$ 将表示映射到使用的对比损失空间中。这里使用了具有一层隐藏层的**MLP**去映射：$\boldsymbol z_i = g(\boldsymbol h_i) = W^{(2)} \sigma(W^{(1)} \boldsymbol h_i)$ 。像第四部分说的，相比于 $\boldsymbol h_i$ ，$\boldsymbol z_i$ 能更好的定于对比损失。 

