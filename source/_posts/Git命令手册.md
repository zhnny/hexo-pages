---
title: Git命令手册
date: 2022-03-09 21:42:10
description: Git常用命令的介绍与概述
categories:
- 开发工具
tags:
- Git
---

## 1. 版本回退

笔者文件经过两次修改，可以通过`git log`查看

```SHELL
[root@LAPTOP-1UJN7PP7 git-learn]# git log
commit 5e3f973bf0248775995e6197cb999c874abc3859
Author: root <root@LAPTOP-1UJN7PP7.localdomain>
Date:   Tue Mar 1 18:17:58 2022 +0800

    second commit

commit 196ad7fafa50d6558e2c4da42344e7ff292d1447
Author: root <root@LAPTOP-1UJN7PP7.localdomain>
Date:   Tue Mar 1 18:15:54 2022 +0800

    first commit

```

如果嫌输出信息太多，看得眼花缭乱的，可以试试加上`--pretty=oneline`参数：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git log --pretty=oneline
5e3f973bf0248775995e6197cb999c874abc3859 second commit
196ad7fafa50d6558e2c4da42344e7ff292d1447 first commit

```

注意：类似于`5e3f...`这一大串数字是`commit id `，是一个SHA1计算出来的一个非常大的数字，用十六进制表示，用来唯一辨识每个版本

Git中，用`HEAD`表示当前版本，上一个版本是`HEAD^`，上上个版本是`HEAD^^`，如果数量过多，可用`HEAD^n`表示，n表示一个数字。

回退上一个版本：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git reset --hard HEAD^
HEAD is now at 196ad7f first commit

```

- **--hard** 参数撤销工作区中所有未提交的修改内容，将暂存区与工作区都回到上一次版本，并删除之前的所有信息提交

此时`Git log`中已经没有后来那次版本的信息：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git log --pretty=oneline
196ad7fafa50d6558e2c4da42344e7ff292d1447 first commit

```

可以使用`git reflog`查看操作记录：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git reflog
196ad7f HEAD@{0}: reset: moving to HEAD^
5e3f973 HEAD@{1}: commit: second commit
196ad7f HEAD@{2}: commit (initial): first commit

```

可以查看到第二次提交的commit id，从而再次变更为第二次版本：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git reset --hard 5e3f973
HEAD is now at 5e3f973 second commit

```

## 2. 工作区与暂存区

文件所在地方为工作区，当文件被修改后，用`git status`查看：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git status
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       modified:   README.md
#
no changes added to commit (use "git add" and/or "git commit -a")

```

显示文件已被修改但是没有add和commit

add文件后，文件来到暂存区，用`git status`查看：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git status
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#       modified:   README.md
#

```

![git-stage](https://s2.loli.net/2022/03/01/EYoZUWvz8tfis1N.jpg)

显示文件没有commit

commit文件后，文件来到版本库，用`git status`查看：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git status
# On branch master
nothing to commit, working directory clean

```

