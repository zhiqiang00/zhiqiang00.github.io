katex: true

# Deep Graph Infomax



> 论文方向：图像领域
>
> 论文来源：2019 ICLR
>
> 论文链接：https://arxiv.org/abs/1809.10341
>
> 论文代码：https://github.com/PetarV-/DGI
>
> 阅读时间：2022年3月7日
>

## 摘要

- DGI 是一种基于图数据结构的表示节点嵌入的无监督方法。

- 最大化局部和全局的互信息（通过图卷积得到的）。

- DGI 不依赖于随机游走目标，并且很容易适用于直推式学习和归纳式学习。

  



## 1 介绍

　　基于研究的背景以及问题描述，作者的研究目标是什么？

- 将神经网络的方法推广到图结构，是目前机器学习的主要难题，GCN很好的解决了这个问题。但是最成功的的方法往往需要有监督的学习，由于**大多数数据都是没有标签的，这使有监督学习面临难题**。
- 此外，**通常希望从** ***大规模图*** **中发现新颖或有趣的结构，因此，无监督图学习对于许多重要任务至关重要.**

- 随机游走的方法高度依赖参数的选择
- 目前尚不清楚随机游走目标是否真的提供了任何有用的信号，因为这些编码器已经强制实施了一个归纳偏差，即相邻节点具有相似的表示。



- **工作：在这项工作中，我们提出了一个基于互信息而不是随机游走的无监督图学习的替代目标。**

​       **解决上面两个动机：1) 无标签问题；2) 在大规模图上可以拓展的问题**



- 在概率论和信息论中，两个随机变量的互信息(Mutual Information, MI)是指变量之间相互依赖性的量度。近年来基于互信息的代表性工作是 **MINE**。
- **MINE** 其中提出了一种Deep InfoMax (**DMI**)方法学习高维数据的表示。DMI训练了一个编码模型来最大化高阶全局表示和输入的局部部分互信息。这鼓励编码器携带存在于所有位置的信息类型（因此是全局相关的），例如类标签的情况。(它依赖于训练统计网络作为来自两个随机变量及其联合分布的样本的分类器边际产品)

## 2 相关工作

### 2.1 对学习方法

　　表示的无监督学习的一个重要方法是训练编码器在捕获感兴趣的统计相关性的表示和不捕获感兴趣的统计相关性的表示之间进行对比。例如，对比方法可以使用评分函数，训练编码器以增加“真实”输入（也就是正例）的分数并降低“假”输入的分数。

​		DGI在这方面也具有对比性，因为我们的目标是基于对局部-全局对和负采样对进行分类。



### 2.2 采样策略

　　对比方法的一个关键实现细节是如何绘制正负样本。之前在无监督图表示学习上的工作依赖于局部对比损失(强制近端节点有相似的嵌入)。



### 2.3 预测编码

　　对比预测编码(CPC)是另一种基于互信息最大化的深度表示学习方法。PCP也是对比的，但是使用条件密度的估计(以噪声对比估计的形式)作为评分函数。

　　然而，与DGI不同的是，CPC和上面的图方法都是预测性的:对比目标有效地训练了输入结构指定部分之间的预测器(例如，在相邻节点对之间或节点与其邻域之间)。**DGI的不同之处在于，同时比较一个图的全局/局部部分，其中全局变量是从所有的局部变量计算而来的。**



　　之前唯一侧重于对比图上的“全局”和“局部”表示的作品是通过邻接矩阵上的(自动)编码目标来实现的(Wang et al.， 2016)，并将社区层面的约束纳入节点嵌入中(Wang et al.， 2017)。这两种方法都依赖于矩阵分解式的损耗，因此不能扩展到更大的图。 **矩阵分解损失比较大**。



## 3  DGI 方法

> 作者解决问题的方法/算法是什么？是否基于前人的方法？基于了哪些？

### 3.1 基于图的无监督学习

　　假设了一个通用的基于图的无监督机器学习设置。

