# Git 添加子模块

> 原文：<https://dev.to/serhatteker/git-adding-submodule-3a78>

### 向 Git 仓库添加子模块

向 git 存储库添加子模块实际上非常简单。例如，如果你在储存库的工作目录中，添加一个新的子模块:

```
$ git submodule add <git url> 
```

Enter fullscreen mode Exit fullscreen mode

*   这只是告诉 Git 我们正在添加一个子模块。这种语法将始终保持不变。
*   `<git url>`–这是要作为子模块添加的外部存储库。

如果你想对`add`功能进行更多的控制:

```
$ git submodule add -b <branch> <git url> <folder/subfolder> 
```

Enter fullscreen mode Exit fullscreen mode

*   `<branch>`–从您的`default`分支获取不同的分支，例如`master`
*   `<folder/subfolder>`–这将您的子模块添加到另一个子文件夹中

至`activate`子模块:

```
$   git submodule init 
```

Enter fullscreen mode Exit fullscreen mode