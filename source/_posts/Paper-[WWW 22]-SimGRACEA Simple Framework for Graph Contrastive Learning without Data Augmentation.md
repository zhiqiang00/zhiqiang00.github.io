---
title: Paper SimGRACE A Simple Framework for Graph Contrastive Learning without Data Augmentation(WWW22)
tags: [对比学习]
categories: [论文阅读]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/b36a188093daf2e64a217a84bf183201-nKO_1QyFh9o-2edcfd.jpg
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg
---

## SimGRACE: A Simple Framework for Graph Contrastive Learning without Data Augmentation(WWW22)

## 摘要

图对比学习已经成为图表示学习的主要技术，它最大限度地提高了具有相同语义的成对图增强之间的互信息。不幸的是，考虑到图数据的多样性，在增强过程中很难很好地保持语义。

 **动机：** GCL中旨在保持语义的数据增强大致分为三种存在缺点的方式：**首先**，每个数据集都可以通过试错来手动选择增强。**其次，**可以通过繁琐的搜索来选择如何增强。**第三，**可以通过引入昂贵的特定领域知识作为指导来获得扩充。**所有这些都限制了现有GCL方法的效率和更普遍的适用性。**

**工作：** 提出了SimGRACE这个模型，一个简化的对比学习框架，不需要数据增强。具体来说，该工作以原始图为输入，GNN模型及其扰动版本作为两个编码器，**将编码器扰动作为噪声，对相同的“正对”输入可以得到两种不同的嵌入**，得到两个相关视图进行对比。

SimGRACE的灵感来自于这样一种观察，即在编码器扰动期间，图数据可以很好地保存其语义，而不需要手动试错、繁琐的搜索或昂贵的领域知识来进行增强选择。

我们设计了对抗训练方案AT-SimGRACE，以增强图对比学习的鲁棒性，并从理论上解释了原因。

虽然简单，但我们表明SimGRACE在通用性、可转移性和鲁棒性方面可以产生具有竞争力或更好的性能，同时享受前所未有的灵活性和效率

## 1 介绍

图神经网络(GNNs)继承了神经网络的力量，同时利用了图数据的结构信息，在各种基于图的任务，如节点、图分类或图生成等方面取得了压倒性的成就。

### **挑战：**

#### 挑战一

现有的大多数gnn是在监督的方式下训练的，收集丰富的标记数据往往是资源和时间密集型。

**目前的解决方案和缺点：**提出了图自监督学习。其中，图对比学习遵循计算机视觉领域对比学习框架，对每个图进行两个增强，然后最大化两个增强视图之间的互信息。**通过这种方式，模型可以学习对扰动不变的表示。** 

1. 不是所有的场景都合适，因为不同数据之间的结构信息和语义信息相差很大。2. 即使微弱的扰动，也可能完全改变图的语义。

针对上面的缺点，GraphCL模型通过反复试验，手动选择每个数据集的数据增强，这极大地限制框架的泛化性和实用性。针对GraphCL模型的缺点，**Graph Contrastive Learning Automate** 这篇论文提出建议GraphCL自动选择增强对。然而，它在搜索合适的增广时要承受更多的计算开销，并且仍然依赖人类的先验知识来构建和配置增广池进行选择。上面这两个模型都存在因为数据增强而改变图语义的问题。MoCL[40]提出用具有相似性质的生物同分异构取代分子图中的有效亚结构。其实就是他找到了另一个相同意义的图结构，类比原来的结构，这样既不改变图语义，又实现了数据增强，但是这需要专业的领域知识进行指导，而且不能用于其他的领域，比如我们的社交网络。

**综上，这个论文的本质动机是：我们能把图对比学习从繁琐的手工试错、繁琐的搜索或昂贵的领域知识中解放出来吗?**  不再是怎么改变增强策略了，而是尝试不用增强了。

具体来说，作者以原始图数据为输入，GNN模型及其扰动版本为两个编码器，得到两个相关视图。然后，我们最大化这两个观点的一致性。将编码器扰动作为噪声，对相同的“正对”输入可以得到两种不同的嵌入。类似于之前的模型，我们将同一批的其他图数据作为“负对”。

这里为什么提出这个把编码器进行扰动来代替以前的数据增强呢，看一下如下图：

