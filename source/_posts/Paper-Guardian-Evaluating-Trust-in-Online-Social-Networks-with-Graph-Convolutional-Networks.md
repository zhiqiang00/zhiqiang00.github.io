---
title: Paper-Guardian-Evaluating Trust in Online Social Networks with Graph Convolutional Networks
categories: [论文阅读]
tags: [信任评估, 图卷积网络]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/b36a188093daf2e64a217a84bf183201-nKO_1QyFh9o-2edcfd.jpg
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg# 
---

## Guardian: Evaluating Trust in Online Social Networks with Graph Convolutional

## 摘要

在社交网络中，每个用户通常可以一共一个值来表示他们的直接朋友的可信度。推断在线社交网络中任何一对任何一对节点之间的社交信任值是用意义的。

目前这方面研究的缺点是：

- 依赖专业领域知识的手工规则
- 需要大量的计算资源，这些问题影响了模型的可扩展性。

因为GCNs已经在图数据方面表现很好，所以作者使用了GCNs去学习社会信任中的潜在关系。提出了Guardian模型，这个模型欧旨在结合社交网络结构和信任关系来估计任意两个用户之间的社会信任。

## 介绍

现有的信任评估方法是基于在线社交网络中**社会信任**的**传播性**和**可组合性**设计的。**社会信任的传播性**指信任可以从一个用户传递给另一个用户，从而创建社会信任链，将两个未明确连接用户连接起来。**社会信任的可组合性**是指如果存在多个社会信任链，则需要聚合信任链。其实，还是信息在网络中的传播和聚合。

在线社交网络中，社交信任可以类似地表示为图形数据，包括社交网络结构和用户之间相关的信任关系。

使用图卷积神经网络评估社会信任非常具有挑战性：

- 挑战一：如何共同表示社会联系和相关的信任关系，以便能同时捕捉社会信任的传播性和组合性。
- 社会信任是不对称的，集一位用户可能对其他人的信任程度超过了对她的信任程度。挑战二：如果表征社会信任中的这种不对称属性。

该框架的关键组成是信任卷积层，使用了局部图卷积。每一层被学习的参数都在所有用户之间共享，这使得参数的负责程度和输入的图网络大小无关。为了解决不对称的问题，每个信任卷积层都包含两个部分，**流行度信任传播**和**参与度信任传播**。前者用于了解用户对他人的信任程度，后者用于捕捉用户信任他人的意愿。最后通过全连接层。Guardian 能够以协作的方式明确表示个人用户的流行度信任和参与度信任。因此，可以建立有效的成对信任关系。

## 问题设置

在线社交网络可以被定义为 $\mathcal{G} = (\mathcal {V}, \mathcal \varepsilon, \mathcal W)$ ，这里面的 $\mathcal W$ 可以理解为信任度，不同的数据集新任务设定不一定。所谓的社会信任评估问题就是去评估(预测)之间没有连接的用户之间的信任度。

## Guardian 模型框架

![image-20220410092224021](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/10/a2e0a6a0cfdf6f4db1bd2c52b10c98f0-image-20220410092224021-8b96ab.png)

模型由三部分组成：

- 嵌入层提供初始的嵌入表示
- 多个卷积层，通过引入高阶信息来细调 popularity trust embedding 和 engagement trust embedding，其实就是汇聚更多的信息到初始嵌入表示上。
- 全连接层和softmax层组成最后的预测层。它首先将用户的潜在表示转化为信任的潜在因子，然后输出预测的概率

它首先将用户的潜在表示转化为信任的潜在因子，然后输出预测的概率

### A. Embedding Layer

作者使用了一个预训练嵌入层将每个user映射到 $D_e$ 维中，值得注意的是，这些表示作为用户嵌入的初始状态，以端到端的方式进行优化。最后经过专门设计的转换层，这些被refine的嵌入向量会被转为成对的向量用于信任度预测。

### B. Trust Convolutional Layers

在线社交网络图包含用户之间的社交联系和信任者与受妥者之间的信任交互，作者提出了一个方法能够同时捕获社交联系和相关信任的关系，从而学习用户嵌入 $h[u]$ 。**其实就是不过连接以及权重。**

