---
title: 数据结构-堆排序(Heap Sort)
date: 2022年3月10日
categories: [数据结构]
tags: [堆，排序]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/c2b838b9c0b8134dfcd60c63c78d286a-nTYkr4p6B9w-bd3b9b.jpg
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg
---

## 一、堆Heap

堆是一个完全二叉树

大顶堆：每个非叶子节点大于或者等于其左右孩子节点的值

小顶堆：每个非叶子节点大于或者等于其左右孩子节点的值

根节点是大顶堆中最大的，是小顶堆中最小的

## 二、构建大顶堆

### 1. 核心算法

如果一个节点的左右孩子的最大值比它大，则进行交换。

如果节点被交换到新的位置，还要继续和它的孩子节点比较，重复上面的步骤。

### 2. 起点节点的选择

从完全二叉树最后一个节点的双亲节点开始，即最后一层的最右的父节点开始。

如果节点从0开始编号，长度为n，那么应该从n/2-1开始。

举个例子：a[6] = [7, 3, 8, 5, 1, 2]， 开始的位置是 6 /2=2,也就是8的位置。



<img src="https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/22/df3abae2089f6f1837d166d2706e833c-image-20220311175414861-80e8f1.png" alt="image-20220311175414861"  />	



### 3. 下个节点的选择

从起始位置开始后，向左找其同层的节点。一层结束后，就上一层继续从右向左。

总的来说，就是从下到上，从右到左。

### 4. 大顶堆的目标

确保每个节点都比左右节点的值大。

### 5. 排序

将大顶堆根节点这个最大值和最后一个叶子节点交换，那么最后一个节点就是最大值，将这个叶子节点**排在待排序节点之外**。

从根节点开始（新的根节点），重新调整为大顶堆后，重复上一步。

堆顶和最后一个节点交换，并排除最后一个节点，完成排序。

**另一种说法：**大顶堆的交换顶部元素A和最后一个元素B，堆的size减1，再将顶部的B执行下沉过程，最后返回元素A。注意，虽然堆的size减小了1，但实际上并没有元素被删除，数组长度也没有任何变化，被pop的元素只是被放在了数组中size之后的位置。

### 6. 插入元素

在大顶堆中插入一个元素，分为如下两种情况：

- 堆未满，将元素放在当前最后一个元素的后面，然后执行上浮过程； 
- 堆已满，如果该0元素大于堆顶则无法插入，小于堆顶则替换堆顶，再执行下沉过程。

### 7. 总结

- 利用堆性质的一种选择排序，在堆顶选出最大值或者最小值
- **时间复杂度：**O(nlogn)  由于堆排序对原始记录的排序状态并不敏感，因此它无论是最好、最坏和平均时间复杂度均为O(nlogn)
- **空间复杂度：** 只是使用了一个交换用的空间，空间复杂度就是O(1)

- 不稳定的排序算法

### 8. 代码实现





**参考博客**

> [Python入门篇-数据结构堆排序Heap Sort](https://www.cnblogs.com/yinzhengjie/p/10970889.html)
>
> [拜托，面试别再问我堆（排序）了！](https://zhuanlan.zhihu.com/p/63089552)
>
> [图解大顶堆的构建、排序过程](https://www.cnblogs.com/sunshineliulu/p/12995910.html)