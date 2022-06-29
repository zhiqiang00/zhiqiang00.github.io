---
title: Competition-KDD22SDWPFC
date: 2022年4月1日
categories: [竞赛]
tags: [特征工程，时间序列]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/e24723f0956f7819c9bf479295b501f7-RB7X0Q5te6s-86a933.jpg
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg #
---

## KDD Cup 2022：风力发电预测赛题解析

### Baseline

- 首先把前面的 id day 和 时间戳去掉了 只留下后面有用的特征
- train val test 都会预留一个输入序列 in_put
- 这里处理的非常巧妙，就是使用tid乘以时间，这样就可以顺利的每次只针对一个风机进行时间处理。
- 注意数据单位













## idea

- 机舱温度和室外温度差值
- 每个turb受到周围turb的影响，比如根据那个经纬度把一定范围的turb的p进行聚合平均
- 风向和‘'风向与涡轮机舱位置的夹角'' 直接的组合(线性组合) 和功率的关系
- 之一段时间某些特征的频率
- 单列特征使用序列预测，然后使用特征横向预测target，最后与baseline进行融合
- 构建二阶特征

### 疑问

- 数据缺失:数据拟合 /  用中值/平均值填充缺失的值?(或移动平均值)     用邻居直接填充 fillna(method='ffill')

![image-20220608193627591](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/06/08/5a5d559de79c7f19ba83cfecb782dc02-image-20220608193627591-738240.png)

- 