![git-stage-after-commit](https://s2.loli.net/2022/03/01/6MscyI19UOJrzuv.jpg)

## 3.管理修改

Git比其他版本控制系统设计得优秀，因为Git跟踪并管理的是修改，而非文件

修改文件：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# echo "This is a new line" >> README.md
```

查看修改：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# cat README.md
This is third commit
This is a new line
```

添加到暂存区：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git add README.md
[root@LAPTOP-1UJN7PP7 git-learn]# git status
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#       modified:   README.md
#

```

再次修改文件：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# cat README.md
This is third commit
```

此时我们提交修改：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git commit -m "Add a new line"
[master 6e2db9d] Add a new line
 Committer: root <root@LAPTOP-1UJN7PP7.localdomain>
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly:

    git config --global user.name "Your Name"
    git config --global user.email you@example.com

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

 1 file changed, 1 insertion(+)

```

可以看到提交的是插入了一行，也就是上一次修改

我们看一下文件状态：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git status
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       modified:   README.md
#vi
no changes added to commit (use "git add" and/or "git commit -a")
```

可以看到工作区的修改存在且未添加到暂存区

所以，Git管理的是修改，当你用`git add`命令后，在工作区的第一次修改被放入暂存区，准备提交，但是，在工作区的第二次修改并没有放入暂存区，所以，`git commit`只负责把暂存区的修改提交了，也就是第一次的修改被提交了，第二次的修改不会被提交

提交后，用`git diff HEAD -- readme.txt`命令可以查看工作区和版本库里面最新版本的区别：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git diff HEAD  README.md
diff --git a/README.md b/README.md
index 28bc83d..62c3f45 100644
--- a/README.md
+++ b/README.md
@@ -1,2 +1 @@
 This is third commit
-This is a new line
```

可见，第二次修改确实没有被提交

## 4.撤销修改

### 4.1.修改但未提交

错误的修改文件但未提交:

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# echo "This is an error" >> README.md
[root@LAPTOP-1UJN7PP7 git-learn]# cat README.md
This git test file
This is an error

```

使用`git status`查看：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git status
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       modified:   README.md
#
no changes added to commit (use "git add" and/or "git commit -a")

```

Git提示可以`use "git checkout -- <file>..." to discard changes in working directory`，即使用`git checkout -- <file>`来丢弃修改：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git checkout -- README.md
[root@LAPTOP-1UJN7PP7 git-learn]# cat README.md
This git test file

```

- 注意：`git checkout -- file`命令中的`--`很重要，没有`--`，就变成了“切换到另一个分支”的命令

可见修改被丢弃

### 4.2.修改已添加到暂存区

如果已经添加到暂存区：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# cat README.md
This git test file
This is an error
[root@LAPTOP-1UJN7PP7 git-learn]# git add README.md
[root@LAPTOP-1UJN7PP7 git-learn]# git status
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#       modified:   README.md
#

```

Git提示`use "git reset HEAD <file>..." to unstage`，即使用`git reset HEAD <file>`来丢弃存储区修改：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git reset HEAD README.md
Unstaged changes after reset:
M       README.md

```

查看状态：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git status
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       modified:   README.md
#
no changes added to commit (use "git add" and/or "git commit -a")
[root@LAPTOP-1UJN7PP7 git-learn]# cat README.md
This git test file
This is an error

```

可见暂存区的修改被撤销了，工作区文件的修改还存在，这时候再使用`git checkout -- <file>`来丢弃修改：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git checkout -- README.md
[root@LAPTOP-1UJN7PP7 git-learn]# cat README.md
This git test file
[root@LAPTOP-1UJN7PP7 git-learn]# git status
# On branch master
nothing to commit, working directory clean

```

### 4.3.修改已提交

回退上一个版本：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git reset --hard HEAD^
HEAD is now at 196ad7f first commit

```

## 5.删除文件

删除工作区文件：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# rm -rf test.txt

```

查看状态：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git status
# On branch master
# Changes not staged for commit:
#   (use "git add/rm <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       deleted:    test.txt
#
no changes added to commit (use "git add" and/or "git commit -a")

```

Git知道我们删除了文件并提示`use "git add/rm <file>..." to update what will be committed`，即我们可以使用`git add <file>`或者`git rm <file>`来删除版本库中的文件：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git rm test.txt
rm 'test.txt'

```

然后再commit：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git commit -m "Delete a file"
[master af52d1c] Delete a dile
 Committer: root <root@LAPTOP-1UJN7PP7.localdomain>
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly:

    git config --global user.name "Your Name"
    git config --global user.email you@example.com

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

 1 file changed, 1 deletion(-)
 delete mode 100644 test.txt

```

如果文件只是添加到暂存区但是我们现在需要删除呢？撤销修改即可，使用`git reset HEAD <file>`

- 注意：先手动删除文件，然后使用`git rm <file>`和`git add<file>`效果是一样的
- 文件误删了可以使用版本库来撤销修改

## 6.创建与合并分支

查看分支：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git branch
* master

```

- 注意：*表示当前工作分区

创建分支：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git branch dev

```

这个时候我们在查看一下分支的情况：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git branch
  dev
* master

```

切换分支：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git checkout dev
Switched to branch 'dev'

```

- 注意：在新版本的Git中可使用switch来切换

- 创建+切换分支：`git checkout -b <name>`或者`git switch -c <name>`

合并某分支到当前分支(dev分支已经做了修改并且切换当前工作分支为master)：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git merge dev
Updating e9442cf..4dfabbc
Fast-forward
 README.md | 1 +
 dev.txt   | 1 +
 2 files changed, 2 insertions(+)
 create mode 100644 dev.txt

```

- 注意：此处dev分支和master分支不存在冲突

删除分支：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git branch -d dev
Deleted branch dev (was 4dfabbc).

```

## 7.解决冲突

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

此处先模拟一个冲突（创建一个分支并在两个分支做一个无法快速合并的提交，即存在冲突）：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git checkout -b feature
Switched to a new branch 'feature'
[root@LAPTOP-1UJN7PP7 git-learn]# ls
dev.txt  README.md  test.txt
[root@LAPTOP-1UJN7PP7 git-learn]# echo "This is feature line" >> README.md
[root@LAPTOP-1UJN7PP7 git-learn]# git add README.md
[root@LAPTOP-1UJN7PP7 git-learn]# git commit -m "Add a line in feature branch"
[feature 4ecad70] Add a line in feature branch
 Committer: root <root@LAPTOP-1UJN7PP7.localdomain>
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly:

    git config --global user.name "Your Name"
    git config --global user.email you@example.com

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

 1 file changed, 1 insertion(+)
[root@LAPTOP-1UJN7PP7 git-learn]# git checkout master
Switched to branch 'master'
[root@LAPTOP-1UJN7PP7 git-learn]# echo "This is master line" >> README.md
[root@LAPTOP-1UJN7PP7 git-learn]# git add README.md
[root@LAPTOP-1UJN7PP7 git-learn]# git commit -m "Add a line in master branch"
[master 9a22c75] Add a line in master branch
 Committer: root <root@LAPTOP-1UJN7PP7.localdomain>
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly:

    git config --global user.name "Your Name"
    git config --global user.email you@example.com

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

 1 file changed, 1 insertion(+)

```

我们尝试合并：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git merge feature
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.

```

发生冲突，无法自动合并

此时我们查看状态：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git status
# On branch master
# You have unmerged paths.
#   (fix conflicts and run "git commit")
#
# Unmerged paths:
#   (use "git add <file>..." to mark resolution)
#
#       both modified:      README.md
#
no changes added to commit (use "git add" and/or "git commit -a")

```

也提示存在冲突，并且提示`fix conflicts and run "git commit"`，即修改文件后使用`git commit`来解决冲突

我们查看文件：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# cat README.md
This git test file
This is dev line
<<<<<<< HEAD
This is master line
=======
This is feature line
>>>>>>> feature

```

Git用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容，我们修改如下后保存：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# cat README.md
This git test file
This is dev line
This is master and feature line

```

提交修改：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git add README.md
[root@LAPTOP-1UJN7PP7 git-learn]# git commit -m "conflict fixed"
[master e52bbb2] conflict fixed
 Committer: root <root@LAPTOP-1UJN7PP7.localdomain>
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly:

    git config --global user.name "Your Name"
    git config --global user.email you@example.com

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

```

可以看到冲突解决了

用带参数的`git log`可以看到分支的合并情况：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git log --graph --pretty=oneline --abbrev-commit
*   e52bbb2 conflict fixed
|\
| * 4ecad70 Add a line in feature branch
* | 9a22c75 Add a line in master branch
|/
* 4dfabbc dev branch first commit
* e9442cf Add test.txt
* c2f0b57 Modified a file
* 6e2db9d Add a new line
* 061be34 third commit
* 5e3f973 second commit
* 196ad7f first commit

```

其图示意思大致如下：

![image-20220308111031068](https://s2.loli.net/2022/03/08/hJgzldVL5qxnYoe.png)



## 8.分支策略

通常，合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息

如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息

合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并

以下是测试：

Fast-forward（默认）模式：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git checkout -b dev
Switched to a new branch 'dev'
[root@LAPTOP-1UJN7PP7 git-learn]# echo "This is a new line" >>dev.txt
[root@LAPTOP-1UJN7PP7 git-learn]# git add dev.txt
[root@LAPTOP-1UJN7PP7 git-learn]# git commit -m "Add a line in dev.txt"
[dev 9f34d0d] Add a line in dev.txt
 Committer: root <root@LAPTOP-1UJN7PP7.localdomain>
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly:

    git config --global user.name "Your Name"
    git config --global user.email you@example.com

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

 1 file changed, 1 insertion(+)
[root@LAPTOP-1UJN7PP7 git-learn]# git checkout master
Switched to branch 'master'
[root@LAPTOP-1UJN7PP7 git-learn]# git merge dev
Updating e52bbb2..9f34d0d
Fast-forward
 dev.txt | 1 +
 1 file changed, 1 insertion(+)
[root@LAPTOP-1UJN7PP7 git-learn]# git log
commit 9f34d0def7a71384b3528f9a1056fa1badc9d0c2
Author: root <root@LAPTOP-1UJN7PP7.localdomain>
Date:   Tue Mar 8 11:58:56 2022 +0800

    Add a line in dev.txt

commit e52bbb2ec0fd79188b47060ebe5ee515c14ac557
Merge: 9a22c75 4ecad70
Author: root <root@LAPTOP-1UJN7PP7.localdomain>
Date:   Tue Mar 8 11:06:11 2022 +0800

    conflict fixed


```

普通模式（--no-ff）：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git checkout -b dev
Switched to a new branch 'dev'
[root@LAPTOP-1UJN7PP7 git-learn]# echo "This is second new line" >>dev.txt
[root@LAPTOP-1UJN7PP7 git-learn]# git add dev.txt
[root@LAPTOP-1UJN7PP7 git-learn]# git commit -m "Add a line in dev.txt"
[dev 1e70bdc] Add a line in dev.txt
 Committer: root <root@LAPTOP-1UJN7PP7.localdomain>
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly:

    git config --global user.name "Your Name"
    git config --global user.email you@example.com

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

 1 file changed, 1 insertion(+)
[root@LAPTOP-1UJN7PP7 git-learn]# git checkout master
Switched to branch 'master'
[root@LAPTOP-1UJN7PP7 git-learn]# git merge dev --no-ff
Merge made by the 'recursive' strategy.
 dev.txt | 1 +
 1 file changed, 1 insertion(+)
[root@LAPTOP-1UJN7PP7 git-learn]# git log --graph --pretty=oneline --abbrev-commit
*   afca374 Merge branch 'dev'
|\
| * 1e70bdc Add a line in dev.txt
|/
* 9f34d0d Add a line in dev.txt
*   e52bbb2 conflict fixed
|\
| * 4ecad70 Add a line in feature branch
* | 9a22c75 Add a line in master branch
|/
* 4dfabbc dev branch first commit
* e9442cf Add test.txt
* c2f0b57 Modified a file
* 6e2db9d Add a new line
* 061be34 third commit
* 5e3f973 second commit
* 196ad7f first commit

```

## 9.BUG分支

你正在dev分支上开发你的代码，但是临时接到一个BUG修改任务，我们需要先存储现在的未完成的工作区内容，再创建一个BUG修复分支，修复完成后merge到master，并且由于dev分支也有这个BUG，需要也将这个修复复制到dev分支

存储工作区：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git stash
Saved working directory and index state WIP on dev: afca374 Merge branch 'dev'
HEAD is now at afca374 Merge branch 'dev'

```

切换到master分支并且创建BUG修复分支：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git checkout master
Switched to branch 'master'
[root@LAPTOP-1UJN7PP7 git-learn]# git checkout -b bug-fixed
Switched to a new branch 'bug-fixed'

```

修复BUG并提交和merge：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# echo "fix bug" >> README.md
[root@LAPTOP-1UJN7PP7 git-learn]# git add README.md
[root@LAPTOP-1UJN7PP7 git-learn]# git commit -m "bug fixed"
[bug-fixed b5abcdd] bug fixed
 Committer: root <root@LAPTOP-1UJN7PP7.localdomain>
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly:

    git config --global user.name "Your Name"
    git config --global user.email you@example.com

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

 1 file changed, 1 insertion(+)
[root@LAPTOP-1UJN7PP7 git-learn]# git checkout master
Switched to branch 'master'
[root@LAPTOP-1UJN7PP7 git-learn]# git merge bug-fixed
Updating afca374..b5abcdd
Fast-forward
 README.md | 1 +
 1 file changed, 1 insertion(+)

```

我们查看master分支：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git branch
  dev
* master
[root@LAPTOP-1UJN7PP7 git-learn]# cat README.md
This git test file
This is dev line
This is master and feature line
fix bug

```

可以看到BUG在master上已经修复

可是dev分支呢？

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git checkout dev
Switched to branch 'dev'
[root@LAPTOP-1UJN7PP7 git-learn]# cat README.md
This git test file
This is dev line
This is master and feature line

```

dev上的BUG可没修复

将master上的BUG修复复制到dev上：

- 获得修复的版本的`commit id`

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git checkout master
Switched to branch 'master'
[root@LAPTOP-1UJN7PP7 git-learn]# git log --graph
* commit b5abcdd2cb9294a98874cd3ab78a212566728dec
| Author: root <root@LAPTOP-1UJN7PP7.localdomain>
| Date:   Tue Mar 8 12:47:40 2022 +0800
|
|     bug fixed
|
*   commit afca374ed3030782154c0b289378286111006643
|\  Merge: 9f34d0d 1e70bdc
| | Author: root <root@LAPTOP-1UJN7PP7.localdomain>
| | Date:   Tue Mar 8 12:24:00 2022 +0800
| |
| |     Merge branch 'dev'
| |
| * commit 1e70bdc1dee854f049b18e11e0b0971c30fc34e6
|/  Author: root <root@LAPTOP-1UJN7PP7.localdomain>
|   Date:   Tue Mar 8 12:23:09 2022 +0800
|
|       Add a line in dev.txt
|

```

复制修复到dev：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git checkout dev
Switched to branch 'dev'
[root@LAPTOP-1UJN7PP7 git-learn]# git cherry-pick b5abcdd
[dev 7429c57] bug fixed
 Committer: root <root@LAPTOP-1UJN7PP7.localdomain>
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly:

    git config --global user.name "Your Name"
    git config --global user.email you@example.com

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

 1 file changed, 1 insertion(+)

```

查看状态：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# cat README.md
This git test file
This is dev line
This is master and feature line
fix bug
[root@LAPTOP-1UJN7PP7 git-learn]# git log --graph
* commit 7429c57090cf3a146ec6f64ff60658922e08b235
| Author: root <root@LAPTOP-1UJN7PP7.localdomain>
| Date:   Tue Mar 8 12:47:40 2022 +0800
|
|     bug fixed
|
*   commit afca374ed3030782154c0b289378286111006643
|\  Merge: 9f34d0d 1e70bdc
| | Author: root <root@LAPTOP-1UJN7PP7.localdomain>
| | Date:   Tue Mar 8 12:24:00 2022 +0800
| |
| |     Merge branch 'dev'
| |
| * commit 1e70bdc1dee854f049b18e11e0b0971c30fc34e6
|/  Author: root <root@LAPTOP-1UJN7PP7.localdomain>
|   Date:   Tue Mar 8 12:23:09 2022 +0800
|
|       Add a line in dev.txt
|

```

可以看到dev上的BUG确实修复了并且在dev上有了一次新的commit

最后，恢复工作区：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git stash pop
# On branch dev
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       modified:   dev.txt
#
no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (18f91d7c6f57e8b4b58d6172419a814a61b0d07e)

```

恢复的方法有两种：

- 一是用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除
- 另一种方式是用`git stash pop`，恢复的同时把stash内容也删了

## 10.feature分支

开发一个新feature，最好新建一个分支

顺利的话，开发完成后merge一下就好

如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git branch  -d dev
error: The branch 'dev' is not fully merged.
If you are sure you want to delete it, run 'git branch -D dev'.
[root@LAPTOP-1UJN7PP7 git-learn]# git branch  -D dev
Deleted branch dev (was 7429c57).

```

## 11.多人协作

查看远程仓库：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git remote -v
origin  git@github.com:zhnny/git-learn.git (fetch)
origin  git@github.com:zhnny/git-learn.git (push)

```

### 11.1.推送分支

推送master分支：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git push origin master
Everything up-to-date

```

推送dev分支：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git push origin dev
Counting objects: 5, done.
Delta compression using up to 12 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 360 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
remote:
remote: Create a pull request for 'dev' on GitHub by visiting:
remote:      https://github.com/zhnny/git-learn/pull/new/dev
remote:
To git@github.com:zhnny/git-learn.git
 * [new branch]      dev -> dev

```

### 11.2.抓取分支

克隆远端仓库：

```shell
[root@LAPTOP-1UJN7PP7 clone]# git clone git@github.com:zhnny/git-learn.git
Cloning into 'git-learn'...
remote: Enumerating objects: 44, done.
remote: Counting objects: 100% (44/44), done.
remote: Compressing objects: 100% (27/27), done.
remote: Total 44 (delta 4), reused 43 (delta 3), pack-reused 0
Receiving objects: 100% (44/44), 4.02 KiB | 0 bytes/s, done.
Resolving deltas: 100% (4/4), done.

```

默认情况下只有master分支：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git branch
* master

```

抓取dev分支：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git checkout -b dev origin/dev
Branch dev set up to track remote branch dev from origin.
Switched to a new branch 'dev'

```

### 11.3.处理冲突

一个人修改了dev并推送：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# echo "This is a new line" >> dev.txt
[root@LAPTOP-1UJN7PP7 git-learn]# git add dev.txt
[root@LAPTOP-1UJN7PP7 git-learn]# git commit -m "Add a line"
[dev 29f398e] Add a line
 Committer: root <root@LAPTOP-1UJN7PP7.localdomain>
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly:

    git config --global user.name "Your Name"
    git config --global user.email you@example.com

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

 1 file changed, 1 insertion(+)
[root@LAPTOP-1UJN7PP7 git-learn]# git push origin dev
Counting objects: 5, done.
Delta compression using up to 12 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 338 bytes | 0 bytes/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To git@github.com:zhnny/git-learn.git
   a7ae8d2..29f398e  dev -> dev

```

另一个人修改了dev并推送：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# echo "This is a new line" >> dev.txt
[root@LAPTOP-1UJN7PP7 git-learn]# git add dev.txt
[root@LAPTOP-1UJN7PP7 git-learn]# git commit -m "Add a line"
[dev 2a494fc] Add a line
 Committer: root <root@LAPTOP-1UJN7PP7.localdomain>
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly:

    git config --global user.name "Your Name"
    git config --global user.email you@example.com

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

 1 file changed, 1 insertion(+)
[root@LAPTOP-1UJN7PP7 git-learn]# git push origin dev
To git@github.com:zhnny/git-learn.git
 ! [rejected]        dev -> dev (fetch first)
error: failed to push some refs to 'git@github.com:zhnny/git-learn.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first merge the remote changes (e.g.,
hint: 'git pull') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.

```

可以看到后面这个push失败，Git提示我们先pull下来，merge后再推送

pull远程仓库：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git pull
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:zhnny/git-learn
   a7ae8d2..29f398e  dev        -> origin/dev
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details

    git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=origin/<branch> dev

```

- `git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`

建立链接关系：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git branch --set-upstream-to=origin/dev dev
Branch dev set up to track remote branch dev from origin.

```

再次pull：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git pull
Merge made by the 'recursive' strategy.

```

merge后再次push：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git push origin dev
Counting objects: 2, done.
Delta compression using up to 12 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 353 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), done.
To git@github.com:zhnny/git-learn.git
   29f398e..3eda6cf  dev -> dev

