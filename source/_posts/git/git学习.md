---
title: git学习
date: 2020-09-20 11:25:27
tags:
- git
- 基础巩固
categories:
- git
---


# 参考

git官方文档
**git** 设置个⼈人姓名 **/** 邮箱(全局)

```
1 git config --global user.name ""
2 git config --global user/email ""
```
版本库 = 仓库 repository= ⽬目录，其中的所有的⽂文件都可以被 git 管理理起来，每个⽂文件的修改 / 删除， git 都能跟踪

## 将⽬目录变成 git 可以管理理的仓库

```
1 git init
```

# 版本回退

git reset --hard
HEAD 当前版本
HEAD^ 上⼀一个版本
HEAD^^ 上上个版本
HEAD~n 往上n个版本

reset 回退之后的重新找回操作

1 git reset --hard ${commit_id}
如果不不知道commit_id-使⽤用git reflog查看
git reflog -查看命令历史

git和其他版本控制系统如svn的⼀一个不不同之处就是会有暂存区的概念

Git⽐比其他管理理版本控制系统设计得更更优秀的原因-git跟踪并管理理的是修改，⽽而⾮非⽂文件

# 差异比较

## git diff

**git diff HEAD -- readme.txt** ⽐比较⼯工作区和版本库⾥里里⾯面最新版本的区别

# 清理理并未提交的内容

### 没有提交到暂存区

**git checkout -- `<file>`**

### 已经提交到暂存区
**git reset HEAD `<file>`**


# 删除⽂件

1 git rm `<file>`
恢复误删⽂文件

1 git checkout -- `<file>`
从来没有被添加到版本库就被删除的⽂文件，是⽆无法恢复的

# git reflog

## git reflog- 查看历史版本记录

每⾏行行记录都由版本号（ **commit id SHA** ）， **HEAD** 值和操作描述三部分组成。
版本号在第⼀一列列，HEAD值在第⼆二列列，操作描述信息在第三列列。
版本号：标识着每⼀一次提交、合并等操作时的版本，相当于唯⼀一标识
**HEAD** 值：同样⽤用来标识版本，但是不不同于版本号的是，Head值是相对的。当HEAD值为HEAD时，表示为提交的最新版本；HEAD^表
示为最新版本的上⼀一个版本；HEAD^^表示为最新版本的上上个版本；HEAD~100表示为最新版本的往上第 100 个版本。HEAD值越⼩小，
表示版本越新，越⼤大表示版本⽣生成时间越久。
在上面图中，我们发现HEAD值的展示形式为HEAD@{0}、HEAD@{1}、HEAD@{2}...同样HEAD值的数字越小，表示版本越新，
数字越大表示版本越旧。
操作描述：记录了了本次是哪种操作，以及操作时编写的描述信息。

## git reflog -n 查看历史版本记录 -- 指定显示条数

## 可以救命的 git reflog

当你不不⼩小⼼心reset --hard之后可以通过git reflog看到所有的操作，执⾏行行git reset --hard 命令可以回到之前的状态

# git reset

**--soft** 回退后a分⽀支修改的代码被保留留并标记为add的状态（git status 是绿⾊色的状态）
**--mixed** 重置索引，但不不重置⼯工作树，更更改后的⽂文件标记为未提交（add）的状态。默认操作。
**--hard** 重置索引和⼯工作树，并且 **a** 分⽀支修改的所有⽂文件和中间的提交，没提交的代码都被丢弃了了。
**--merge** 和--hard类似，只不不过如果在执⾏行行reset命令之前你有改动⼀一些⽂文件并且未提交，merge会保留留你的这些修改，hard则不不会。
【注：如果你的这些修改add过或commit过，merge和hard都将删除你的提交】
**--keep** 和--hard类似，执⾏行行reset之前改动⽂文件如果是a分⽀支修改了了的，会提示你修改了了相同的⽂文件，不不能合并。如果不不是a分⽀支修改
的⽂文件，会移除缓存区。git status还是可以看到保持了了这些修改。

## git revert

## git revert操作之后，network中还是可以看到a分⽀支和合并a分⽀支的操作，只不不过在其基础上⼜又增加了了⼀一个revert的commit⽽已

```
1 git revert -m 'msg'
```
# 创建 SSH key (公钥)的命令

```
1 ssh-keygen -t rsa -C "youremail@example.com"
```

# 拷⻉贝⽣生成公钥的命令

```
1 pbcopy < ~/.ssh/id_rsa.pub
```
# git amend ⽤用法

## 修改最后一次提交

## 1.git commit –-amend –-no-edit，不不⽤用重新编辑提交信息

## 2.git commit --amend -m "msg" ,重新编辑提交信息

# 远程仓库

## 先有本地库，后有远程库的时候，关联远程库

1.在github上⾯面创建⼀一个仓库
2.关联远程库，使⽤用命令

```
1 git remote add origin git@github.com:**/learngit.git
```
origin 远程仓库的名字 --git的默认叫法
3.将本地库所有内容推送到远程库上

```
1 git push -u origin master
```
```
第一次推送master分支时，加了-u参数，git会把本地的分支内容推送到远程新的master分支，而且会把本地的master分支和远程的
master分支关联起来
```
## 先创建远程库，然后，从远程库中克隆隆

### 1.⾸首先要拿到仓库地址

2.使⽤用git clone命令克隆隆

```
git支持多种协议，包括https，但ssh协议速度最快
```
# 分⽀支管理理

## 创建与合并分⽀支

git checkout 加上-b参数表示创建并切换，相当于

```
1 git branch dev
2 git checkout dev
```
git merge命令-⽤用于合并指定分⽀支到当前分⽀支
fast-forward-快进模式，也就是直接把master指向dev的当前分支，所以合并速度非常快

## git switch vs git checkout

### 有两个容易混淆的命令

```
1.切换分支使用git checkout <branch>
2.撤销修改则是git checkout --<file>
所以最新版本的git提供了新的git switch 命令来切换分支
```

1.创建并切换到新的dev分⽀支

1 git switch - c dev
2.直接切换到已有的master分⽀支

```
1 git switch master
```
⼩小结：

1. 查看分⽀支：git branch
**2.** 创建分⽀支： **git branch `<name>`
3.** 切换分⽀支： **git checkout `<name>`** 或者 **git switch <name>
4.** 创建 **+** 切换分⽀支： **git checkout -b `<name>`** 或者 **git switch -c `<name>`**
5. 合并某分⽀支到当前分⽀支：git merge `<name>`
6. 删除分⽀支：git branch -d `<name>`
7. 删除某个分⽀支以外的分⽀支
1 git branch | grep -v "master" | xargs git branch -D

# 解决冲突

1. 当Git⽆无法⾃自动合并分⽀支时，就必须⾸首先解决冲突
2. 解决冲突后，再提交，合并完成。

解决冲突就是把Git合并失败的⽂文件⼿手动编辑为我们希望的内容，再提交。
⽤用git log --graph命令可以看到分⽀支合并图。

```
1 git log --graph --pretty=oneline --abbrev-commit
```
### 分⽀支管理理策略略

```
合并分⽀支时，加上--no-ff参数就可以⽤用普通模式合并，合并后的历史有分⽀支，能看出来曾经做过合并，⽽而直接不不使⽤用该参数fast
forward合并就看不不出来曾经做过合并
```
--no-ff⽅方式的git merge
--no-ff参数，表示禁⽤用Fast forward
因为本次合并要创建⼀一个新的commit，所以加上-m参数，把commit描述写进去。

```
1 git merge --no-ff -m "merge with no-ff" dev
```
# BUG 分⽀支

## dev 分⽀支上进⾏行行开发时，需要修复 bug

1.git stash保存⼯工作现场
2.修复bug并提交


**3.git stash list** 命令查看历史

**4. git stash apply vs git stash pop**
    git stash apply
       恢复后，stash内容并不不删除，需要git stash drop来删除
    git stash pop
       恢复同时把stash内容也删除了了

# 多⼈人协作

### 多⼈人协作的⼯工作模式通常是这样：

1. ⾸首先，可以试图⽤用git push origin `<branch-name>`推送⾃自⼰己的修改；
2. 如果推送失败，则因为远程分⽀支⽐比你的本地更更新，需要先⽤用git pull试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再⽤用git push origin `<branch-name>`推送就能成功！

本地分⽀支和远程分⽀支的链接关系没有创建，使⽤用

```
1 git branch --set-upstream-to <branch-name> origin/<branch-name>。
```
创建远程origin的分⽀支到本地&push，使⽤用

```
1 git checkout -b dev origin/dev
2 git push origin dev
```
# rebase （变基）

```
rebase操作可以把本地未push的分叉提交历史整理理成直线；
rebase的⽬目的是使得我们在查看历史提交的变化时更更容易易，因为分叉的提交需要三⽅方对⽐比。
```
# TAG

```
发布⼀一个版本时，我们通常先在版本库中打⼀一个标签（tag），这样，就唯⼀一确定了了打标签时刻的版本。将来⽆无论什什么时候，取某
个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的⼀一个快照。
```
普通打标签（打在最新的⼀一次提交上）

1 git tag v1. 0
查看标签

1 git tag
给对应的commit打标签

1 git tag v0. 9 f52c
查看标签信息

1 git show `<tagname>`
创建带有说明的标签，⽤用-a指定标签名，-m指定说明⽂文字：

```
1 git tag -a v0. 1 - m "version 0.1 released" 1094 adb
```
删除标签

1 git tag -d v0. 1
推送某个标签到远程，使⽤用命令

1 git push origin `<tagname>`
⼀一次性推送全部尚未推送到远程的本地标签


1 git push origin --tags
删除远程标签

```
1 从本地删除
2 git tag -d v0. 9
3 从远程删除。删除命令也是push，但是格式如下
4 git push origin :refs/tags/v0. 9
```


