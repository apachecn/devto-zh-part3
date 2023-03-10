# 在大规模 Git 回购中找到一个字符串

> 原文：<https://dev.to/dougblackjr/find-a-string-in-a-massive-git-repo-22h9>

我想我会分享这个，因为我只是需要这样做。

我们正在为一个 [Engauge Analytics](https://engaugeanalytics.com/) 项目进行大规模的 git 回购。这些分支中有大量正在进行的活动工作，有些只是在修剪过程中被忽略了。

我需要找到 10 个角色。

从技术上讲，我需要找到包含这 10 个字符的方法，但我知道这 10 个字符将是找到它的关键。

假设您正在 repo 中的许多 git 分支之一中寻找一个字符串标题`the magic string`。可以使用:

```
git grep "the magic string" `git show-ref --heads` 
```

Enter fullscreen mode Exit fullscreen mode

如果字符串存在于你的回购协议中，任何地方，你都会得到这样的东西:

```
***c5cd1d8:path/to/file.js:let a = "the magic string" 
```

Enter fullscreen mode Exit fullscreen mode

一旦有了这些数据，就像找到分行一样容易！

```
git branch --contains ***c5cd1d8 
```

Enter fullscreen mode Exit fullscreen mode

可能有更简单的方法来做到这一点。你推荐什么？