---
title: git代码回滚终极指南
date: 2018-07-16 20:34:58
tags:
---

# git代码回滚终极指南

## 0 背景

git作为最受欢迎的版本控制工具，被很多开发者所喜欢。撤销回滚操作在git中是非常常用的操作。比如代码上线前需要删除掉其中的一个commit，排查线上代码需要临时增加一些log点，之后需要回退到之前的代码，针对不同的需求场景，需要使用不同的命令。本文从具体的实例出发，详细分析了不同的命令在不同场景下的用处。

## 1 准备工作

### 准备一个测试目录

```bash
mkdir git_exp
```

### git初始化

```bash
git init
```

### 准备测试内容

为了使我们的例子更加的简单易懂，我在master分支添加一个a.txt文件，并且提交四次修改。分别增加字母abcd，此时文件内容为：

```plain
a
b
c
d
```

git log如图：
![git log](http://owgxabs76.bkt.clouddn.com/WX20180716-210813.png)

### 推到远程仓库进行备份

+ 关联远程仓库

```bash
git add remote origin git@github.com:zhaipeihan/git_exp.git
```

+ 推送到远程仓库

```bash
git push origin master
```

## 2 git reset

git reset命令用于撤销commit，回退到某个版本上。

我们在之前master的基础上，创建reset_test分支进行测试

```bash
git checkout -b reset_test
```

我们现在的git log为：

```plain
d 75578cd7ae1a222c5fa745a0d2735a3448275a27
c 076a714bed33fce4fa044b18b79f06f180ff3d97
b d1e4634545ae8228556158734dc39e4783ec2d94
a 739fe11e522ffca6c49061c9927e074d5ba3cbbd
```

git reset命令常用参数有三个，影响程度从小到大为 --soft --mixed --hard
我们依次使用这三个命令来恢复到 b这个commit上

### --soft

```bash
git reset --soft d1e4634545ae8228556158734dc39e4783ec2d94
```

此时git log为：
![](http://owgxabs76.bkt.clouddn.com/WX20180716-221456.png)

git status:
![](http://owgxabs76.bkt.clouddn.com/WX20180716-222012.png)

文件内容：
![](http://owgxabs76.bkt.clouddn.com/WX20180716-221943.png)

--soft参数回退到版本b的提交状态上了，但是保留了之后修改文件内容，只是未提交的状态

### --mixed

我们使用之前的master分支来新建一个分支恢复到初始状态来测试--mixed

我们执行：

```bash
git reset --mixed d1e4634545ae8228556158734dc39e4783ec2d94
```

执行之后的状态：
![](http://owgxabs76.bkt.clouddn.com/WX20180716-223008.png)

--mixed参数同样恢复到版本b提交状态上了，也同样保留了之后修改的文件内容，与--soft不同的是，--mixed还没有提交到缓存区。

### --hard

我们使用之前的master分支来新建一个分支恢复到初始状态来测试--hard

我们执行：

```bash
git reset --hard d1e4634545ae8228556158734dc39e4783ec2d94
```

执行之后的状态：
![](http://owgxabs76.bkt.clouddn.com/WX20180716-223514.png)

--hard同样也恢复到了版本b的提交状态上了，但是--hard丢弃了工作区和缓存区的所有内容，所以之后修改的文件内容并没有保存，使用--hard需要慎重，确定不需要保留之后的修改内容之后再使用。

### 小节

git reset 命令用来回退到某个版本上，其常用的--soft --mixed --hard三个参数对应了不同的修改场景，需要根据具体场景来选择。git reset是一个“危险的”操作，它可能会导致你永久的丢失提交记录。需要注意的是，本地仓库可以用reset来进行回退;如果commit已经push到自己的远程仓库，在reset回退之后，需要git push -f强制覆盖远程分支，否则因为缺少了后面的commit会造成冲突；如果是多人共享的公司远程仓库，建议不要使用reset进行回退版本，因为你回退了版本，会和别人的本地或者他人自己的远程仓库产生冲突，造成不必要的麻烦，如果需要回退共享仓库的版本应该使用下边分析的git revert。

## 3 git revert

git reset是直接删除了需要撤销的操作，这是比较危险的操作，git revert是在当前提交之后自动生成一个逆向的提交来达到回滚的效果。

我们在前面小节的基础上增加b.txt c.txt d.txt三个文件分别进行3次提交，现在的项目结构为：

![](http://owgxabs76.bkt.clouddn.com/WX20180717-134819@2x.png)

git log记录为：
![](http://owgxabs76.bkt.clouddn.com/WX20180717-135001@2x.png)

接下来我们使用git revert来撤销add b file这个提交。

```bash
git revert 3a1252d048bdd12cae8f1668584bc196428a1072
```

执行后的目录结构：

![](http://owgxabs76.bkt.clouddn.com/QQ20180717-195444@2x.png)
执行后的git log记录：
![](http://owgxabs76.bkt.clouddn.com/QQ20180717-195428@2x.png)

通过结果可以看到add b file这个操作已经被撤销，b.txt这个文件已经消失，并且commit历史中新增了一个commit。

git revert [commit1] [commit2] 可以撤销commit1到commit2之间的所有的提交

### 小结

git revert命令是在当前的节点上向后生成了新的commit来撤销之前的操作，之前的commit并不会被删除，如果要撤销已经push到远程仓库的commit建议使用git revert,这是一个安全的操作，在后续的合并分支时不会产生冲突。

## 3 git rebase

git rebase用来重新编排所有的commit,此处可以用来撤销某个commit。我们此处还是以上一小节的例子来说明git rebase。

目前的项目结构和git log如上一小节执行git revert之后的状态所示。

此时我们进行一个撤销add c file的操作。

```bash
git rebase -i 3a1252d048bdd12cae8f1668584bc196428a1072
```

注意使用git rebase 输入的commit版本号是想要撤销的commit的上一个commit号

执行该命令后，会出现一个类似交互式的界面，如图：

![](http://owgxabs76.bkt.clouddn.com/QQ20180717-201421@2x.png)

我们将想要删除的commit改为 drop 然后保存退出。

执行之后的目录结果为：
![](http://owgxabs76.bkt.clouddn.com/QQ20180717-201625@2x.png)

add c file的操作已经被撤销，git log 如图：
![](http://owgxabs76.bkt.clouddn.com/QQ20180717-201733@2x.png)

我们可以看到 add c file的操作已经直接被撤销了，并且没有生成新的提交记录。

### 小结

利用git rebase可以达到和git revert相同的效果，主要区别在于git rebase不会生成新的commit。

## 4 git checkout
以上提到的都是提交层面的撤销操作，如果是想对未add到缓存区的内容做撤销操作，使用git checkout [filename]

如果已经add到缓存区，使用git checkout 并没有效果。此时可以使用git reset来回溯到上一个提交点。

## 5 总结

以上我们分析了每一个命令对应的不同的撤销的场景，简单来说，改动未add到缓存区可以使用git checkout来放弃修改；已经提交commit，但是commit未push到远程仓库，可以使用reset来回到某个commit，如果是想删除某个特定的commit,可以使用rebase来操作；如果commit已经推送到远程仓库，建议使用git revert来撤销其中的操作。