---
title: 使用外网ip连接服务器
categories: [linux]
tags: [linux]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/7f0f94f3ea0db706c233c4bcb413c046-XfMeXNI42d8-b472ff.jpg
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg# 
---

## 使用外网ip连接服务器

## 问题背景

实验室的服务器没有和和学校申请固定的外网 `ip` ，导致在服务器使用ppp拨号连接互联网的时候，即使连接了学校里面的校园 `wifi` 依然无法连接到服务器，而在学校外面连接VPN更是不行，只能通过连接网线的方式使用。这是很痛苦的事情，因为不连互联网就无法下载包，连接了就连接不到网络。就在刚刚突然想起来一个方法，可以暂时解决和这个问题。

解决方案：

直接连接服务器的外网 `ip`

输入命令 `ifconfig`，找到ppp，如图，然后就可以使用wifi连接了。

![image-20220415110246270](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/15/297cf9d6188d23a822573885da3e300f-image-20220415110246270-115cc5.png)

## 缺点：

- 外网 `ip` 可能不稳定，有可能因为关机等问题发生改变；
- 还没有尝试校外VPN是否有效，但是理论上应该可以；

