---
title: Git-标签管理
tags: [git]
categories: [Git]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/31/75c3fd05a3a3a095f6aa8a79d0720676-image-20220331174845354-4f3399.png
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg

---

## 标签管理

发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。

tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起。

### 创建标签

切换到需要打标签的分支，敲命令`git tag <name>`就可以打一个新标签：

```
$ git tag v1.0
```

可以用命令`git tag`查看所有标签：

```
$ git tag
v1.0
```

默认标签是打在最新提交的commit上的。有时候，如果忘了打标签，比如，现在已经是周五了，但应该在周一打的标签没有打，怎么办？

方法是找到历史提交的commit id，然后打上就可以了：

```
$ git log --pretty=oneline --abbrev-commit
a70aa75 (HEAD -> dev, tag: v1.0, origin/main, origin/dev, main) conflict fixed
b33a7d9 main
c28d724 feature
f2edd24 & simple
68e9b13 AND simple
b6ea9c3 branch test
a3158c5 remove test.txt
f57c096 add test.txt
dfed0cf git tracks changes
65b2fa1 append GPL
6cd35de 修改readme
b2fe4ff wrote a readme file
```

```
$ git tag v0.9 c28d724

$ git tag
v0.9
v1.0
```

注意，标签不是按时间顺序列出，而是按字母排序的。可以用`git show <tagname>`查看标签信息：

```
$ git show v0.9
commit c28d724d4fdc8515c3993900df166db1ef031e41 (tag: v0.9)
Author: zhiqiang00 <1941686805@qq.com>
Date:   Sat Apr 9 17:50:00 2022 +0800

    feature

diff --git a/readme.txt b/readme.txt
index 855483a..3d1a029 100644
--- a/readme.txt
+++ b/readme.txt
@@ -2,4 +2,4 @@ Git is a distributed version control system.
 Git is free software distributed under the GPL.
 Git has a mutable index called stage.
 Git tracks changes.
-Creating a new branch is quick AND simple.
\ No newline at end of file
+Creating a new branch is quick & simple. feature1
\ No newline at end of file
```

还可以创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：

```
$ git tag -a v0.1 -m "version 0.1 released" 1094adb
```

用命令`git show <tagname>`可以看到说明文字。

**标签是指向commit的死指针，分支是指向commit的活指针**

### 操作标签

如果标签打错了，也可以删除：

```
$ git tag -d v1.0
Deleted tag 'v1.0' (was a70aa75)
```

因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。

如果要推送某个标签到远程，使用命令`git push origin <tagname>`：

```
$ git push origin v0.9
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/zhiqiang00/learngit.git
 * [new tag]         v0.9 -> v0.9
```

或者，一次性推送全部尚未推送到远程的本地标签：

```
$ git push origin --tags
```

如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除：

```
$ git tag -d v0.9
Deleted tag 'v0.9' (was c28d724)
```

然后，从远程删除。删除命令也是push，但是格式如下：

```
$ git push origin :refs/tags/v0.9
To https://github.com/zhiqiang00/learngit.git
 - [deleted]         v0.9
```

要看看是否真的从远程库删除了标签，可以登陆GitHub查看。

还可以可以使用`git push origin -d tagName` 

也可以 `git push origin -d tag tagName`

但是如果远程分支名和标签名重名,那么第一种就会报一个错误,第二种可以指定删除标签。都可以用，标签又不跟分支有联系。即使重名第一种删不了任何东西

<br/><br/><br/>

## 引用

[廖雪峰的官方网站](