![image-20220426222511330](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/26/08b82ff39db5f547fb5695430d27abb3-image-20220426222511330-6ccab4.png)

两个类别的样本由颜色区分(蓝色和橙色)。首先，分别用这些方法训练三个GNN编码器，并利用t-SNE可视化原始图的表示。然后，以各自的方式对图或编码器进行扰动(对GraphCL进行边扰动，对MoCL用性质相似的生物同线取代功能基团，对SimGRACE进行编码器扰动)，并在下一行显示扰动(GraphCL,  MoCL)或原始(SimGRACE)图的表示。与GraphCL不同，SimGRACE和MoCL可以在扰动之后很好地保留类标识语义。然而，MoCL需要昂贵的领域知识作为指导。

#### 挑战二

**缺点：**GraphCL[54]表明gnn可以通过他们提出的框架获得健壮性。然而，(1)它们不能解释为什么GraphCL可以增强鲁棒性;(2)  GraphCL似乎对随机攻击免疫良好，但对对抗性攻击的表现不令人满意。GROC[19]首先将对抗变换集成到图对比学习框架中，提高了对对抗攻击的鲁棒性。

问题是，GROC的鲁棒性是以更长的训练时间为代价的，因为对每个图进行对抗性转换是耗时的。

针对上述问题，作者提出了一种新的算法AT-SimGRACE，以一种对抗的方式干扰编码器，引入较少的计算开销，同时具有更好的鲁棒性。

##  2 相关工作

### 2.1 图上的生成/预测自我监督学习

### 2.2 图对比学习

对比学习一般分为两类，**一类是通过对比局部和全局表示来编码有用的信息:**首先提出了DGI和InfoGraph，通过最大化不同粒度的图级表示和子结构级表示之间的互信息来获得图或节点的表示。最近，MVGRL提出通过执行节点扩散和将节点表示与增强图表示进行对比来学习节点级和图级表示。**另一类是被设计用来学习容忍数据打扰的表示：** 具体来说，将增强的图数据输入到共享的编码器和projection head 中，然后最大化它和原图的互信息。通常，对于节点级任务，GCA认为数据增广方案应保留图的内在结构和属性，因此提出采用自适应增广方案，只扰动不重要的组件。对于图级任务，GraphCL为一般图提出了四种类型的扩展，并演示了学习到的表示可以帮助下游任务。

## 3 方法

### 3.1 SimGRACE

![image-20220427145627554](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/1b7c47d9b75d2f62142ef0b63c955d2e-image-20220427145627554-eabc0e.png)

主要分为三个部分：

**(1) 编码器扰乱**

定义一个GNN 编码器 $f(\cdot; \theta)$ 和它的被扰动的编码器 $f(\cdot; \theta')$ ，然后对同一个图进行嵌入表示：

![image-20220427121312463](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/3d8d31c1b9695f9ad1c08e848ff996fc-image-20220427121312463-b13c95.png)

这里通过下面的方法进行编码器扰动：

![image-20220427121846437](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/1ccbd0d042b97b13aadc4f5daa44c7c1-image-20220427121846437-c6a8b4.png)

其中，$\theta$ 和 $\theta'$ 是GNN 编码器的第 $l$-th权重，每一层的权重是分开扰动的。$\eta$ 是缩放扰动大小的系数。$\Delta \theta_l$ 为从均值和方差均为零的高斯分布中采样的扰动项。

**(2) Projection head**

一个名为投影头的非线性转换 $g(·)$ 将表示映射到另一个潜在空间可以提高性能。作者采用两层MLP去进行映射：

![image-20220427124343545](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/ab6e379004e3eabc39d4c95ecf323d46-image-20220427124343545-94aaab.png)

**(3) Contrastive loss**

该工作采用归一化的温度尺度交叉熵损失(normalized temperature-scaled cross entropy loss， NT-Xent)，去提高正样本对 $z$ 和 $z'$ 的负样本。

在训练过程中，一个minibatch中随机采样 $N$ 个图，然后分别输入到原始编码器和扰动后的编码器中，分别得到 $z$ 和 $z'$ ，最后会得到 $2N$ 个图表示。我们把 $z$ 和 $z'$ 标记为  $z_n$ 和 $z_n'$ 为一个minibatch中的第 $n$-th 个图，负采样对是在同一个minibatch中的其他 $ N- 1$ 个扰动表示产生的。表示余弦相似函数为sim $(z,z')=z^\top z' / \| z \| \| z'\| $ ，第 $n$-th个图的损失被定义如下：

