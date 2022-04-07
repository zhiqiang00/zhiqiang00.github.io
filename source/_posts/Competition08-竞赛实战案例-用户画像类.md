---
title: Competition08-竞赛实战案例-用户画像类
date: 2022年3月17日
categories: [竞赛]
tags: [特征工程，用户画像]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/e24723f0956f7819c9bf479295b501f7-RB7X0Q5te6s-86a933.jpg
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg
---

## 竞赛实战案例-用户画像类

## 一、数据探索

1. 仔细查看每个数据的基本含义，打印出数据，看看是什么样子的

2. 校验数据的正确性，缺失情

3. 查看数据的分布的时候，除了看训练集和测试集的分布是否相似，还要看target的分布情况（describe）

4. 查看数据的时候 需要看看是否有重复数据  **nunique()**

5. 针对属性信息的字段的离散和连续性，分开后统一分析。注意不是是数值的就是连续的，要看实际情况。

   **1)离散型**（还分为数值型和非数值型）

   - **针对object类，**离散性变量一般要么是两个，要么是含有字典序，因此可按照字典序对object型变量先编码处理(id）
   - 从pd.Serise类型转为numpy.anrray类型 需要在pd类型的数据后面加上values
   - **缺失值处理**，同时为了能够更方便统计，首先做缺失值处理，对于非数值型离散字段可统一用-1进行填充
   - 需用describe()函数判断是否有正无穷
   - 在进行离散特征编码的时候，需要注意将值转为str，否则无法使用sort进行排序
   
   **2）连续型**
   
   - 查看缺失数据和无穷数据
   
   **3）时间类型**
   
   - 关于时间的处理上，可以按照一些字符串操作提取相应的信息，比如直接提取年份、月份、日期和小时点等等
   
   - 还有一种相对万能可适用于各种场景的办法就是使用unix时间戳，可以灵活用于各种转换与计算
   
   - **时间段（上午、下午、晚上、凌晨）** 其实就是获取时间后整除6     //6
   
   - **休息日or工作日** ` datetime.strptime(x.split(" ")[0], "%Y-%m-%d").weekday()`# weekday()返回0-6 分别代表周一到周日    这里整除5， 不是零就是一。 这两的datetime.strptime()就是将字符转为data对象
   
     

## 二、特征工程



### 1. 通用特征

- 在进行特征编码的时候，需要将train和test一起编码
- 删除数据文件之间的冗余数据
  - 可以考虑用`set(new_transaction.columns) & set(merchant.columns)`这种形式删除多余的列
  - 用`.drop_duplicates()`函数删除重复的行，非原地删除

### 2. 业务特征

- 利用pandas工具的groupby进行统计，对机器性能要求比价高。（为了pandas统计需要，不需要再对缺失值以及离散字段进行转化了？？？）
- 利用diff()可以求两次行为之间的间隔时间。
- 记性特征统计的时候需要分裂了统计

  - 连续性数值变量需要统计如下变量：`['nunique', 'mean', 'min', 'max','var','skew', 'sum']` `var`方差    `skew`偏态分布
  - 离散型变量需要统计如下变量：`['nunique']`
  - id类别：`['size', 'count']`
- 合理使用`df.groupby().agg([...]) ` 先进行分组，然后对分组后的部分列进行聚合参考[pandas group分组与agg聚合](https://blog.csdn.net/u012706792/article/details/80892510?spm=1001.2101.3001.6650.5&amp;utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-5.pc_relevant_paycolumn_v3&amp;depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-5.pc_relevant_paycolumn_v3&amp;utm_relevant_index=10)。


### 3. 文本特征

- `CountVectorizer`是属于常见的特征数值计算类，是一个文本特征提取方法。对于每一个训练文本，它只考虑每种词汇在该训练文本中出现的频率。

- TfidfVectorizer可以把原始文本转化为`tf-idf`的特征矩阵，从而为后续的文本相似度计算。

**上述两种方式实际上就是通过类统计词频的方式，没一句话都会由一个长度为单词表大小，每个位置代表单词的频率（CounterVectorizer）或者idf值（TfidfVectorizer）组成。即用向量表示文本。**

> 在读取数据的时候，也要关注内存情况，可以使用del，gc.collect()释放内存。

## 三、模型训练

### 1. 随机森林

#### 1.1 读取数据

- 读取数据后，要去除重复列，拼接特征列。这里拼接特征一般用merge函数，记得用`fillna()`进行确实填充。

#### 1.2  特征选取

- 把id、target这些列去掉，这些不算特征。

- 去除缺失值比较多的特征

- 进行pearson相关性计算，注意这里使用pearson针对的时线性相关，即判断两个变量之间的相关性。因为不论正负相关，其实都是相关的，所以要**取绝对值**。

  - 输出格式为Dataframe类型，如图，所以取`.values[0][1] `，就是想要的相关系数。

    ![image-20220315100347217](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/22/56bcedcb561b261f58c9cddbb608b0a1-image-20220315100347217-235182.png)	

- 取top300的特征进行建模，具体数量可选

#### 1.3 参数调优

- 使用sklearn库的网格搜索(GridSearch)
- 实际上是不同参数、不同取值的排列集合。处理在一定的参数空间内进行搜索，还需要根据调优结果多次手动迭代参数空间。每次迭代都是在上次最佳参数的基础上增加没有搜索过的参数区域。
- 输出的时含参数的最佳分类器。

#### 1.4 训练预测

- 使用k折交叉验证
  - 避免模型对训练数据的过拟合
  - 模型对测试集的预测结果更具有健壮性。
  - 可以生产用于Stacking融合模型的特征，即训练集的交叉预测结果和测试集的模型预测结果。

### 2. LightGBM

这个模型的四个模块和随机森林是一样的，特征选取使用了wrapper方法，参数调优阶段选择hyperopt。

#### 2.1 特征提取

这部分主要是选择出重要的特征参数，使用k折交叉验证，将数据分为几个train和test的形式，然后使用lgb进行train，最后得到的模型有一个`.feature_importance()`属性，然后进行排序就可以了。

这里的输入只是使用了train的数据，没有使用test的。

#### 2.2 参数调优

Hyperopt 是一个sklearn的Python库，他在搜索空间进行串行和并行的优化，搜索空间可以实值、离散值和条件维度，提供了传递参数空间和评估函数的接口。

这里的输入只是用到了train，输出是**最佳的参数字典**。

#### 2.3 需要进行计算的部分

- 需要计算所有test的数据预测的价格是多少，就是k折验证每次累加，最后求平均值。
- 需要计算所有train的数据预测的价格是多少，就是k折验证每次累加，最后求平均值。
- 需要计算单次k折验证的时候，针对train的价格计算mse，用来计算最后的score。**这个得分用于后面的模型融合。**
- 最后保存的submission和第一个其实是一样的，只不过改成了需要的提交格式`[card_id, target]`

### 3. XGboot

#### 3.1 特征提取

相较于前面两个模型，这里使用了nlp特征，并且合并成了是sparse稀疏矩阵。略过特征选取阶段，考虑使用特征全集进行建模。

#### 3.2 参数调优

使用beyesian(贝叶斯)方法，该方法调参通过最大化评估分值进行优化，而评估指标均方根应该是越小越好，所以采用负值的均方根误差作为优化目标。

#### 3.3 需要进行计算的部分

同上。



## 四、高效提分

### 1. 特征优化

#### 1.1 基础统计特征

这部分主要以card_id为key进行聚合统计。

####  1.2 全局card_id

主要包括与用户行为相关时间相关的统计。

#### 1.3 最近两个月的card_id

和上面的类似，主要区别在于时间范围不同。

#### 1.4 补充特征

此部分特征大多数具有也业务意义，比如更好的发现异常值。

### 2. 融合技巧

#### 2.1 单模结果

#### 2.2 加权融合

`Weight_average = (LightGBM + XGBoost + CatBoots) / 3`

#### 2.3 Stacking融合

#### 2.4 Trick 融合

- 方案一：分类模型结果 * （-33.219281） + （1 - 分类结果）* 非异常值得回归模型
- 方案而：方案一的结果 * 0.5 + 全量数据的归回模型













