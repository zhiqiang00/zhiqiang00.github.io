---
title: Git-分支管理
tags: [git]
categories: [Git]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/31/75c3fd05a3a3a095f6aa8a79d0720676-image-20220331174845354-4f3399.png
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg
---

## 分支管理

## 1 创建与合并分支(这里以 `master` 为例)

最开始 `master` 分支只是一条线，Git用 `master` 指向最新的提交，再用 `HEAD` 指向 `master` 。就能确定当前分支以及当前分支的提交点了。

![image-20220409171302870](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/09/23dd5fde81f65f3896914d84f7f70ce3-image-20220409171302870-73ff25.png)

每次提交 `master` 都会向前走一步， `master` 这个分支的线也会越来越长。

当我们创建新的分支的时候，例如 `dev` 时，Git创建一个新的指针叫 `dev` ，指向 `master` 相同的提交， 再把 `HEAD` 指向 `dev` ，这就表示当前分支在 `dev` 上。

![image-20220409171556046](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/09/8fa5a6601e60902d223d4d48240da3d0-image-20220409171556046-8d1e91.png)

接下来，对工作区的修改和提交就是针对 `dev` 分支了，比如新提交一次后， `dev` 指针向前走一步，但是 `master` 指针不变：

![image-20220409171734897](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/09/1313d06b70fdadba27f075b73bd21612-image-20220409171734897-0fc171.png)

当我们在 `dev` 上完成工作后，通过直接吧 `master` 指针指向 `dev` 的当前提交就可以完成 `dev` 到 `master` 分支的合并了。

![image-20220409171942012](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/09/0da6b2de2e3cf5cd5f46aef5ad71c826-image-20220409171942012-2d47b4.png)

接下来进行实战：

- 创建 `dev` 分支，并切换到该分支。

  ```
  $ git checkout -b dev
  Switched to a new branch 'dev'
  ```

  `git checkout` 命令加上 `-b` 参数表示创建并切换，相当于以下两条命令：

  ```
  $ git branch dev
  $ git checkout dev
  Switched to branch 'dev'
  ```

- 查看当前分支

  ```
  $ git branch
  * dev
    main
  ```

- 修改文件，然后提交

  ```
  $ git add readme.txt 
  $ git commit -m "branch test"
  ```

- 然后切换回 `main` 分支，发现刚刚的修改不见了。

![image-20220409172639033](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/09/0350966ceb81f09f4dcd8c249ec8952a-image-20220409172639033-03cac9.png)

- 进行分支合并

  ```
  $ git merge dev
  Updating a3158c5..b6ea9c3
  Fast-forward
   readme.txt | 3 ++-
   1 file changed, 2 insertions(+), 1 deletion(-)
  ```

  `git merge` 命令用于合并指定的分支到**当前分支**。合并后，文件就会相同了。

  注意到上面的`Fast-forward`信息，Git告诉我们，这次合并是“快进模式”，也就是直接把`master`指向`dev`的当前提交，所以合并速度非常快。当然不是每次合并都能 `Fast-forward` 。

- 合并完成后，就可以删除分支啦

  ```
  $ git branch -d dev
  Deleted branch dev (was b6ea9c3).
  ```

- **switch**

  我们这里使用了 `git checkout <branch>` 来切换分支，前面讲过的撤销修改则是 `git checkout -- <file>` ，相同的命令不同的用法有些模糊。最新版本的Git提供了 `git switch` 命令用来切换分支。

  创建并切换到新的 `dev` 分支，可以使用：

  ```
  $ git switch -c dev
  ```

  直接切换到已有的`master`分支，可以使用：

  ```
  $ git switch main
  ```

## 2 解决冲突

当我们在不同分支对同一个文件进行修改而进行合并的时候，这种情况下，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突。

![image-20220409174157857](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/09/2cdaa5944c1f6ef607b5f3ceaa7f5051-image-20220409174157857-0c0f99.png)

准备新的分支，然后修改 readme.txt， 然后提交。

```
$ git switch -c feature1
```

切换回 `main` 分支，然后再修改 readme.txt 的同一行，然后提交。

我们尝试合并：

```
$ git merge feature1
Auto-merging readme.txt
CONFLICT (content): Merge conflict in readme.txt
Automatic merge failed; fix conflicts and then commit the result.
```

这是 `git status` 也会显示冲突。我们这时候查看 readme.txt 文件。

Git用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容，我们将冲突内存删除，保存，然后进行提交。

```
$ git add readme.txt
$ git commit -m "conflict fixed"
[main a70aa75] conflict fixed
```

