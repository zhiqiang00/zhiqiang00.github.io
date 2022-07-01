---
title: Paper-[IEEE 20]-AtNE-Trust Attributed Trust Network Embedding for Trust Prediction in Online Social Networks
tags: [信任评估]
categories: [论文阅读, IEEE20]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/b36a188093daf2e64a217a84bf183201-nKO_1QyFh9o-2edcfd.jpg
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg
---

## AtNE-Trust: Attributed Trust Network Embedding for Trust Prediction in Online Social Networks

## 摘要

人与人之间的信任关系预测为在线社交网络中的决策、信息传播和产品推广提供了有价值的支持。过学习编码内在网络结构的节点表示，网络嵌入在链路预测方面取得了可喜的性能。

**动机：** 1. 现有方法不能有效捕获具有有向边和具有输入/出链接的节点信任网络的属性信息。2. 信任网络中存在丰富的用户属性，现有方法不能整合这些属性进行预测。

**工作：** 1. 从信任网络结构和用户属性中捕获用户嵌入；2. 设计了一个深度多视图表示学习模块来进一步挖掘和融合用户嵌入表示； 3. 设计一个模块进行用户之间的信任关系； 4. 表示学习和信任评估一起优化，已捕获高质量的用户嵌入并作出准确预测。

## 1 介绍

**挑战：**

(1) 信任属性保持：与正常的没有方向的对称社交网络不同，信任网络的底层结构通常是复杂的。如何同时保留用户的双重角色和连接属性是一个难题； 

 (2)  多视图非线性用户属性：社交网络用户通常与多视图数据相关联，例如评分、评论/评论、评分/评论项目。所有视图组合起来形成社交网络中用户的特征，但表示和融合这些高维和非线性[6]数据是一项具有挑战性的任务；  

(3) 数据稀疏性：实际上，许多信任网络通常非常稀疏，观察到的链接数量有限，这不足以获得信息丰富的用户嵌入。

##  2 参数定义

定义一个有属性的网络

使用用户撰写的评论通过 `bag-of-words` 构建用户属性矩阵 `X` 。构建两个集合，一个是与 $u_i$ 有信任关系的，另一个是随机的。

![image-20220421133930866](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/21/2d0b985761f1b7ebed9618a8b0ca3e91-image-20220421133930866-623c6f.png)

在这项工作中，作者研究了两种计算用户对之间相似度的方法：

- 计算啷个用户之间的共同属性的数量作为共同属性的相似度
- 每个用户节点的余弦相似度

![image-20220421135507354](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/21/962e0c8aa5e9d5833d98969fd7700746-image-20220421135507354-c6a308.png)

计算发现之间信任的用户的用户属性用有更多相似的属性。有利用里统计学方法验证了这个特点。

同理，之间信任的用户的共同属性也会增加，比如都喜欢使用体育用品的人之间信任度会高。

## 3 方法

**AtNE-Trust分为四个部分**

- 信任网络嵌入基于输入层的信任网络结构，包含了用户的双重角色和连接属性；
- 用户属性嵌入包含了来自可用数据的用户属性，包括来自输入层的用户评分、评论和评分/评论项目；
- 表示学习将获得的嵌入馈送到嵌入层，该嵌入层包括一组自动编码器和一个融合层，用于进一步的表示学习；
- 信任关系评估将每对用户的特征连接起来，并通过预测层预测他们的信任关系。

![image-20220421141927763](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/21/e5676c7ca10f51fe8b3165866b89f18c-image-20220421141927763-feeabf.png)

### A. Trust Network Embedding

主要有两步：

**1) 随机游走生成**

信任网络嵌入的第一阶段，从信任网络派生图上的每个种子节点生成多个阶段随机游走。根据与边权重成比例的转移概率，步行的每一步都遵循有向边，直到满足所需的长度。如果随机游走遇到死胡同，剩余的步骤将从种子节点重新开始。对于每个节点，随机游走过程可能会执行 w 次。如果将两个节点放置在生成的随机游走序列中的短距离内，则将它们定义为同时出现的对(也就是生成了正样本对)。然后从步行序列中选择窗口大小 c 内的所有共现对。所有这些同时出现的对都将用于以下似然优化过程。

**2) 似然优化**

在第二阶段，使用带有负采样的神经语言模型  SkipGram 学习向量表示。为了限制每一步更新的参数数量，SGNS 抽样一些用户来更新他们的权重。不会计算其他用户的权重。似然公式函数定义如下：

![image-20220421145810253](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/21/5967e54389752094929b11e70db34dec-image-20220421145810253-e1c150.png)

其中 $D$ 表示包含随机游走过程中生成的所有共现用户的用户对集。$v_j^‘$ 是随机采样的样本点。目标函数的后半部分正则化似然函数中的偏差项。

![image-20220421150544281](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/21/17e0484e03a1379d3ca53e4a7703b8c2-image-20220421150544281-18245d.png)

通过最大化目标函数 (5)，同现用户对的可能性随着内积的增加而增加。这意味着受信任的用户被放置得很近，而没有连接的用户则被放置得很远。**符合同质性理论。**目标函数 (5) 的第二个组成部分是偏置项 $b^{out}_u，b^{in}_u$ 对用户的连接属性进行建模。根据优先依附理论，更大的连通性会导致形成额外链接的可能性更高，因此当偏差项增加时，链接形成的可能性会增加，如等式（5）中计算的那样。