```

## 12.Rebase操作

rebase操作：

- rebase操作可以把本地未push的分叉提交历史整理成直线

- rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比

rebase前：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git log --graph --pretty=oneline
*   b3c1899f42d1364eee047c3ec22ffb7b18912d67 merge conflict
|\
| * 7d9e9e2375785c7d594da445823bc2ceffcb2137 Add a new line
| * 1ed7903f52d52ce675bdedeb29b41f8648aaefbc Add a new line
| * 3bc7183e800263d908d1b7701c9b15860e58beca Add a new line
| *   905a799a93d51d11a588e2493bdc23765b9b3fde merge confict
| |\
| * | aa43bf9c412070c05227e475a5c1f7d633f208c1 Add the fifth line
* | | 6fd7b915a404dbea8dea256c4f5f6e8e4376e9f5 Add a new line
| |/
|/|
* | cda61afd2f6b2c7dcad3db5abe8e1f8304354fbe Add the forth line
* | 44e789020405f50780964136663edd4701bb39d7 Add the third line
|/
* 74a80dc3c466574717ccfffbf341609259e111a6 Add the second new line
* e85a234700ebf67aa23434d9bde59b18845eadee Add a new line
* 1a20b8eba002fcd70bbb6ca039dc41fe66ab1d81 Add a line

```

