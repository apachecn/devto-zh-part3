# 如何删除不在远程上的本地分支？

> 原文：<https://dev.to/rafaelcg/how-to-remove-local-branches-that-aren-t-on-remote-26k1>

当您想要将本地存储库分支与远程分支同步时，这是最理想的方法。

# 同步你的树枝

你只需要在**获取**命令期间使用选项`-p`或`--prune`。

```
git fetch --prune 
```

Enter fullscreen mode Exit fullscreen mode

> -p，- prune
> 获取后，删除远程服务器上不再存在的任何远程跟踪分支。你也可以检查[修剪选项](https://git-scm.com/docs/git-fetch#git-fetch---prune)。

# 清理你的局部

如果你想同步的不仅仅是分支，你也可以同步当前的分支，只需要使用下面的命令。

```
git pull --all
git reset --hard
git clean -df 
```

Enter fullscreen mode Exit fullscreen mode

# 谢谢

感谢你阅读这篇文章，我希望它对你有用。如果你使用不同的策略，请告诉我！