### B. User Attributes Embedding

在线社交网络的用户很多属性，这里面是上面的三个，但是没有用年龄、性别等，因此存在隐私限制和不真实性。

#### Modeling User Rating Behavior:

利用矩阵分解，基本思想是k个未观察到的潜在因素会影响用户的态度和偏好。通过将 useritem-rating 矩阵分解为 user-specific 矩阵和 item-specific 矩阵的内积，可以将用户和项目投影到联合 k  维潜在空间中。用户特定矩阵表示用户对 k个 潜在因素上的项目的偏好，项目特定矩阵表示属于可以吸引用户偏好的项目的 k 个潜在属性。

![image-20220425143204261](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/25/7ff13d54c52a077553483b16af66e7e4-image-20220425143204261-dfc6ab.png)

$P_{m \times k}$ 代表了 $m$ 个uesr和 $k$ 个维度的潜在因素。这个矩阵的每一个行代表了用户从rating 矩阵中学到的用户属性，这里记录为 $x_r$ 。

#### Modeling User Review Behavior:

 针对用户评价的文本内容定义为评价矩阵 $RE$ ，每行代表了一个用户所有的评论。把每个用户的评论利用 **Doc2vec** 转为用户属性。

![image-20220425144412902](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/25/024e7d8065e3c1bf355838e0393a61bc-image-20220425144412902-02804b.png)

#### Modeling Item Properties: 

用户评分和评论的项目自然反映了用户的兴趣，因此项目背后的属性应该被视为用户属性的一部分。

![image-20220425153129391](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/25/9365b953645e3b5b144bf23099765cf4-image-20220425153129391-8a9efe.png)

### C. Representation Learning

通过embedding步骤，已经获得了信任网络节点向量 $x_t$ 和用户属性 $x_r, x_{re}, x_{it}$ 。为了进一步挖掘信息和融合这些信息，使用了一个包含自动编码器的多视图表征学习模型和特征混合单元。

#### 1) Encoder:

分别把上面的四个特征向量输入到编码器中，第  $l$-th层的输入如下：

![image-20220425154158455](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/25/8cc0d086c8369c5d9606eba88cb3ee8b-image-20220425154158455-2dd06e.png)

其中，激活函数是 $ReLU$ 。

#### 2) Feature Fusion Unit:

直接合并所有视图中的所有信息可能会丢失每个视图的独特特征。因此，作者采用特征融合单元来控制数据融合过程。这个单元的输入就是上面Encoder的输出。

- **step 1**

  把用户属性的三个特征映射为一个向量

  ![image-20220425154727413](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/25/9fdce45d6a9395415e58759ccae8d99a-image-20220425154727413-9c9a13.png)

- **step 2**

  然后，计算有多少来自其他视图的数据将与当前视图融合，相当于一个门控机制。step 1 的用户特征融合数据和网咯本身节点嵌入的融合。

  ![image-20220425155151261](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/25/2421fbf21d0482b942b1085139e44851-image-20220425155151261-892381.png)

- **Step 3**

  最后，融合目标视图和其他视图的特征。

  ![image-20220425155322112](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/25/0f3423c8d7602a75c6ba0dc354bda0ee-image-20220425155322112-d7ebda.png)

  这里其实是以 $h_t^{(l)}$ 为例的，也就数说四个中的一个和其他三个进行特征信息融合。

#### 3) Decoder:

通过编码器-解码器，然后计算重构特征和输入特征的损失，然后学习特征。

![image-20220425164003908](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/25/b90f5d84e16d018a96b1a771219a1199-image-20220425164003908-bf4e4c.png)

![image-20220425164013482](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/25/3ed849726bbb5d33462a772dcfb6a9b8-image-20220425164013482-2eb74b.png)

![image-20220425164022363](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/25/e4bffd9ec57d550d039bb45894f1bbaa-image-20220425164022363-2e43cb.png)

### D. Trust Relationship Evaluation

将特征融合单元的得到的输出特征进行拼接。
$$
concat_{u_i} = (y_t \oplus y_r \oplus y_{re} \oplus y_{it})
$$
每对用户的表示进一步连接为单层 MLP 的输入，如下所示：

![image-20220425171205453](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/25/3ce84263629513839adaf108dd415ec5-image-20220425171205453-53549b.png)

其中，$y'_{ij}$ 是属于信任对或者不信任对的预测概率。选择交叉熵作为信任预测损失函数：

![image-20220425171354009](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/25/3b6455f01310edf48509fae23262df62-image-20220425171354009-59cf59.png)

### E. Optimization Objective

AtNE-Trust 模型的最终优化目标是最小化重建损失和信任评估损失的总和：

![image-20220425171439713](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/25/d7036facf0f22386e119d934c3d9e508-image-20220425171439713-c53e60.png)

## 4 实验

![image-20220425172438839](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/25/5fcfc042937d5d5d9decff74952be7c3-image-20220425172438839-b9da12.png)
