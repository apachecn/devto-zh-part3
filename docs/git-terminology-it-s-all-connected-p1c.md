# Git 术语——都是联系在一起的

> 原文：<https://dev.to/creeland/git-terminology-it-s-all-connected-p1c>

# Git 术语

Git 有一些每个人都使用的花哨的行话，但这一切意味着什么，它是如何连接的？我们来通俗地分解一下！👨‍🏫

## 变化

Git 跟踪项目中的所有文件。每当您对一个文件进行更改时，git 都会准确地知道您更改了什么并跟踪它。

## 提交

一个*提交*是一个**的变更集合**。无论何时提交，你都是在告诉 git 你希望它保存你所做的所有更改。

## 分支

一个*分支*只是一个**提交序列**，所以你所做的所有变更都是从项目的开始开始。

每当你分支时，你就脱离了当前的提交。**其他分支不会跟踪你在一个单独的分支上所做的提交**，这允许你在不影响其他人的情况下做出改变！

## 储存库

一个*存储库*，也被称为*回购*，是分支的集合。这是整个项目，每个分支上的每个提交。

## 遥控

远程存储库不是您当前所在的存储库。你可以连接一个遥控器来推拉。

## 推送

当您*推送时，*您**将您的提交**从您计算机上的分支上传到**远程**存储库的分支

## 拉

当您*拉动时，*您**将提交内容**从远程存储库的分支下载到您计算机的分支上的**存储库。**

## 合并

*合并*就是你**同步**两个分支的提交。

一个*合并冲突*是当两个提交改变相同的东西！

## 🐙开源代码库

GitHub 是一个托管远程存储库的网站！

## 克隆

*克隆*是远程存储库的副本。如果您没有权限，则无法将更改推送到遥控器。

## 叉

一个 *fork* 是你名下 Github 上一个库的克隆。您可以将更改推送到分叉的存储库中！