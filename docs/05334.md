# Git 删除本地分支如何删除所有本地分支，但保留主分支

> 原文：<https://dev.to/ben/how-to-delete-all-your-local-branches-but-keep-master-lb1>

我发现自己经常搜索这个 git 一行程序，所以我想我应该把它放在这里，以帮助未来的搜索者:

```
git branch | grep -v "master" | xargs git branch -D 
```

Enter fullscreen mode Exit fullscreen mode

快乐编码