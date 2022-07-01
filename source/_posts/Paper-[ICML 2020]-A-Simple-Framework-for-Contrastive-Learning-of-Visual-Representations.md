---
title: Paper-SimCLR A Simple Framework for Contrastive Learning of Visual Representations
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

- 数据增强的组合在定义有效的预测任务中起着至关重要的作用；
- 在表示学习和对比损失之间引入非线性变化大大提高了表示学习的质量；
- 对比学习相比于有监督学习，受益于更大的batch sizes和更多的training steps。

通过上面这些发现，作者能够大大的优化以前在ImageNet上进行自监督和半监督的学习方法。

## 1. 介绍

在没有人工监督的情况下的学习有效的视觉表示方法是一个长久的问题，一般分类如下两类：生成式(generative)和判别式(discriminative)。生成方法学习在输入空间中生成或以其他方式建模像素。判别方法使用类似于监督学习的目标函数来学习表示，但训练网络执行网络前置任务（[Pretex task](https://www.zhihu.com/question/358468168)的好处就是简化了原任务的求解，在深度学习里就是避免了人工标记样本，实现无监督的语义提取），其中输入和标签都来自未标记的数据集。许多这样的方法依赖于启发式来设计网络前置任务。

为了了解什么能够实现良好的对比表示学习，作者系统地研究了他们的框架的主要组成部分并表明：

- 多个数据增强操作的组合对于定义产生有效表示的对比预测任务至关重要。同时，无监督的对比学习更受益于更强的数据增强。
- 在表示和对比损失之间引入可学习的非线性变换大大提高了学习表示的质量。
- 具有对比交叉熵损失的表示学习受益于归一化嵌入和适当的调整温度参数([temperature parameter](https://zhuanlan.zhihu.com/p/132785733))
- 对比学习相比于有监督学习，受益于更大的 batch sizes 和更长时间的训练。并且也更受益于更深更宽的网络。

## 2. 方法

### 2.1 The Contrastive Learning Framework

SimCLR 在潜在空间中通过对比损失最大化同一个数据不同增强视图之间的一致性来表示学习。在这个框架下一共包括了四个组件，如图所示：

![image-20220331210653510](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/31/d5400b85d6a71807b9df6206c02a1389-image-20220331210653510-662af6.png)



- 一个随机数据增强模块，他随机转换任何给定数据实例，产生表示同一个示例的两个相关视图，表示为 $x_i$ 和 $x_j$ ，我们将其视为正对(positive pair)。该文章中，作者使用了三个简单的随机这随机增强：随机裁剪，然后将大小调整回原始大小、随机颜色失真和随机高斯模糊。如第 3 节所示，随机裁剪和颜色失真的组合对于获得良好的性能至关重要。
- 使用神经网络基础的编码器 $f(\cdot)$ 从增强数据中提取表示向量。这个框架可以任意选择网络架构，没有限制。该文选择简单，采用常用的 ResNet。 $\boldsymbol{h}_i = f(\widetilde {\boldsymbol{x}}_i)=ResNet(\widetilde {\boldsymbol{x}}_i)$ ，这里的 $\boldsymbol{h}_i \in \mathbb R^d$ 是经过了平均池化层后的输出。
- 使用小型的神经网络projection head $g(\cdot)$ 将表示映射到使用的对比损失空间中。这里使用了具有一层隐藏层的**MLP**去映射：$\boldsymbol z_i = g(\boldsymbol h_i) = W^{(2)} \sigma(W^{(1)} \boldsymbol h_i)$ 。像第四部分说的，相比于 $\boldsymbol h_i$ ，$\boldsymbol z_i$ 能更好的定义对比损失。 
- 定义对比损失函数为一个对比预测任务，给一个包含正样本对的 $(\widetilde x_i, \widetilde x_j)$ 的集合 $\{\widetilde x_k\}$ ，对比预测任务的目的是从集合 $\{ \widetilde x_k \}_{k \ne i}$ 识别给定 $\widetilde x_i$ 的

 $\widetilde x_j$ 。

随机抽取大小为N个的minibatch，并在派生出来的增强样本中定义对比预测任务，得到2N个数据点。作者没有明确的采样负样本。给出一个正样本对的 $(\widetilde x_i, \widetilde x_j)$ ，那么其余的2(N-1)个增强样本看做是负样本。损失函数定义如下：

![image-20220519160235778](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/05/19/9e9556c2ff09335a797261aa6eccd3c7-image-20220519160235778-f5e58c.png)

最终损失是针对所有正对计算的，包括  (i, j) 和 (j, i)

### 2.2. Training with Large Batch Size

为简单起见，我们不使用记忆库训练模型。相反，将训练批量大小 N 从 256 更改为 8192。批量大小为 8192 为我们提供了来自两个增强视图的每个正对的 16382 个负样本。使用具有线性学习率缩放的标准 SGD/Momentum 时，大批量的训练可能会不稳定。为了稳定训练，我们对所有批量大小使用 LARS 优化器。

### 2.3. Evaluation Protocol

## 3. 对比表示学习的数据增强

虽然数据增强已经被广泛的使用在有监督和无监督的任务上，但是它还没有被认为是定义对比学习任务的系统方法。本文通过对目标图像执行简单的随机裁剪（调整大小）来避免这种复杂性，这会创建一系列包含上述两个的预测任务，如图 3 所示。这种简单的设计选择可以方便地将预测任务与其他组件（例如神经网络架构）分离。可以通过扩展增强系列并随机组合它们来定义更广泛的对比预测任务。

![image-20220519173307561](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/05/19/7a423997489f76d0b52aee2fac9b5222-image-20220519173307561-5cc98c.png)



### 3.1 数据增强操作的组合对于学习良好的表示至关重要

为了系统的研究数据增强的重要程度，我们考虑了几种一般的增强方式。**一种类型的增强涉及数据的空间/几何变换**，例如裁剪和调整大小（使用水平翻转）、旋转和剪切。**其他类型的增强涉及外观变换**，例如颜色失真（包括颜色下降、亮度、对比度、饱和度、色调）、高斯模糊和 Sobel 滤波。

由于 ImageNet 图像大小不同，我们总是应用裁剪和调整图像大小，这使得在没有裁剪的情况下很难研究其他增强。为了消除这种混淆，我们考虑了一种用于这种消融的非对称数据转换设置。具体来说，我们总是首先随机裁剪图像并将它们调整为相同的分辨率，然后我们将目标转换仅应用于图 2 中框架的一个分支，而将另一个分支作为标识（即 $t(x_i) = x_i$)。尽管这种设置会影响模型的表现，但不应实质性地改变单个数据增强或其组成的影响。

图 5 显示了单个和组合变换下的线性评估结果。我们观察到，没有单一的转换足以学习良好的表示，即使模型几乎可以完美地识别对比任务中的正对。在组合增强时，对比预测任务变得更加困难，但表示的质量显着提高。

![image-20220519194924541](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/05/19/d5cc57ecdf57596dabbece1842a56e80-image-20220519194924541-1156f2.png)



我们推测，当仅使用随机裁剪作为数据增强时，一个严重的问题是图像中的大多数补丁共享相似的颜色分布。图 6  显示仅颜色直方图就足以区分图像。神经网络可以利用这种捷径来解决预测任务。因此，为了学习可概括的特征，使用颜色失真进行裁剪是至关重要的。

### 3.2.对比学习比监督学习需要更强的数据增强

作者表明，对监督学习没有产生准确性优势的数据增强仍然可以极大地帮助对比学习。

## 4. Architectures for Encoder and Head

### 4.1 更大模型的无监督对比学习好处（更多）

![image-20220519204212557](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/05/19/8c0a001ea5d9ed8aa08db5cd7f0f34d8-image-20220519204212557-a3748d.png)

由图可知，在监督模型和在无监督模型上训练的线性分类器之间的差距随着模型大小的增加而缩小，表明无监督学习比有监督学习更受益于更大的模型。

### 4.2.非线性投影头提高了前一层的表示质量

![image-20220519204907598](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/05/19/063f1d5ca8d1d09977c43ed85727c27d-image-20220519204907598-c653e8.png)

图 8 显示了对头部使用三种不同架构的线性评估结果：（1）恒等映射(不进行映射)； (2) 线性投影，如之前的几种方法所使用的； (3) 带有一个附加隐藏层（和 ReLU 激活）的默认非线性投影。我们观察到非线性投影优于线性投影（+3%），并且比无投影（>10%）好得多。当使用投影头时，无论输出尺寸如何，都可以观察到类似的结果。**此外，即使使用非线性投影，投影头之前的层 h 仍然比之后的层 z = g(h) 好得多（>10%），这表明投影头之前的隐藏层是比之后的层更好的表示。**

值得注意的 $g(\cdot)$ 得到的 $z=g(h)$ （最终用于loss的是 $z$ ）并不是最好的representation，而 $h$ 才是最好的representation。

在representation与contrastive loss间使用可学习的non-linear projection，并证明效果较好。这边使用可学习的网路的优势在于避免计算 similarity 的 loss function在训练时丢掉一些重要的feature。论文中使用非常简单的单层MLP，配上ReLU activation function作为non-linear projection。

## 5. Loss Functions and Batch Size

### 5.1 具有可调温度的归一化交叉熵损失比其他方法效果更好

### 5.2.更大的批量和更长的训练带来的对比学习好处（更多）

- 小epoch大batch_size 效果
- 随着更多的训练步骤/时期，不同批次大小之间的差距会减小或消失，前提是批次是随机重新采样的。
- 与监督学习相比，在对比学习中，更大的批量提供更多的负面例子，促进收敛（即在给定的准确度下采取更少的时期和步骤）。训练时间更长也会提供更多的负面例子，从而改善结果。

## 6. Comparison with State-of-the-art

## 7. Related Work

## 8. Conclusion

在这项工作中，我们提出了一个简单的框架及其用于对比视觉表示学习的实例化。我们仔细研究了它的组成部分，并展示了不同设计选择的效果。通过结合我们的发现，我们大大改进了以前的自我监督、半监督和迁移学习方法。我们的方法与 ImageNet 上的标准监督学习的不同之处仅在于数据增强的选择、在网络末端使用非线性头部以及损失函数。这个简单框架的优势表明，尽管最近兴趣激增，但自我监督学习仍然被低估了。

