---
title: Competition-SouHu22
categories: [竞赛]
tags: [特征工程，NLP, 推荐]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/e24723f0956f7819c9bf479295b501f7-RB7X0Q5te6s-86a933.jpg
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg #

---

## SouHu Cup 2022

## 数据分析



| 列名        | 解释                                                         |
| ----------- | ------------------------------------------------------------ |
| sampleId    | 训练样本的id                                                 |
| label       | 正样本(曝光并点击)和负样本(曝光但未点击)，用户的行为(1为有点击，0为未点击) |
| pvId        | 每次曝光给用户的展示结果列表称为一个Group(每个Group都有唯一的pvId)  相同用户id应该是一样的 |
| suv         | 推测是用户id                                                 |
| itemId      | 每个推荐的文章id                                             |
| userSeq     | 用户之前的点击序列，相同用户id是相同的                       |
| logTs       | 当前时间戳                                                   |
| operator    | 操作系统                                                     |
| browserType | 浏览器                                                       |
| deviceType  | 设备                                                         |
| osType      | 运营商                                                       |
| province    | 省份                                                         |
| city        | 城市                                                         |

## Jake Baseline

- 训练了一个Bert模型，预测了task 1 的结果。

- 将task2的train 和 test 数据的itemId拼接，把task2的实体挑选出来，然后利用上面的模型进行预测，作为task 2 任务的特征。

  验证集AUC如下：

  ![image-20220501091913603](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/05/01/084dacc8ecc15406e58b0d0bb2bc0788-image-20220501091913603-3a2668.png)

### 加入通用特征

就是把所有的特征分为类别类型和数值类型，然后分别进行数据统计，生成df_feat特征，拼接到原始数据中，进行计算。线下准确率如下：

![image-20220501091752392](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/05/01/ea399cdea4d08cbbcfbcf43ac2e118ab-image-20220501091752392-4eff99.png)

![image-20220501093124866](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/05/01/d347306604993caa779d32790daa39e9-image-20220501093124866-e4784d.png)

#### 效果不好 利用随机森林进行特征筛选



### 针对useseq处理

将其序列利用word2vec进行embedding，将其加入到特征中



## 想法

- 把不同变量之间进行组合
- 针对时间戳怎么处理
- 针对useseq怎么处理 
- user-item网络  graph embedding
- 模型融合

