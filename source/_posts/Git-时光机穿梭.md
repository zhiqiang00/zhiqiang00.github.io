---
title: Git-时光机穿梭
tags: [git]
categories: [Git]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/31/75c3fd05a3a3a095f6aa8a79d0720676-image-20220331174845354-4f3399.png
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg
---
## Git-时光机穿梭

更该之前提交的readme.txt文件，然后运行 `git status` 命令查看结果：

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

`git status` 用来查看仓库当前的状态。这时只是告诉我们有文件被修改了，可以使用 `git diff` 命令查看具体改了什么。

```
$ git diff
diff --git a/readme.txt b/readme.txt
index 9f7547c..b18bfcc 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is a distributed version control system.^M
 Git is free software.
\ No newline at end of file
```

通常会显示的格式正是Unix通用的diff格式。

然后我们输入 `git status` 和 `git commit` 并输入 `git status` 查看状态。

```
$ git status
On branch master
nothing to commit, working tree clean
```

Git告诉我们当前没有需要提交的修改，而且，工作目录是干净（working tree clean）的。

## 1 版本回退

再进行一次修改，用来进行验证。

使用 `git log` 命令显示从最近到最远的提交日志。

```
$ git log
commit 65b2fa1813525287243b334583a3460799c02895 (HEAD -> master)
Author: zhiqiang00 <1941686805@qq.com>
Date:   Wed Apr 6 18:39:32 2022 +0800

    append GPL

commit 6cd35de4f4649ab8ad5093bb6cc69eea57858ff4
Author: zhiqiang00 <1941686805@qq.com>
Date:   Wed Apr 6 18:36:36 2022 +0800

    修改readme

commit b2fe4fff91ff3a0d22077ede1176eac3d6a9b4a3
Author: zhiqiang00 <1941686805@qq.com>
Date:   Wed Apr 6 11:45:50 2022 +0800

    wrote a readme file
```



如果嫌输出信息太多，看得眼花缭乱的，可以试试加上`--pretty=oneline`参数：

```
$ git log --pretty=oneline
65b2fa1813525287243b334583a3460799c02895 (HEAD -> master) append GPL
6cd35de4f4649ab8ad5093bb6cc69eea57858ff4 修改readme
b2fe4fff91ff3a0d22077ede1176eac3d6a9b4a3 wrote a readme file
```

**准备进行回滚**。首先Git需要知道当前是哪个版本，在Git中head表示当前版本，也就是最近commit的。上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

现在把版本回退到上一个版本，使用 `git reset` :

```
$ git log
commit 6cd35de4f4649ab8ad5093bb6cc69eea57858ff4 (HEAD -> master)
Author: zhiqiang00 <1941686805@qq.com>
Date:   Wed Apr 6 18:36:36 2022 +0800

    修改readme

commit b2fe4fff91ff3a0d22077ede1176eac3d6a9b4a3
Author: zhiqiang00 <1941686805@qq.com>
Date:   Wed Apr 6 11:45:50 2022 +0800

    wrote a readme file
```

可以看到就剩下两个版本了。如果我们想要找回刚刚的那个最新的版本，就需要在命令行窗口还没有被关掉的时候去找到那个 `append GPL` 的 `commit id` ，然后指定回到未来的某个版本。

```
$ git reset --hard 65b2fa1813525287243b334583a3460799c02895
HEAD is now at 65b2fa1 append GPL
```

版本号没必要写全，前几位就可以了，Git会自动去找。当然也不能只写前一两位，因为Git可能会找到多个版本号，就无法确定是哪一个了。

![image-20220406192748297](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/06/0eb896e060889f6ca1b02758783ac351-image-20220406192748297-80f9ca.png)

如果窗口已经关了怎么办呢？Git提供了一个命令 `git reflog` 来记录我们的每一次命令。

```
$ git reflog
65b2fa1 (HEAD -> master) HEAD@{0}: reset: moving to 65b2fa1813525287243b334583a3460799c02895
6cd35de HEAD@{1}: reset: moving to HEAD^
65b2fa1 (HEAD -> master) HEAD@{2}: commit: append GPL
6cd35de HEAD@{3}: commit: 修改readme
b2fe4ff HEAD@{4}: commit (initial): wrote a readme file
```

这个时候又可以看见 `commit id` 了，又可以进行向后翻滚了(最新的版本)。