rebase：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git rebase
First, rewinding head to replay your work on top of it...
Applying: Add a new line
Using index info to reconstruct a base tree...
M       dev.txt
Falling back to patching base and 3-way merge...
Auto-merging dev.txt
CONFLICT (content): Merge conflict in dev.txt
Failed to merge in the changes.
Patch failed at 0001 Add a new line
The copy of the patch that failed is found in:
   /root/clone/git-learn/.git/rebase-apply/patch

When you have resolved this problem, run "git rebase --continue".
If you prefer to skip this patch, run "git rebase --skip" instead.
To check out the original branch and stop rebasing, run "git rebase --abort".


```

在`rebase`的过程中，也许会出现冲突(conflict)。在这种情况，Git会停止`rebase`并会让你去解决冲突；在解决完冲突后，用”`git add`“命令去更新这些内容的索引(index), 然后，你无需执行 `git commit`,只要执行：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git rebase --continue
Applying: Add a new line

```

rebase后：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git log --graph --pretty=oneline
* 9119f8fb6a62595fa70456132aa7329aee9630e0 Add a new line
* 7d9e9e2375785c7d594da445823bc2ceffcb2137 Add a new line
* 1ed7903f52d52ce675bdedeb29b41f8648aaefbc Add a new line
* 3bc7183e800263d908d1b7701c9b15860e58beca Add a new line
*   905a799a93d51d11a588e2493bdc23765b9b3fde merge confict
|\
| * cda61afd2f6b2c7dcad3db5abe8e1f8304354fbe Add the forth line
| * 44e789020405f50780964136663edd4701bb39d7 Add the third line
* | aa43bf9c412070c05227e475a5c1f7d633f208c1 Add the fifth line
|/
* 74a80dc3c466574717ccfffbf341609259e111a6 Add the second new line
* e85a234700ebf67aa23434d9bde59b18845eadee Add a new line
* 1a20b8eba002fcd70bbb6ca039dc41fe66ab1d81 Add a line