![image-20220427135010583](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/4b7fbfc023853930e5a2df97c1efe449-image-20220427135010583-987802.png)

其中 $\tau$ 为温度参数。最后的损失是在小批中的所有正对上计算的。这个损失第n个图的所有扰动嵌入表示和第n个图的非扰动嵌入表示的余弦相似度的exp作为分子，第n个图的非扰动嵌入表示和同一个minibatch中除了这个图本身的其他的图的扰动嵌入的余弦相似度的exp的和作为分母。最后取负log。因为我们期待分母更小，分子更大，那么加负log，导致期待整个loss变小，符合最小损失的期望，

### 3.2 Why can SimGRACE work well?

首先介绍一个分析度量方法。具体的，确定对比学习相关的两个关键属性：对齐(alignment)和一致性(uniformity)，然后提出两个指标来衡量通过对比学习获得的表示的质量。

- 对齐(alignment)：直接用正对之间的期望距离定义

  ![image-20220427140704374](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/0409419c7f5eab3b98c1f73dbcae8621-image-20220427140704374-78a3a4.png)

  其中，$p_{pos}$ 是正样本分布，也就是同一个样本的数据增强。这个度量与对比学习的目标是一致的:正样本应该在嵌入空间中保持接近。类似地，对于我们的SimGRACE框架，我们提供了一个修改的对齐度量：

  ![image-20220427141225986](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/a23ce4031803df619675d7989de7b7c5-image-20220427141225986-a59be3.png)

  其中，在作者的实验中 $\alpha = 2$ 。

- 一致性(uniformity)：定义为平均两两高斯势的对数

  ![image-20220427141604114](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/7371f623722c798e0a5e343fbd208e91-image-20220427141604114-26c1e4.png)

  其中，在作者的实验中 $t= 2$ 。一致性度量也与对比学习的目标一致，即随机样本的嵌入应该分散在超球上。

  每两个epoch输出一个一致性和对齐指标，可以观察到，这三种方法都可以提高对准性和均匀性。然而，与SimGRACE和MoCL相比，GraphCL在对齐方面获得的增益较小。也就是说，GraphCL不能封闭，因为一般的图数据增强(删除边、删除节点)会破坏原始图数据的语义，从而降低了语义表示。

  GraphCL相比，由于编码器扰动可以很好地保留数据语义，SimGRACE可以在提高均匀性的同时实现更好的对齐。另一方面，虽然MoCL通过引入领域知识作为指导，获得了比SimGRACE更好的对齐，但在一致性上只获得了很小的增益，最终表现不如SimGRACE。

  ![image-20220427142109328](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/f3c8d0729b894a90b4f12ef3d56f6bfa-image-20220427142109328-c62e82.png)

### 3.3 AT-SimGRACE

这个模型用来解决上面提到的挑战二。也就是，GraphCL表明 GNN 可以使用他们提出的框架获得鲁棒性。但是，他们没有解释为什么 GraphCL 可以增强鲁棒性。此外，GraphCL 似乎对随机攻击具有很好的免疫力，但对对抗性攻击却不尽如人意。

这部分期望用对抗训练Adversarial Training (AT) 以有原则的方式提高 SimGRACE 的对抗鲁棒性。

一般的，AT 直接将对抗样本结合到训练过程中来解决以下优化问题：

![image-20220427144804062](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/05171886d0e26b1ff872f19af731a6b2-image-20220427144804062-167c54.png)

其中 $n$ 是训练样本数量，$x'_i$ 是以自然样本为中心的 $\epsilon$-球(（以 $L_p$ 范数为界）)内的对抗样本，$f$ 是权重为 $\theta$ 的DNN，$ℓ′(·)$ 是标准的监督分类损失（例如，交叉熵损失），$\mathcal{L}'(\theta)$ 被称为“对抗性损失”。

然而，上述AT的通用框架不能直接应用于图对比学习，因为**（1）AT需要标签作为监督，而标签在图对比学习中不可用； (2) 以对抗的方式扰动数据集的每个图将引入大量的计算开销。**

- 为了解决第一个问题，作者将 Eq8 中的监督分类损失替换为 Eq4 中的对比损失。