　　首先给出图的节点特征， $\mathbf X = \{\vec{x_1},\vec{x_2},...,\vec{x_N},\}, \vec{x_i}\in\mathbb{R}^F$，是节点$i$的属性，$F$是维度，$N$是节点个数。 邻接矩阵A是这些节点的关心信息。假设边没权重，邻接矩阵存储的时 $0$ 或者 $1$。

　　模型的目的是训练一个encoder，将 $\varepsilon:R^{N \times F}  *  R^{N \times N }\rightarrow R^{N \times F'}$，形式化表示为: $\mathbf H = \varepsilon({\mathbf X},{\mathbf A}) = \{\vec{h_1},\vec{h_2},...,\vec{h_N}\} $最终实现用$F'$维度表示每个节点。然后可以检索这些表示，并将其用于下游任务，如节点分类。$\vec{h}_i$表示一个节点的high-level的表示。

　　在这里，重点关注图卷积编码器——一种灵活的节点嵌入体系结构，它通过局部节点邻域的重复聚集生成节点表示。一个关键的结果是产生的节点嵌入$\vec{h}_i$，总结了以节点$i$为中心的图的一个局部信息(一个patch)，而不仅仅是节点本身。在接下来的内容中，我们经常将 $\vec{h}_i$ 作为局部特征(patch representations)表示来强调这点。**也就是说用$\vec{h}_i$表示局部特征。**

### 3.2 局部-全局互信息最大化

　　我们学习编码器的方法依赖于最大化局部互信息——即，DGI寻求获取节点(即局部)表示，该表示捕获整个图的全局信息内容，由一个汇总向量 $\widetilde{s}$ 表示。

　　为了获得图级别的 summary vector $\widetilde{s}$，作者提出了 **readout函数** $R: R^{N×F}→R^F$，将得到的patch表示汇总为图级表示$\widetilde s = R(\varepsilon(\mathbf X,\mathbf A)) $。 **就是将一堆节点的向量映射为一个向量**。

　　作为最大化局部互信息的指标，使用了一个判别器 discriminator: $\mathcal D:\mathbb{R}^F \times \mathbb{R}^F \to \mathbb{R} $， 例如 $\mathcal{D}(\vec h_i, \vec s )$ 代表着patch-summary对，也就是去判断patch和summary的两个向量的概率分数，如果patch在summary里面的话，这个值应该更高。

　　给判别器$\mathcal D$提供的负样本是 $(\mathbf X, \mathbf A)$的全局表示(summary $\vec s$ ) 和另一图 $(\widetilde{\mathbf X}, \widetilde{\mathbf A})$ 的局部特征表示(patch representations)。针对多图设置，$(\widetilde{\mathbf X}, \widetilde{\mathbf A})$，可以是其他元素。但是针对单图，需要一个显示的(随机的)curruption函数 $\mathcal C:\mathbb{R}^{N \times F} \times \mathbb{R}^{N \times N} \to \mathcal C:\mathbb{R}^{M \times F} \times \mathbb{R}^{M \times M}$ 。

　　负抽样程序的选择将控制特定类型的结构信息，这些信息是作为这种最大化的副产品所希望捕获的。

　　对于目标，我们遵循Deep InfoMax (DIM, Hjelm等人，2018)的直觉，并使用带有标准二值交叉熵(BCE)损失的**噪声对比型**目标，==这些样本来自联合(正示例)和    边际(负示例)的乘积。==目标函数：

![image-20220314230556068](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/18/66a244ad9eac643dbb2b7f294045b062-image-20220314230556068-f34c74.png)

　　该方法基于联合和边缘的乘积之间的JensenShannon散度，有效地最大化了 $\vec h_i$ 和$\vec s_i$之间的互信息。

　　生成的局部特征patch representation 被驱动以保持与全局图摘要的互信息，这允许在局部水平上发现和保存相似性，例如具有相似结构的角色的遥远节点。注意，这是一个“颠倒”版本的参数由Hjelm et al提出，针对节点分类，我们的目标是为局部特征（也就是节点的向量表示）与图中类似的局部特征建立联系，而不是让全局信息summary包含所有这些相似之处(然而,这两种效应在原则上应该同时发生)。

### 3.3 THEORETICAL MOTIVATION

　　现在，我们提供了一些直觉，将我们的鉴别器的分类错误与图表示上的互信息最大化联系起来。......

### 3.4 DGI 概述

　　假设单图设置(即提供$(X, A)$作为输入)，我们现在总结`Deep Graph Infomax`过程的步骤:

- 使用腐蚀函数`corruption function`j进行负采样:$(\widetilde{\mathbf X}, \widetilde{\mathbf A}) \sim \mathcal C(\mathbf X,\mathbf A)$
- 获取局部特征`patch representations`，通过将图输入编码器，得到局部特征$\vec h _i$：$\mathbf H = \varepsilon({\mathbf X},{\mathbf A}) = \{\vec{h_1},\vec{h_2},...,\vec{h_N}\} $.
- 将负采样的样本输入编码器，得到局部特征 $\vec{\widetilde h}_i$：$\widetilde {\mathbf H} = \varepsilon(\widetilde{\mathbf X},\widetilde {\mathbf A})$。
- 通过`readout function`函数获取全局表示：$\vec s = \mathcal R(\mathbf H)$。
- 通过梯度下降最大化公式1，从而更新 $\varepsilon, \mathcal R, \mathcal D $ 。

![image-20220315214300091](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/18/d6dcf84932263ac92be3922a01832ffd-image-20220315214300091-1ca706.png)

## 4 实验

在多种节点分类任务上评估了DGI编码器的好处。在每个任务上，DGI都完全使用无监督的方式去学习局部特征(patch representation)，然后去评估这些节点的分类效果。这个过程使用patch representation训练和测试一一个简单的线性(逻辑回归)分类器来完成。

### 4.1 数据集

这篇文章的实验设置和GCN和GraphSAGE一样，进行了以下基准任务：

1）将Cora、Citeseer和Pubmed引文网络的论文按照主题进行分类;

1）用Reddit Posts数据集预测社交网络模型的社区结构;

1）在蛋白质-蛋白质相互作用(PPI)网络中对蛋白质的作用进行分类，需要将其推广到不可见的网络。

![image-20220317102616142](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/18/9c912c4158ebc525cc6445b44ca766ed-image-20220317102616142-026aa6.png)

### 4.2 实验设置

可见上面的5个任务分为直推式、在大图上的归纳式以及在多图上的归纳式三个大类的任务，因此使用了适合该设置的不同的**编码器**和**腐蚀函数**(如下所述)：

> - 归纳学习(Inductive Learning): 先从训练样本中学习到一定的模式，然后利用其对测试样本进行预测，即首先从特殊打一半，再从一般到特殊，常见模型如贝叶斯模型。
>
> - 直推式学习(Transductive Learning)：先观察特定的训练样本，然后对特定测试样本做出预测(从特殊到特殊)。实际指当前学习的知识直接推广到给定的数据上，相当于给了一些测试集的情况下，结合已有的训练数据集，看能不能推广到测试数据上。
>
>   ==总的来说，二者的区别在于我们想要预测的样本，在我们训练的时候是否遇见。

- **直推式**

  这里编码器是一层图卷积网络(GCN)模型，具有以下传播规则：
  $$
  \varepsilon(X, A) = \sigma(\hat D^{-\frac{1}{2}} \hat{A} D^{-\frac{1}{2}} X \Theta)
  $$
  其中， $\hat A = A + I_N$ 代表加上自环的邻接矩阵， $\hat D$ 代表相应的度矩阵，满足 $\hat D_{ii} = \sum_j\hat A _{ij}$，对于非线性激活函数 $\sigma$ 选择PReLU(parametric ReLU)。$\Theta \in R^{F \times F'}$ 是应用于每个节点的科学系线性变换。

  因为我们的腐蚀函数被设计使用主要是想去鼓励正确编码表示图中不同节点的结构相似性，因此输入到腐蚀函数 $\mathcal C$ 的连接矩阵没有变 $\widetilde A = A$ 。腐蚀函数通过改变原始特征特征进行腐蚀扰动获得 $\widetilde X$ 。腐蚀函数输入的图的节点和原始图是保持一致的，但是在图中的位置不同，因此可学习到不同的局部表征。

- **在大图上的归纳式**

  针对归纳式学习，这篇论文再采用GCN作为编码器了（因为学习过的滤波器依赖于一个固定的和已知的邻接矩阵），而是采用了平均池化传播规则，就像GraphSAGE-GCN那样。

$$
MP(X,A) = \hat D ^{-1} \hat A X \Theta
$$

​       参数定义如上。注意乘以 $\hat D^{−1}$ 实际上执行了一个标准化的和(因此是均值池).虽然方程中明确指定了邻接矩阵和度矩阵，==但它们并不需要：相同的归纳行为可以通过节点的邻居的持续关注机制观察到，就像在Const - GAT模型中使用的那样。==

​        对于Reddit，编码器是一个带有跳跃连接的三层平均池化模型：
$$
\widetilde {MP}(X, A) = \sigma (X \Theta' \parallel MP(X,A))  \qquad \varepsilon(X,A)=\widetilde{MP}_3(\widetilde{MP}_2(\widetilde{MP}_1(X,A), A), A)
$$
$\parallel$ 代表着连接，也就是说中心节点和它的邻居节点是分开处理的。

考虑数据集的规模较大，很难完全将他放进GPU，因此，采用Hamilton等人(2017a)的子抽样方法，首先选择一个小批量节点，**然后通过节点邻域的替换抽样，得到以每个节点为中心的子图。**==具体的，分别在第一、二和三层采样上采样10，10，25个邻居，那么这个采样的patch就是`1 + 10 + 100 + 2500 = 2611`个节点。也就是给定一个中心节点，然后第一圈采样10个(用这10个节点得到 $ h_1$ )，然后分别以第一圈得到的节点为中心再采样100个(用这10 * 10个节点得到 $ h_2$)，再以第二圈采样得到的节点为中心采样2500个(用这10 * 10 * 25个节点得到 $ h_3$)，这三个 $h_i$ 代表的时同一个节点==。只进行必要的计算去得到中心节点 $i$ 的表示。在整个训练过程中使用了256个节点的小批量。

![image-20220317130050446](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/18/4f9f47014a67097417d0a84a3973ae2c-image-20220317130050446-eb755e.png)

该图中，$\vec s$ 是通过几个邻居采样的局部特征(patch represention)利用**readout函数**得到 $\vec s$ 。

这里的腐蚀函数与上面的直推式类似，只不过将每次采样的patch作为一个单独的图进行破坏。这很可能导致中心节点的特征被替换为一个抽样邻居的特征（因为会把2611个以i为中心节点采样得到的节点(特征)放在一起，逐行打乱子采样patch中的特征矩阵），从而进一步鼓励负样本的多样性。最后将得到的patch representation 输入到判别器。

- **在多图上的归纳式**

  对于PPI数据集，受之前成功的监督架构的启发(GAT)，我们的编码器是一个具有密集跳跃连接的三层平均池模型。
  $$
  \begin{equation}
  \begin{split}
  H_1 &= \sigma (MP_1(X,A))\\
  H_2 &= \sigma (MP_2(H_1 + X W_{skip},A))\\
  \varepsilon(X,A) &= \sigma (MP_2(H_2 + H1 + X W_{skip},A))
  \end{split}
  \end{equation}
  $$
  其中 $W_{skip}$ 为可学投影矩阵，$MP$ 定义如上所示。利用 $\sigma$ 的PReLU激活，计算出每个 $MP$ 层的$F' = 512$个特征。

  在这种多图设置中，选择使用随机抽样的训练图作为反面例子(也就是说，腐败函数只是从训练集中抽出一个不同的图)。考虑到在这个数据集中有超过40%的节点的特征为全零，发现这种方法是最稳定的。为了进一步扩大负面例子的池子，作者对采样图的输入特征应用了dropout。在将学习到的嵌入信息输入到逻辑回归模型之前，对整其进行标准化是有益的。

- **读出器、鉴别器**

在所有三个实验设置中，我们采用了相同的readout fuction和鉴别器结构。

对于readout function函数，作者使用所有节点特征的简单平均：
$$
\mathcal R(H) = \sigma (\frac{1}{N} \sum_{i=1}^N \vec h_i)
$$
鉴别器通过应用一个简单的双线性评分函数对summary-patch 表征对进行评分：
$$
\mathcal D(\vec h_i, \vec s_i) = \sigma (\vec h_i^T \mathbf W\vec s)
$$
这里的 $\mathbf W$ 是一个可学习的分数矩阵， $\sigma$ 是一个logistic sigmoid nonlinearity，用来将 $(\vec h_i, \vec s_i)$ 是一个正例的分数转为概率。

### 4.3 结果

- 对于直推式任务，我们报告了我们的方法在训练50次后（随后是逻辑回归）在测试节点上的平均分类准确率（含标准偏差），其中DeepWalk、GCN、LP和Planetoid直接使用了GCN原文里面的实验结果，没重新跑。
- 对于归纳式任务，我们报告了测试节点(未见过的)上的micro-averaged F1 scores，这是训练50次后的平均值，并重复使用GraphSAGE已经报告的其他模型的实验结果。具体来说，由于作者的设置是无监督的，所以只与无监督的GraphSAGE方法进行比较。另外作者还提供了两个相关架构的监督结果--FastGCN和Avg.  pooling。
- 结果表明，在所有五个数据集上都取得了强大的性能。DGI方法与有监督损失的GCN模型所报告的结果具有竞争力，甚至超过了它在Cora和Citeseer数据集上的表现。我们认为这些优势源于这样一个事实，即**DGI方法间接地允许每个节点获得整个图的结构属性，而有监督的GCN只限于两层邻域**（由于训练信号的极端稀疏性和相应的过拟合威胁）。
- 虽然DGI能够超越同等的监督编码器架构，但它的性能仍然没有超过目前的有监督直推式最先进的技术。
- DGI方法在Reddit和PPI数据集上成功超越了所有竞争的无监督GraphSAGE方法--**从而验证了基于局部互信息最大化的方法在归纳式节点分类领域的潜力**。

![image-20220317153436271](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/18/518f1220832d13ad6a8b443a94cf3202-image-20220317153436271-283e4a.png)

上图中，以分类精度(在归纳性任务上)或micro-averaged F1 scores(在归纳性任务上)计算的结果摘要。在第一栏中，强调了每种方法在训练期间可用的数据种类**（X：特征，A：邻接矩阵，Y：标签）**。"GCN "对应于以监督方式训练的两层DGI编码器。

![image-20220317155707972](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/18/e5303f67fecefe7ce824101fdd244dc1-image-20220317155707972-66a61a.png)

Cora数据集中的节点的t-SNE嵌入来自原始特征（左）、随机初始化的DGI模型的特征（中）和学习的DGI模型（右）。学会的DGI模型的嵌入的聚类是明确的，Silhouette得分是0.234。

## 5. 总结

我们提出了Deep Graph Infomax（DGI），这是一种在图结构数据上学习无监督表示的新方法。通过利用强大的图卷积架构获得的图的局部互信息最大化，能够获得考虑到图的全球结构特性的节点嵌入。这使得在各种transductive和inductive的分类任务中具有竞争性的性能，有时甚至超过了相关的监督性架构。

## 参考文献

1.  Learning deep representations by mutual information estimation and maximization
2. Semi-supervised classification with graph convolutional networks

2. Inductive representation learning on large graphs

## **参考博客**

> 1.   [论文解读DGI](https://www.cnblogs.com/BlairGrowing/p/15261335.html)
> 2.   [DEEP GRAPH INFOMAX 阅读笔记 ——知乎](https://zhuanlan.zhihu.com/p/58682802)