```

在任何时候，可以用`--abort`参数来终止`rebase`的操作，并且 分支会回到`rebase`开始前的状态：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git rebase --abort

```

## 13.创建标签

创建标签：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git tag v1.0

```

查看标签：

```shell

[root@LAPTOP-1UJN7PP7 git-learn]# git tag
v1.0

```

打之前commit的标签：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git log --graph --pretty=oneline
* b5abcdd2cb9294a98874cd3ab78a212566728dec bug fixed
*   afca374ed3030782154c0b289378286111006643 Merge branch 'dev'
|\
| * 1e70bdc1dee854f049b18e11e0b0971c30fc34e6 Add a line in dev.txt
|/
* 9f34d0def7a71384b3528f9a1056fa1badc9d0c2 Add a line in dev.txt
*   e52bbb2ec0fd79188b47060ebe5ee515c14ac557 conflict fixed
|\
| * 4ecad70e9719d9d5d2e95ca1ef2fc12c7016b4b4 Add a line in feature branch
* | 9a22c75daf397a63a8598094e209f078e0a8a1f7 Add a line in master branch
|/
* 4dfabbce1de291335759960028913e31491ccfce dev branch first commit
* e9442cfef62eada2943e45ac4c2782f2bc54b9b4 Add test.txt
* c2f0b571b6b84becc27440e6670b69b0a9b035a7 Modified a file
* 6e2db9d63f9984e2233a6c3c972b00489f315074 Add a new line
* 061be34209d57299ffe96616f82008ee4655eea0 third commit
* 5e3f973bf0248775995e6197cb999c874abc3859 second commit
* 196ad7fafa50d6558e2c4da42344e7ff292d1447 first commit
[root@LAPTOP-1UJN7PP7 git-learn]# git tag v0.9 afca

```