- 为了解决第二个问题，作者不是对图数据进行对抗性转换，而是以对抗性方式扰动编码器，这在计算上更有效。

  假设 $\Theta$ 是 GNN 的权重空间，对于任何 $w$ 和任何正 $\epsilon$，我们可以定义在参数 $\theta$ 中以 w 为中心半径为 $\epsilon$ 的范数球(norm ball)：

![image-20220427150808115](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/5480c7021481d93d3e1ff85fe4ea974a-image-20220427150808115-bdbc2a.png)

作者选择 $L_2$ 范式来定义我们实验中的规范球。基于这个定义，可以将 AT-SimGRACE 转为如下优化问题：

![image-20220427151220120](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/ba391ae4a3fe52b7bb8b1410250784a5-image-20220427151220120-b04bde.png)

其中 $M$ 是数据集中图的数量。作者提出算法 1 来解决这个优化问题。具体来说，对于内部最大化，我们向前推进 $I$ 步骤以使用梯度上升算法在增加对比损失的方向上更新  $\Delta$。随着内部最大化的输出扰动 $\Delta$ ，外部循环使用 mini-batch SGD 更新 GNN 的权重 $\theta$。

### 3.4 Theoretical Justification

在本节中，我们旨在解释 AT-SimGRACE 可以增强图对比学习的鲁棒性的原因。

首先，人们普遍认为平坦的损失格局(flatter loss landscape)可以带来稳健性，就是使损失变低。例如，如公式8所示，对抗训练(AT)通过在模型输入确实受到干扰时限制损失的变化来增强鲁棒性，也就是说，输入是有干扰有变化的，但是希望输出依旧和标签一样，

作者希望从这个角度去分析为什么它的AT-SimGRACE模型有效。

数学推导看不懂了......

## 4 实验

- **RQ1. (Generalizability) Does SimGRACE outperform com-**
  **petitors in unsupervised and semi-supervised settings?**
- **RQ2. (Transferability) Can GNNs pre-trained with Sim-**
  **GRACE show better transferability than competitors?**
- **RQ3. (Robustness) Can AT-SimGRACE perform better than**
  **existing competitors against various adversarial attacks?**
- **RQ4. (Efficiency) How about the efficiency (time and mem-**
  **ory) of SimGRACE? Does it more efficient than competitors?**
- **RQ5. (Hyperparameters Sensitivity) Is the proposed Sim-**
  **GRACE sensitive to hyperparameters like the magnitude of**
  **the perturbation  $\eta$, training epochs and batch size?**

### 4.1 Experimental Setup

#### 4.1.1 数据集

- 对于无监督和半监督学习，我们使用来自基准 TUDataset [27] 的数据集，包括各种社交网络 [2, 52] 和生化分子 [9,  32] 的图形数据。
- 对于迁移学习，我们对 ZINC-2M 和 PPI-306K 进行预训练，并使用 PPI、BBBP、ToxCast 和 SIDER  等各种数据集对模型进行微调。

#### 4.1.2 评估

- 在无监督设置中，我们使用整个数据集训练 SimGRACE 来学习图形表示，并将它们输入到具有 10 倍交叉验证的下游 SVM 分类器中。
- 对于半监督设置，我们使用 SimGRACE 对所有数据预训练 GNN，并使用 $k$ ( $k = \frac{1}{label \ rate}$ ) 折对**没有显式训练/验证/测试拆分的数据集**进行微调和评估。**对于具有训练/验证/测试拆分的数据集**，我们使用训练数据预训练 GNN，对部分训练数据进行微调并评估验证/测试集。

#### 

### 4.2 Unsupervised and semi-supervised learning (RQ1)

![image-20220427163355450](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/0ca76609b047de0c44d691dae8c2ae69-image-20220427163355450-0900c4.png)

针对无监督任务，在相同的实验设置下将分类精度与基线进行比较。每个数据集的前三个准确度或排名以粗体强调。 A.R.表示平均排名。 - 表示结果在已发表的论文中不可用。

![image-20220427163631564](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/b521536af64db6e881397289790fe01b-image-20220427163631564-bbf563.png)

针对半监督任务，如表 4 所示，我们报告了两个标签率分别为 1% 和 10% 的半监督任务。在 1% 的设置中，SimGRACE 大大优于以前的基线或与  SOTA 方法的性能相匹配。对于 10% 的设置，SimGRACE 的性能与包括 GraphCL 和 JOAO(v2) 在内的 SOTA  方法相当，后者的增强是通过昂贵的试错或繁琐的搜索得出的。

