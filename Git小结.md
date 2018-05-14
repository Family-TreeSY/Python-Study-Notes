---
title: Git学习小结
date: 2017-12-1 20:08:08
tags:
- Git
- GitHub
---
前段时间学习了廖雪峰老师的[Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013743256916071d599b3aed534aaab22a0db6c4e07fd0000)，今天再来温故下，加深下记忆！

创建文件夹、文件
> mkdir gittest

> touch gittest.txt


**创建版本库**
> git init


编辑文本文件gittest.txt
> Git is a version control system.
Git is free software.


把文件添加到版本库
> git add gittest.txt

**提交请求**，-m表示提交的信息

> git commit -m '第一次提交'

再次编辑gittest.txt
> Git is a distributed version control system.
Git is free software distributed under the GPL.

再次提交到版本库，重复上面git add git commit指令

**查看git 日志**，看看之前提交了什么(有学到了一个新命令)

> git log 

返回的数据掩护缭乱，那就来个简洁的,只有简单的两行数据

> git log --pretty=oneline


**返回上一个版本**(HEAD表示当前版本，HEAD^表示上一个版本)

> git reset --hard HEAD^

**再次返回上一个版本** 后面那一串数字是版本号

> git reset --hard 6f5a174c09469994c2dc59cf6db87ac7012d16c4


我们再来看一下日志最新的请求又出现了


查看命令历史

> git reflog


**git操作分两步**
1、git add file 添加文件到版本库就是添加到暂存区
2、git commit 提交请求就是把暂存区的内容提交给master分支


**查看工作区和暂存区版本的区别**

> git diff HEAD --gittest.txt


修改文件后，再添加到版本库之前(**没有git add)**，想撤销掉这次操作

> git checkout -- gittest.txt

这一次已经将文件添加到了暂存区(git add file)，可以这样撤销操作

> git reset HEAD gittest.txt

这样我们就从暂存区返回了工作区，再把工作区的操作撤回就行了

**删除文件**

删除文件之前新建一个test.txt，再添加到暂存区并提交到分支

>git rm test.txt


> git commit -m '删除test.txt'

如果是误删的话，返回上一个操作
> git checkout test.txt


**添加远程仓库**

> git remote add origin git@github.com:Family-TreeSY/git_test.git

**将本地库的内容推送到远程**

> git push -u origin master

**从远程库克隆**
需要先在github上面创建一个仓库

> git clone git@github.com:Family-TreeSY/B-2.git


**分支管理**

创建分支，这个之后HEADz指向dev,，-b表示创建并切换分支

> git checkout -b dev

这条命令相当于下面两条
> git branch dev 创建dev分支
> git checkout dev 切换到dev分支

查看当前分支

> git branch


把dev分支合并到master分支
> git merge dev


删除分支

> git branch -d dev

查看分支合并图
> git log --graph --pretty=oneline --abbrev-commit


分支管理策略

> git merge --no-ff -m "merge with no-ff" dev

**--no-ff:** 表示禁用Fast-forward，使用--no-ff 可以看到合并后的历史有分支
这次创建会提交一个commit 所以带有-m参数


**BUG分支**
把当前工作藏起来，等恢复现场后再来工作

> git stash

创建bug分支修改bug流程

假如需要在master分支上修复bug，那就在master上创建分支
git checkout -b issue-101-------->修改gittest.txt-------->git add gittest.txt --------->git commit -m '修复bug'------------> 切换回master；git checkout master ------------->合并分支;git merge --no-ff -m "合并分支" issue-101---------->删除issue-101分支; git branch -d issue-101

> git checkout -b issue-101

修复bug的工作结束了，我们现在重新回到dev分支工作,，查看刚刚的工作现场

> git checkout dev

查看刚刚被隐藏的工作现场
> git stash list

回到工作现场
> git stash pop


**feature分支**

强行删除
> git branch -D feature-vulcan

**多人协作**
查看远程库信息
> git remote -v
推送分支
> git push origin master

如果是推送dev分支
> git push origin dev

多人协作模式
先推送自己的：git push origin ... ----------> 推送失败，远程库比本地新需要：git pull 合并------->

如果有冲突就解决冲突---------->没有冲突或解决冲突后: git push origin 分支


**标签管理**

首先切换到要打标签的分支
> git checkout master

打标签

> git tag v1.0

查看标签

> git tag

默认标签是打在最新的commit上的，我们想在其他commit上打标签就需要查看ID号
> git log --pretty=oneline --abbrev-commit

打在指定commit ID上面

> git tag v0.9 1555a84

查看标签信息

> git show v0.9


创建带有说明的标签，用-a指定标签名，-m指定说明文字

> git tag -a v0.1 -m '指定标签练习' c839d68

删除标签

> git tag -d v0.1

推送指定标签daoyuancheng

> git push origin v0.9

推送所有标签到远程

> git push origin --tags


标签已经推送到远程后再删除，先删除本地标签，再删除远程仓库标签

> git tag -d v1.0

> git push origin :refs/tags/v1.0




欢迎访问[Treehl的博客](https://family-treesy.github.io/)

[GitHub](https://github.com/Family-TreeSY)




