![image-20220409175803838](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/09/41b0754703d0269c70a5f30bce8e9e34-image-20220409175803838-68a1db.png)

用带参数的`git log`也可以看到分支的合并情况：

```
$ git log --graph --pretty=oneline --abbrev-commit
*   a70aa75 (HEAD -> main) conflict fixed
|\
| * c28d724 (feature1) feature
* | b33a7d9 main
* | f2edd24 & simple
|/
* 68e9b13 AND simple
* b6ea9c3 branch test
* a3158c5 (origin/main) remove test.txt
* f57c096 add test.txt
* dfed0cf git tracks changes
* 65b2fa1 append GPL
* 6cd35de 修改readme
* b2fe4ff wrote a readme file
```

`git log --graph` 可以查看分支合并图。



#### 分支管理策略

通常，合并分支的时候 `Fast forward` 模式，但是这种模式下，删除分支后，会丢掉分支信息。强制禁用 `Fast forward` 模式，Gi就会在merge时生成一个新的commit，这样，从分支历史就可以看出来分支信息了。

使用 `--no-ff` `git merge`:

创建并切换 `dev` 分支：

```
$ git switch -c dev
Switched to a new branch 'dev'
```

修改readme.txt文件，并提交一个新的commit：

```
$ git commit -m "20220414"
[dev 41f894d] 20220414
 1 file changed, 1 insertion(+)
```

切换回 `main` :

```
$ git switch main
Switched to branch 'main'
Your branch is ahead of 'origin/main' by 6 commits.
  (use "git push" to publish your local commits)
```

这里的意思是本地仓库比远程 `origin/main` 提前了6个commit。

准备合并`dev`分支，请注意`--no-ff`参数，表示禁用`Fast forward`：

### 分支策略

实际开发中，master应该是非常稳定的，应该在 `dev` 分支上干活，然后合并。团队成员每个人都在 `dev` 分支上干活，然后时不时的往 `dev` 分支上合并就可以了。

![git-br-policy](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/04/14/3fde99592e66bb0030802f7f77bbd57e-0-799174)



## Bug 分支

软件开发中，bug就像家常便饭一样。有了bug就需要修复，在Git中，由于分支是如此的强大，所以，每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。

当你接到一个修复一个代号101的bug的任务时，很自然地，你想创建一个分支`issue-101`来修复它。

## Feature分支

软件开发中，总有无穷无尽的新的功能要不断添加进来。

添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。

## 多人协作

当你从远程仓库克隆时，实际上Git自动把本地的`master`分支和远程的`master`分支对应起来了，并且，远程仓库的默认名称是`origin`。

要查看远程库的信息，用`git remote`：

```
$ git remote
origin
```

或者，用`git remote -v`显示更详细的信息：

```
$ git remote -v
origin  git@github.com:michaelliao/learngit.git (fetch)
origin  git@github.com:michaelliao/learngit.git (push)
```

上面显示了可以抓取和推送的`origin`的地址。如果没有推送权限，就看不到push的地址。

### 推送分支

推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：

```
$ git push origin master
```

但是，并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？

- `master`分支是主分支，因此要时刻与远程同步；
- `dev`分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
- bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
- feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

### 抓取分支

当合作成员从远程库clone时，默认情况下，你的小伙伴只能看到本地的 `main` 分支。要在`dev`分支上开发，就必须创建远程`origin`的`dev`分支到本地，于是他用这个命令创建本地`dev`分支：

```
$ git checkout -b dev origin/dev
```

### 冲突

多个人同时对相同的文件进行修改，会发生冲突。Git已经提示我们，先用`git pull`把最新的提交从`origin/dev`抓下来，然后，在本地合并，解决冲突，再推送。

如果报错：

```
$ git pull
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

    git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=origin/<branch> dev
```

根据提示：

```
$ git branch --set-upstream-to=origin/dev dev
Branch 'dev' set up to track remote branch 'dev' from 'origin'.
```

设置这个东西，是让你不用每次都git push origin dev , git pull origin dev。关联后可以简化为git pull , git push。

再 `pull`，这回`git pull`成功，但是合并有冲突，需要手动解决，解决的方法和分支管理中的[解决冲突](http://www.liaoxuefeng.com/wiki/896043488029600/900004111093344)完全一样。解决后，提交，再push。

**多人协作的工作模式通常是这样：**

1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`。

**配置Git的时候，加上`--global`是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。**



#### Rebase

使用频率不高，用时再学。

<br/>

> 2022年4月14日  学完了Git的第一遍，我相信还会有第二遍、第三遍~



<br/><br/><br/>

## 引用

[廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600/896202780297248)