再次查看标签：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git tag
v0.9
v1.0

```

- 注意，标签不是按时间顺序列出，而是按字母排序的

可以用`git show <tagname>`查看标签信息：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git show v0.9
commit afca374ed3030782154c0b289378286111006643
Merge: 9f34d0d 1e70bdc
Author: root <root@LAPTOP-1UJN7PP7.localdomain>
Date:   Tue Mar 8 12:24:00 2022 +0800

    Merge branch 'dev'


```

还可以创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git tag -a v0.1 196a -m "version 0.1 released"

```

- 注意：标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签

## 14.操作标签

删除标签：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git tag -d v0.1
Deleted tag 'v0.1' (was 63f1df9)

```

- 因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除

推送标签：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git push origin v1.0
Total 0 (delta 0), reused 0 (delta 0)
To git@github.com:zhnny/git-learn.git
 * [new tag]         v1.0 -> v1.0

```

一次性推送全部尚未推送到远程的本地标签：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git push origin --tags
Total 0 (delta 0), reused 0 (delta 0)
To git@github.com:zhnny/git-learn.git
 * [new tag]         show -> show
 * [new tag]         v0.9 -> v0.9

```

删除远程标签就麻烦一点，先从本地删除：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git tag -d show
Deleted tag 'show' (was afca374)

