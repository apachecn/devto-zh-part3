# Git 用户和他们的最后提交

> 原文：<https://dev.to/bearcherian/git-users-and-their-last-commits-2gpi>

我需要记住去年谁和我一起在某个项目上工作。我不太记得了，因为去年对我个人来说太疯狂了，而且这个项目重叠了好几年。所有的年、月、日都在我的脑子里一起运转。

幸运的是，我们有 git 日志，但这并不简单。我想出了这组命令来帮助完成这个任务:

```
git log --pretty=format:"%an" | sort | uniq | awk '{system("git log -1 --author="$1 " --pretty=format:\"%ad%x09%an\""); print "\r"}' | sort -k5n -k2M -k3n 
```

我们正在做的是:

1.  获取 git 日志中的所有用户名
2.  按词汇排序
3.  删除重复项(sort -u 可以替换这些重复项)
4.  为每个用户做另一个 git 日志，但是打印出他们的最后一次提交，并将其格式化为日期和他们的名字，用制表符分隔。
5.  按列排序(年、月、日)