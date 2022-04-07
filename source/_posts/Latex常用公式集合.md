---
title: Latex常用公式集合
date: 2022年4月6日
categories: [工具使用]
tags: [Latex]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/06/c1417d854e47ac376731d98168bb5a26-image-20220406144003254-181b6d.png
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg
---



[TOC]

## \limits

\limits可以将它后续跟随的_和^的上下标从右侧转至正上和正下方。需要和 `\mathop` 配合使用
$$
a = y_i \\
a = \mathop{y} \limits^{1} \\
a = \mathop{y} \limits_{1} \\
$$

## \mathop

\mathop相当于一个定义，在\mathop后的{}内的内容将会被当作一个整体来处理，上下标的位置将会由\mathop的框定范围来决定。注意看 $\theta$ 的位置。
$$
\bar \theta = arg \ \mathop{max}_{\theta}  \sum^N_{i=1} score(y_i, pred_i) \\
\bar \theta = \mathop{\arg\min}_{\theta} \sum^N_{i=1} score(y_i, pred_i)
$$

## \\cdots

生成位于中间位置的省略号
$$
y_t = \frac{1}{N}(w_1 \times y_{t-1} + w_2 \times y_{t-2} + ... +w_N \times y_{t-N})\\
y_t = \frac{1}{N}(w_1 \times y_{t-1} + w_2 \times y_{t-2} + \cdots +w_N \times y_{t-N})
$$