### 4.3 Transferability (RQ2)

为了评估预训练方案的可迁移性，我们在之前的工作之后对化学中的分子特性预测和生物学中的蛋白质功能预测进行了迁移学习实验。

具体来说，我们使用不同的数据集对模型进行预训练和微调。对于预训练，学习率在  {0.01, 0.1, 1.0} 和 epoch 数在 {20, 40, 60, 80, 100} 进行调整，其中执行网格搜索。如表 3  所示，没有普遍有益的预训练方案，特别是对于迁移学习中的分布式场景。然而，与其他预训练方案相比，SimGRACE 显示出具有竞争力或更好的可迁移性，尤其是在  PPI 数据集上。

![image-20220427163922449](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/2395c9f17f8c64a331c4af5c9218b3c3-image-20220427163922449-752ffb.png)

### 4.4 Adversarial robustness (RQ3)

为了公平起见，采用 Structure2vec [6] 作为 GNN 编码器，如 [7, 54]。此外，我们对 GNN 编码器进行了 150 个 epoch  的预训练，因为对抗训练的收敛需要更长的时间。我们设置内部学习率 $\zeta$ = 0.001 和扰动球半径 $\epsilon$ = 0.01。如表 5  所示，在三种典型的规避攻击下，与从头开始训练和 GraphCL 相比，AT-SimGRACE 显着提高了 GNN 的鲁棒性。

![image-20220427164233260](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/3eaddd22736ff4893d21bfd6a7389e31-image-20220427164233260-1d3736.png)

### 4.5 Efficiency (Training time and memory cost)(RQ4)

在表 6 中，比较了 SimGRACE 与最先进的方法（包括 GraphCL 和 JOAOv2）在训练时间和内存开销方面的性能。

![image-20220427165414098](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/d97c13d24d50b369a540742974e4136f-image-20220427165414098-f61e59.png)

### 4.6 Hyper-parameters sensitivity analysis (RQ5)

#### 4.6.1 扰动的大小

如图 4 所示，扰动在 SimGRACE 中至关重要。如果我们将扰动的大小设置为零（휂 =  0），则与这四个数据集的其他扰动设置相比，性能通常是最低的。这一观察符合我们的直觉。在没有扰动的情况下，SimGRACE  只是将两个原始样本作为负对进行比较，而正对损失变为零，导致所有图表示均质地相互推开，这是不直观的。相反，适当的扰动通过最大化图与其扰动之间的一致性来强制模型学习不受扰动影响的表示。此外，与之前声称“硬”正对和负对可以提高对比学习的性能的工作  [15, 33]  很好地吻合，我们可以观察到更大的扰动幅度（在适当的范围内）可以带来一致的改进的表现。然而，过大的扰动会导致性能下降，因为图数据的语义没有被保留。

![image-20220427165708541](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/c28fd24f8f4452ac03d0e7d6ba5eb2e0-image-20220427165708541-7b3b61.png)

#### 4.6.2 批量大小和训练时期

图 5 展示了使用各种批量大小和 epoch 训练的 SimGRACE 的性能。通常，较大的批大小或训练 epoch 可以带来更好的性能。原因是更大的batch  size会提供更多的负样本进行对比。类似地，训练时间越长也为每个样本提供了更多新的负样本，因为总数据集的分割随着训练时期的增加而更加多样化。在我们的实验中，为了公平起见，我们遵循其他竞争对手  [53, 54] 的相同设置，通过训练批量大小为 128 和 epoch 数为 20 的 GNN 编码器。

![image-20220427165855254](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/27/11339607d7e798c8b3a9e588a11c5cf0-image-20220427165855254-7e50a9.png)

## 5 总结

(1) 探索编码器扰动是否可以在计算机视觉和自然语言处理等其他领域很好地工作。

 (2) 将预训练的 GNN 应用于更现实的任务，包括社会分析和生物化学。

## 6 我的思考

- 在不同环节做扰动，很有参考价值；
- 度量和对抗损失那部分，我觉得很好地体现出了什么是理论借鉴，方法论的迁移。
- 实验的考虑层面很全面，值得借鉴；
