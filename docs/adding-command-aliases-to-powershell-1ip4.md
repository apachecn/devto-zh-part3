# 向 PowerShell 添加命令别名

> 原文：<https://dev.to/michaeljolley/adding-command-aliases-to-powershell-1ip4>

如果你像我一样，有一些命令会在你的一天中反复运行。在`git checkout`、`docker {whatever}`和使用`cd`导航到频繁路径之间，我一直在想通过缩短这些命令和参数我能节省多少时间。

我实际上有点嫉妒我的朋友使用 bash 和他们漂亮的别名，所以我去寻找一种在 PowerShell 中使用别名的方法。原来，真的很简单！

## 这个座位有人了

PowerShell 内置了我们不想踩的命令。我不会把它们都列出来(有很多)，但是如果你从你的别名中得到了一些意想不到的东西，那么你可以谷歌一下。

## 所以已经给我看了

好吧好吧。首先，让我们在一个`.ps1`文件中创建一个函数。

```
function goGoGadgetGitStatus {
  git status
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的函数简单的调用`git status`。然后，您可以设置一个别名来调用该函数。

```
Set-Alias gs goGoGadgetGitStatus 
```

Enter fullscreen mode Exit fullscreen mode

然后从 PowerShell 控制台输入`gs`，它就会运行`git status`。

这是一个非常简单的例子，但给你一个什么是可能的想法。

## 参数呢

很棒的问题！我们可以在函数中定义参数，然后将它们传入。

```
function goGoGadgetGit {
  Param(
    [Parameter(Mandatory = $true, Position = 0)]
    [String]
    $Cmd,

    [Parameter(Mandatory = $false, ValueFromRemainingArguments = $true)]
    [String[]]
    $Params
  )

  Switch ($Cmd) 
  {
    # status
    's' { git status $Params }
    # branch
    'c' { git checkout $Params }
  }
}
Set-Alias g goGoGadgetGit 
```

Enter fullscreen mode Exit fullscreen mode

现在在我们的控制台中，我们可以调用`g s`来得到`git status`或`g c master`来执行`git checkout master`。

关于如何编写 PowerShell 函数或参数选项，我就不赘述了。对于这两者来说，有大量的资源可以利用。但是希望这向您展示了为 PowerShell 设置命令别名的基本方法。只需将代码添加到 PowerShell profile.ps1，您就可以开始比赛了。

## PowerShell 别名

实际上，我已经在 https://github.com/MichaelJolley/ps-alias 创建了一个存储库，其中有我在个人资料中包含的 PowerShell 脚本，它为 Docker、Git 等提供了许多别名。随意叉& PR 任何你认为可能帮助别人的化名。

有其他建议/修正吗？请在下方留言评论。