## 2 工作去和暂存区

- 工作区

  就是我们在文件中能看到的目录，即learngit，文件夹就是一个工作区

- 版本库

  工作区里面有 `.git` 隐藏目录，这个是Git的版本库。

  Git的版本库最重要的两部分就是暂存区stage和暂存区分支master(仓库)，以及指向master的HEAD指针。

![image-20220407134230971](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/07/959063b0157c923ef1bd943d71bca6ff-image-20220407134230971-3d10b5.png)

我们平时把文件添加到GIt版本库的时候，分两步：

- `git add` 把文件添加到暂存区stage
- `git commit` 实际上是把暂存区的所有内容提交到当前分支。

其实就是我们平时都是将提交的文件放在暂存区，然后一次性提交暂存区所有修改。

**总结：**

```
Git管理的文件分为：工作区，版本库，版本库又分为暂存区stage和暂存区分支master(仓库)

工作区>>>>暂存区>>>>仓库

git add把文件从工作区>>>>暂存区，git commit把文件从暂存区>>>>仓库，

git diff查看工作区和暂存区差异，

git diff --cached查看暂存区和仓库差异，

git diff HEAD 查看工作区和仓库的差异，

git add的反向命令git checkout，撤销工作区修改，即把暂存区最新版本转移到工作区，

git commit的反向命令git reset HEAD，就是把仓库最新版本转移到暂存区。
```



## 3 管理修改

**Git优秀的地方在于它跟踪并管理的是修改而非文件。**

- 第一次修改 -> `git add` -> 第二次修改 -> `git commit` 在这种情况下，第二层修改是不会被commit到分支仓库的。原因是 `git add` 只会讲工作区的修改存放暂存区。而 `git commit` 只负责将暂存区的修改提交到分支仓库，而不会直接把工作区的修改提交至分支仓库。

- 提交后，用 `git diff HEAD -- readme.txt` 命令可以查看工作区和版本库里面最新版本的区别：

```
$ git diff HEAD -- readme.txt
diff --git a/readme.txt b/readme.txt
index 9da90e9..dce515b 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,4 +1,4 @@
 Git is a distributed version control system.
 Git is free software distributed under the GPL.
 Git has a mutable index called stage.
-Git tracks changes.
+Git tracks changes of files.
```

可见，确实第二次修改没有被提交。

## 4 撤销修改

### 4.1 写完未 `git add` 

`git checkout -- file`命令丢弃工作区的修改，也就是  `git add` 的相反操作，撤销工作区修改，即把暂存区最新版本转移到工作区。命令中的`--`很重要，没有`--`，`--` 和 `file` 之间有有空格。 就变成了“切换到另一个分支”的命令，我们在后面的分支管理中会再次遇到`git checkout`命令。

### 4.2 已 `git add` 了

`git reset HEAD <file>`命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用`HEAD`时，表示最新的版本。**这时候暂存区是干净的，工作区有修改**。当我们用 `HEAD` 时，表示最新的版本

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

使用 `git checkout -- file` 命令丢弃工作区的修改

```
$ git status
On branch master
nothing to commit, working tree clean
```

根据最新的版本，现在有新的命令可以等效替代：

```
把工作区的修改撤销： git checkout -- file  ==  git restore [--worktree] file
从暂存区恢复到工作区：git reset HEAD file   ==  git restore --staged file
```

### 4.3 已 `git commit` 

可以版本回退，但是前提是你没有push到远程，如果推送到远程，就真的惨了。

## 5 删除文件

创建了一个test.txt文件，然后 `git commit` 提交。

使用 `rm test.txt` 删除test.txt文件，查看状态。

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    test.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

这时候有两个选择：

- 是确实要从版本库中删除该文件，那就用命令 `git rm` 删掉，并且 `git commit` 。

```
$ git rm test.txt
rm 'test.txt'

$ git commit -m "remove test.txt"
[master a3158c5] remove test.txt
 1 file changed, 1 deletion(-)
 delete mode 100644 test.txt
```

- 另一种情况是删错了，因为版本库里还有呢，所以可以把误删的文件恢复到最新版本：

```
$ git checkout -- test.txt
```

 `git checkout` 其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

 注意：从来没有被添加到版本库就被删除的文件，是无法恢复的！



## 引用

[廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600/896202780297248)