```

然后，从远程删除。删除命令也是push，但是格式如下：

```shell
[root@LAPTOP-1UJN7PP7 git-learn]# git push origin :refs/tags/show
To git@github.com:zhnny/git-learn.git
 - [deleted]         show

```

- 上面这种操作的含义是，将冒号前面的空值推送到远程标签名，从而高效地删除它(参考资料[3])

- 第二种更直观的删除远程标签的方式是：

  ```shell
  git push origin --delete <tagname>
  ```

## 15.配置Git

### 15.1.配置全局信息

配置用户信息：

```shell
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```

配置控制台回显颜色：

```shell
$ git config --global color.ui true
```

### 15.2.配置忽略文件

有些文件我们需要忽略，比如密码等配置文件、编译产生的中间文件等

忽略文件的原则是：

- 忽略操作系统自动生成的文件，比如缩略图等
- 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的`.class`文件
- 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件

Git工作区的根目录下创建一个特殊的`.gitignore`文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件

不需要从头写`.gitignore`文件，GitHub已经为我们准备了各种配置文件，只需要组合一下就可以使用了。所有配置文件可以直接在线浏览：

[github/gitignore: A collection of useful .gitignore templates](https://github.com/github/gitignore)

规则：

```python
# 排除所有.开头的隐藏文件:
.*
# 排除所有.class文件:
*.class

# 不排除.gitignore和App.class:
!.gitignore
!App.class
```

​	强制添加：

```shell
$ git add -f App.class
```

- 值得注意的是，如果忽略了某个文件排除只能排除子文件下的，子文件夹的子文件下是不能排除的

### 15.3.配置别名

配置Git命令的别名：

```shell
$ git config --global alias.<别名>  <命令名>
```



## 16.参考资料

\[1][Git教程 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600)

\[2][git rebase命令 - Git教程™ (yiibai.com)](https://www.yiibai.com/git/git_rebase.html)

\[3][Git - 打标签 (git-scm.com)](https://git-scm.com/book/zh/v2/Git-基础-打标签)