# 化名 git way

> 原文：<https://dev.to/konstantin/aliases-git-way-1kjp>

我经常看到开发人员在推特上发布他们的 git 别名，大多数时候他们都在使用外壳的标准别名功能。有点像:
`alias gs = git status`

实际上为你提供了为一些 git 命令创建别名甚至创建你自己的命令的能力。你可以在你的`.gitconfig`文件的`[alias]`部分完成(可以在你用户的主文件夹中找到)。

## 别名

举个例子，如果我们举前一个例子，你可以这样写:

```
[alias]
  s = status 
```

Enter fullscreen mode Exit fullscreen mode

您将能够如下使用它:
`git s`

现在你可能会说它比 gs 长得多。但是您也可以在您的 shell 中为 git 本身创建一个别名:

```
 alias g = git 
```

Enter fullscreen mode Exit fullscreen mode

所以现在你可以通过输入`g s`来调用你的别名。

## 自定义命令

除了别名，你实际上可以创建一些非常酷的自定义命令。例如，我们可以添加一个别名来查看您已经签出的最后 10 个分支:

```
[alias]
  recent = for-each-ref --count=10 --sort=-committerdate refs/heads/ --format="%(refname:short)" 
```

Enter fullscreen mode Exit fullscreen mode

这是我无耻地从[CSS wizardy 的文章](https://csswizardry.com/2017/05/little-things-i-like-to-do-with-git/)中摘出来的。当你运行它(`g recent`)时，你会看到你最近的 10 个分支的列表(或者更少，如果你没有检查出 10 个)。

## 在 git 别名内使用 shell

您还可以在 git 别名:
中进行 shell 评估

```
[alias]
  compare = "!f() { git log --oneline $1..$2; }; f" 
```

Enter fullscreen mode Exit fullscreen mode

这里我们定义了一个别名，它包含一个可以直接调用的匿名函数。我们也可以将参数传递给这个函数。在这个例子中，我们传递了两个修订或分支，这样我们就可以得到一个与它们不同的提交列表。

您可以如下使用此别名:
`g compare branch1 branch2`

## 更多的东西

你可以从我的[点文件回购](https://github.com/gko/dotfiles)的 [gitconfig 文件](https://github.com/gko/dotfiles/blob/master/.gitconfig)中看到上面的例子和其他一些很酷的东西。