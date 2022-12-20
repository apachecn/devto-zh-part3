# 如何删除合并到当前分支的所有本地 git 分支

> 原文：<https://dev.to/aaronpowell/how-to-delete-all-local-git-branches-merged-into-the-current-branch-4n73>

我最近看到 [Ben Halpern 的](https://dev.to/ben)关于[如何删除所有本地 git 分支](https://dev.to/ben/how-to-delete-all-your-local-branches-but-keep-master-lb1)的帖子，我想分享一个有用的片段，作为删除所有**合并**分支
的别名

```
[alias]
    rmb = "!f() { git branch --merged | grep -v "master" | while read i; do git branch -d $i; done; }; f" 
```

Enter fullscreen mode Exit fullscreen mode

`rmb`是*移除合并的分支*的简称，它会检查所有已经合并到你当前所在分支的分支并移除它们。

我将经常在`master`上运行`git rmb`来进行清理，但是它也可以在任何有子分支的分支上进行。😊