由于存在不对称性，模型首先将信任交互分为两组： popularity interactions 和 engagement interactions。popularity interactions表明用户被他人信任的程度，而engagement interactions表明用户信任他人的意愿。接下来使用信任卷积层分别聚合信息，表示为 $h_I[u]$ 和 $h_O[u]$ 。这里的聚合器使用的是 `mean-aggregator` 去聚合用户与其邻居相关的信任交互。

- Popularity Trust Propagation (pTr)

  传入的社交连接和相关的信任程度提供了该用户受欢迎信任的直接证据，所以基于此进行popularity trust的传播和聚合。使用one-hot编码表示不同类别的可信度，通过公式1映射为稠密的向量。

  ![image-20220410151703756](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/10/af66c4d23a730a38925e3e66f2d54053-image-20220410151703756-a9ff95.png)

  建模 $u$ 的popularity trust，是需要组合 $v$ 的嵌入表示和他们之间的信任度 $e_{w_{u \gets v}}$ ，如公式2。

  ![image-20220410152146695](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/10/40231af11275a1c81a726732c0b7457d-image-20220410152146695-812407.png)

  其中，$\otimes$ 表示两个向量之间的连接操作， $\left | w \right |$ 表示可信度类型的数量，也就是one-hot的长度。

  取 $\{ pTr_{u \gets u}, \forall v \in N_I(u)\}$ 里面向量的平均值，这种基于均值的聚合器是局部谱卷积的线性近似，如下函数：

  ![image-20220410153319791](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/10/daefb597e7d43208161c9f356998f8d2-image-20220410153319791-a2d263.png)



- Engagement Trust Propagation (eTr)

  这个聚合和上面的同理。

  ![image-20220410153531580](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/10/7f6c76d236caa096158995d4058c91d8-image-20220410153531580-fcda52.png)

- Learning Trust Latent Factors of Users

  为了更好的进行下游的信任度预测，需要量上面两个方面结合起来，作者通过标准的全连接层将 $h_I[u]$ 和 $h_O[u]$ 结合起来，其中， $h_I[u]$ 和 $h_O[u]$ 需要先拼接起来。公式如下：

  ![image-20220410154138536](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/10/e735140dbc8d725f43371937d4e9e3b4-image-20220410154138536-986931.png)

- Higher-order Trust Propagation

  通过堆叠  $l$ 个信任卷积层，用户能够接收从其 $l$-hop 邻居传播的社会信任（流行度信任和参与度信任）。在第 $l$ 步中，用户 $u$ 的表示递归地表述为等式8-12：

  ![image-20220410154343706](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/10/8fee40557cb39c309ba4cc4d4c3a47ad-image-20220410154343706-dc5f89.png)

  ![image-20220410154352106](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/10/eda9909e66fdccdb06e564cb4469a530-image-20220410154352106-b051ed.png)

### C. Prediction Layer

为了学习信任关系的潜在因素，我们首先将信任者和受托者的潜在嵌入连接起来，然后将它们拟合到标准的全连接 (FC) 层，然后是 softmax 层。

![image-20220410154813828](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/10/49e2e6f5432aee518d6f69d46b7f7fc3-image-20220410154813828-b7d650.png)

从用户 u 的角度来看，用户 v 的可信度计算为 $\widetilde{w}_{u \to v} = \mathop{argmax} \limits_j (\widetilde{h}_{u \to v}) $ ，这里应该就是将经过激活函数得到的概率向量取其中最大的，就是我们预测的信任度。

### D. Model Training

为了学习 Guardian 中的模型参数，将目标函数定义为预测值与观察集 $W$ 的真实可信度之间的交叉熵损失：

![image-20220410160828081](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/10/a3682b08bc804598e9049d24d449750e-image-20220410160828081-6ab61b.png)

![image-20220410161212704](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/10/22c2d28c0f68adff9b4ae45a308b2283-image-20220410161212704-179818.png)

### E. Analysis and Discussions

Guardian 是一种归纳学习模型，它能够估计在训练阶段未见过的用户的成对可信度。换句话说，它不需要任何重新训练过程，因为预训练的参数可以用于对看不见的用户进行推理。

