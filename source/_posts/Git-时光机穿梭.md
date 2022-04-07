---
title: Git-时光机穿梭
date: 2022年3月31日
tags: [git]
categories: [Git]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/31/75c3fd05a3a3a095f6aa8a79d0720676-image-20220331174845354-4f3399.png
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg
---
## 1 Git-时光机穿梭

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

## 1.1 版本回退

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









**疑难解答：**

<br/><br/><br/>

## 引用

[廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600/896202780297248)

