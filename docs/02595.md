# 如何计算两次提交散列之间的提交次数

> 原文：<https://dev.to/espoir/how-to-count-the-number-of-commits-made-between-2-commits-hashes-1aoa>

### 问题

你已经为这个功能努力了很多天，现在终于完成了。不幸是，您已经提交了许多提交，您想将它们压缩成一个提交并提出拉请求。

正如本教程中所说，你可以用一个命令来完成:

`git rebase -i HEAD~[NUMBER OF COMMITS]`

但是如何知道从你开始使用这个特性的那一刻起你已经提交了多少次呢？

### 解

使用`git log`,您可以在创建分支后列出所有提交。

假设您的第一个提交哈希是`33b14c62b`，最后一个提交哈希是`33ad6cecf`

您可以通过手工计算两个哈希之间的提交次数来实现，但这并不总是容易的。

Git 有一个名为`rev-list`的强大命令，它按时间倒序列出提交对象

`git rev-list 33b14c62b...33ad6cecf`

使用该命令，您可以获得开始哈希和结束哈希之间所有提交列表。

要计算该命令输出中的行数，请使用管道和下面的命令:`| wc -l`。

最终的命令将如下所示:

`git rev-list 33b14c62b...33ad6cecf | wc -l`

### 把所有的放在一起！

一旦您获得了想要压缩的提交数量，您就可以执行简介中提到的命令，并通过决定您想要挑选的内容和想要压缩的内容来组合您的提交。
如果发现 10 次提交，命令将是:

`git rebase -i HEAD~10`

### 合二为一:

您可以将这两个命令合并成一个命令，并使用以下命令删除空格和制表符:

`git rebase -i HEAD~"$(git rev-list 33b14c62b...33ad6cecf | wc -l | sed 's/ //g')"`

特别感谢 [@paesibassi](https://dev.to/paesibassi) 的这个建议

问候。

使用的资源:

[关于 stackoverflow 的一个回答](https://stackoverflow.com/a/44344164/4683950)

[git 圣经中的 git rev-list](https://git-scm.com/docs/git-rev-list/#git-rev-list---ancestry-path)