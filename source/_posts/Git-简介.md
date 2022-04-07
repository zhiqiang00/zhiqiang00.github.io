---
title: Git-简介
date: 2022年3月31日
tags: [git]
categories: [Git]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/31/75c3fd05a3a3a095f6aa8a79d0720676-image-20220331174845354-4f3399.png
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg
---
## 1 Git-简介

### 1.1 Git的诞生

Linus在1991年创建了开源的Linux。Linus最开始是使用Bitkeeper进行版本控制，后来因为社区有人尝试破解Bitkeeper的协议，BitKeeper公司要收回免费使用权。于是，Linus花了两周时间自己用C写了一个分布式版本控制，这就是**Git**。

### 1.2 集中式 VS 分布式

- **集中式**

  集中式版本控制系统，版本库集中存放中央服务器。在干活的时候，需要先从中央服务器取得最新版本，然后开始干活，干完活后再把自己的活推送给中央服务器。集中式最大的毛病就是必须联网才可以。

  所有的版本数据都存在服务器上，用户的本地设备只能看到自己以前所同步的版本，如果不连网的话，用户就看不到历史版本。

![image-20220406105153228](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/06/ac5b0c9152a30aef52c1871194cd4b05-image-20220406105153228-506b89.png)

![image-20220406110129054](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/06/0f73c49a3f69289c7b533a878f646804-image-20220406110129054-a85b48.png)

- **分布式**

  分布式控制系统中没有“中央服务器”，每个人电脑都是一个完整的版本库。两个人同时修改同一个文件**A**，这是只需要把各自修改的内容推送到对方，就可以相互看到对方的修改了。分布式除了不需要联网外，安全性能要高很多，因为每个人电脑里面都有一个完整版。

  也需要一个“版本集中存放的服务器”，但是这个服务器仅用来方便“交换”修改，没有他一样干活。**而每一台电脑有各自独立的开发环境，不需要联网，本地直接运行，相对集中式安全系数高很多。**

  分布式不是复制指定版本的快照，而是把所有的版本信息仓库同步到本地，可以离线本地提交，只有需要联网时push到相应的服务器或者其他用户那里。也就是平时自己`git add`就是本地，`git push`就是同步远程。

![image-20220406105212046](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/06/04f91ae7fa2ddf9fe585217bcd1212f2-image-20220406105212046-23c76b.png)

![image-20220406110720175](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/06/b443a8cfaae5c9f9c308b3149e24611b-image-20220406110720175-db0c3d.png)



- **区别**
  你的本地是否有完整的版本库历史！
  假设SVN服务器没了，那你丢掉了所有历史信息，因为你的本地只有当前版本以及部分历史信息。SVN每次都需要联网commit。
  假设GitHub服务器没了，你不会丢掉任何git历史信息，因为你的本地有完整的版本库信息。你可以把本地的git库重新上传到另外的git服务商。Git只有在Push、pull 的时候需要联网，而我们平时更多的操作应是commit。

  针对分布式，我们平时用git commit的命令就是在自己本地仓库进行版本控制，和远程没关系。只有需要大家一起同步的时，我们才会“随便”找一个大家统一认定的电脑进行同步。也就是说，分布式是“同步原则”，平时自己提交到自己的本地仓库，需要同步的时候，我们进行push同步。即使断网了，让然可以进行版本回滚和更新，因为有完整的版本信息。

  针对集中式，我们本地只有当前版本和部分历史版本，这时候如果断网或者中央服务器挂了，那么所有的历史版本就都没有了，无法回滚版本。每次commit都需要联网，所以无法进行回滚和更新。



## 2 Git安装

Git是分布式版本控制系统，所以每个机器都必须说明自己的情况：你的名字和Email地址。

注意`git config`命令的`--global`参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

## 3 创建版本库

所谓版本库(repository)可以理解为一个目录，里面的所有的文件都可以被Git管理起来，能跟踪文件的修改、删除，便于追踪和还原。

### 3.1 创建空目录：

```
mkdir learngit
cd learngit
pwd
/d/learngit
```

### 3.2 初始化为IGt可以管理的仓库

```
$ git init
Initialized empty Git repository in D:/learngit/.git/
```

该目录下面会生成一个`.git`的目录，用于跟踪和管理版本库。当然，不一定在新的空目录下，有东西的目录也可以初始化为Git仓库。

### 3.2 把文件添加到版本库

首先明确，所有的版本控制系统其实只能跟踪文本文件的改动，比如TXT文件网页和代码等，包括Git，无法跟踪图片、视频等二进制文件，只知道大小改变了，无法知道改了哪里。

不要使用Windows自带的记事本，原因是Microsoft开发记事本的团队使用在每个文件开头添加了0xefbbbf（十六进制）的字符的方式来保存UTF-8编码的文件，会导致遇到很多不可思议的问题。

第一步，使用 `git add` 告诉Git，把文件添加到仓库。

```
$ git add readme.txt
```

没有输出。

第二步，使用 `git commit` 告诉Git，把文件提交到仓库。

```
$ git commit -m "wrote a readme file"
```

`git commit`命令执行成功后会告诉你，`1 file changed`：1个文件被改动（我们新添加的readme.txt文件）；`2 insertions`：插入了两行内容（readme.txt有两行内容）。



**疑难解答：**

Q：输入`git add readme.txt`，得到错误`fatal: pathspec 'readme.txt' did not match any files`。

A：添加某个文件时，该文件必须在当前目录下存在，用`ls`或者`dir`命令查看当前目录的文件，看看文件是否存在，或者是否写错了文件名。

<br/><br/><br/>

## 引用

[廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600/896202780297248)

[集中式版本控制与分布式版本控制](https://blog.csdn.net/sjt19910311/article/details/83685420?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-1.pc_relevant_paycolumn_v3&spm=1001.2101.3001.4242.2&utm_relevant_index=4)