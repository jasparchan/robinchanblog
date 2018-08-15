---
title: Git 常用命令
date: 2017-09-14 14:46:14
tags: Git
---
> Git是目前世界上最先进的分布式版本控制系统（没有之一）。

![](http://image.robinchan.cn/git.jpeg)
从SVN来到Git的世界，谢谢[廖雪峰的Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)，让我更加深刻了解Git。

### Git本地管理 
git init ：创建版本库（当前目录多了一个`.git`的隐藏目录）

git status ： 随时掌握工作区的状态

git diff ： 可以查看这个这个文件修改内容。

git add ： 把文件修改添加到暂存区

git commit ： 把暂存区的所有内容提交到当前分支

git log ： 查看从最近到最远的提交日志（--pretty=oneline）

git reflog ： 查看命令历史记录（一个记录都有一个commit id）

gitignore文件：git需要忽略提交的文件名或文件夹

### Git时光机
> Git是一把打开时光隧道的钥匙
> 妈妈再也不用担心文件备份或者丢失的问题

git reset –hard HEAD^ ： 回退到上个版本（上上个版本HEAD^^，类推），HEAD^可以用commit id代替，回退到具体版本

git checkout –- file ：把readme.txt文件在工作区的修改全部撤销，

git rm file&git commit -m “delete file” ：删除已添加到版本库的文件

git checkout ：一键还原，慎用，你可能会丢失最近一次提交后你修改的内容


### 创建和合并分支
git branch ： 查看分支

git branch <name> ： 创建分支

git checkout <name> ： 切换分支

git checkout -b <name> ： 创建+切换分支

git merge <name> ： 合并某分支到当前分支

git branch -d <name> ： 删除分支

git branch -D <name> ： 强行删除分支


### 解决冲突
合并分支或多人开发时，偶尔会出现冲突，这时就需要手动修改
VS Code很赞，我一般用这个解决冲突。
![](http://image.robinchan.cn/vccodechongtu.jpg)

