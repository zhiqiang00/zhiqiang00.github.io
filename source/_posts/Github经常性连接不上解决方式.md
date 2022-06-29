---
title: Github经常性连接不上解决方式
categories: [工具使用]
tags: [Github]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/b36a188093daf2e64a217a84bf183201-nKO_1QyFh9o-2edcfd.jpg
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg
---
## Github经常性连接不上解决方式

> 平常开发经常性需要访问github代码,频繁连接不上github



## 解决方式

```
# 访问https://www.ipaddress.com/或者https://site.ip138.com/
# 找到目前github的IP映射
输入域名查询IP地址

# 修改本机的hosts文件
140.82.114.4	github.com
199.232.69.194	github.global.ssl.fastly.net
185.199.108.133	raw.githubusercontent.com

# ps.如果使用的是Mac本,可能需要添加更多的映射关系
---Mac本直接别用Safari浏览器,换Google浏览器就可以访问了
```