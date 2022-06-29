---
title: Git-远程仓库
tags: [git]
categories: [Git]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/31/75c3fd05a3a3a095f6aa8a79d0720676-image-20220331174845354-4f3399.png
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg
---

## 远程仓库

## 1 添加远程仓库

如果你已经在本地有一个Git仓库了，邮箱在GitHub创建一个仓库，并且让这个两个仓库进行远程同步。

- 在GitHub上创建一个新的仓库 `learngit`

- 关联远程仓库

  ```
  git remote add origin https://github.com/zhiqiang00/learngit.git
  ```

  其中，远程仓库的名字就是`origin` ， 这还少Git的默认叫法，这个名字一看就是远程仓库。

- 此时本地仓库的名字是 `master` ，但是现在GitHub现在主分支已经是 `main` 了，所以首先修改本地分支名字。

  ```
  git branch -M main
  ```

  一个改名操作，所以本地要保证先有 master 分支，还可以这样 `git branch -m oldName  newName` 。

- 把本地仓库推送到远程，用 `git push` 实际是把当前分支 `main` 推送到远程了。

```
git push -u origin main
```

我们第一次推送 `main` 的时候，加上了 `-u` ，不仅会把本地 `main` 推送到远程新的 `main` 分支，还会把本地和远程的 `main` 分支关联起来，这样以后推送或者拉取的时候就会化简命令了。

- 之后，只要我们在本地提交了，就可以使用如下命令：

```
git push origin main
```

## 2 删除远程仓库

可以使用 `git remote rm <name>` 删除远程仓库。建议先使用 `git remote -v` 查看远程库信息。

```
$ git remote -v
origin  https://github.com/zhiqiang00/learngit.git (fetch)
origin  https://github.com/zhiqiang00/learngit.git (push)
```

然后根据返回的名字删除

```
git remote rm origin
```

注意，这里只是删除本地和远程的关联关系，而不是删除物理上删除远程仓库。如果要删除远程仓库，还是要登录GitHub删除。

#### 3 从远程库克隆

区别于上面先有本地仓库，如我们还没有开始项目，最好是先创建远程仓库，然后从远程仓库克隆。

在创建远程仓库的时候，要勾选 `Add a README file` 。

![image-20220408161246490](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/08/5acd1378aa53681d314f5eb16c6a399b-image-20220408161246490-cfc5f0.png)

然后使用 `git clone` 克隆到本地。

GitHub 给出了多个地址，因为Git支持多种协议，默认的 `git://` 使用ssh，但是也使用 `https` 。



<br/><br/><br/>

## 引用

[廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600/896202780297248)