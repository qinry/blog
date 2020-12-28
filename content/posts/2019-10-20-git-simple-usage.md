---
title: "git入门使用"
date: 2019-10-20T08:50:01+08:00
categories: "Git"
tags: ["Git"]
---

## 配置Git

提供Git用户名和邮箱

> $ git config --global user.name "username"

> $ git config --global user.email "usernaem@example.com"

## 忽略文件

创建文件 `.gitignore` ，让Git忽略某目录中的所有文件(不跟踪这些文件)，使用它可以避免项目混乱

## 初始化仓库

> $ git init


仓库是程序中被Git主动跟踪的一组文件。Git用来管理仓库的文件都存储在隐藏的.git/中，你根本不需要与这个目录打交道，但千万不要删除这个目录，否则将丢弃项目的所有历史记录。

## 检查状态

> $ git status

在Git中，分支 是项目的一个版本。提交 是项目在特定时间点的快照。

## 文件添加到仓库

> $ git add [< file > | < path >]

只是让Git开始关注指定文件或目录中的文件。但未提交。

## 执行提交

> $ git commit -m < message >

标志-m 让Git将接下来的消息记录到项目的历史记录中

> $ git commit -am < message >

标志-am中 a 让Git将仓库中所有修改了的文件都加入到当前提交中，m同上是提交记录信息

保证最后的工作目录是干净的，否则很有可能忘记添加文件

## 查看提交历史

> $ git log

每次提交时，Git都会生成一个包含40字符的独一无二的引用ID。它记录提交是谁执行的、提交的时间以及提交时指定的消息。并非在任何情况下你都需要所有这些信息，因此
Git提供了一个选项，让你能够打印提交历史条目的更简单的版本：

> $ git log --pretty=oneline

将显示提交的引用ID及提交记录的信息

## 撤销修改

> $ git  checkout - - < file >

命令`git checkout` 让你能够恢复到以前的任何提交。命令`git checkout .`放弃自最后一次提交后所做的所有修改，将项目恢复到最后一次提交的状态。

## 检出以前的提交

> $ git checkout [< id > | < master >]

可以检出提交历史中的任何提交，而不仅仅是最后一次提交，为此可在命令git checkout 末尾指定该提交的引用ID的前6个字符（而不是句点）。还可以从之前的提交回到 **master**

除非你要使用Git的高级功能，否则在检出以前的提交后，最好不要对项目做任何修改。

> $ git reset --hard < id >

永久地恢复到引用ID是此前6个字符的提交。

## 删除仓库

mac/linux
> $ rm -rf .git

window
> $ rmdir /s .git

仓库的历史记录被你搞乱了，而你又不知道如何恢复。如果无法恢复且参与项目开发的只有一个人，可继续使用这些文件，但要将项目的历史记录删除——删除目录.git。这不会影响任何文件的当前状态，而只会删除所有的提交。
