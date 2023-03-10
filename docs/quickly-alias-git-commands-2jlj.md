# 快速别名 Git 命令

> 原文：<https://dev.to/ikey2244/quickly-alias-git-commands-2jlj>

一遍又一遍地输入 Git 命令会变得非常乏味。让我们变得更简单！

使用您自己的别名和相应的 git 命令在您的 shell 中运行这个命令

`git config --global alias.{alias-name} {git command}`

#### 例子:

`git config --global alias.show-remote 'remote show origin'`

*   命令现在是`git show-remote`

而不是:
`git remote show origin`

##### 你甚至可以组合命令！

`git config --global alias.add-commit '!git add -A && git commit'`

*   命令现在是`git add-commit -m 'My commit message'`

而不是:
`git add .` git commit -m '我的提交消息'`吗

化名可以节省你很多时间！我总是拼错 git 命令，但是用别名已经是过去的事情了。

要查看您的所有别名，请运行以下命令:

`nano ~/.gitconfig`

在底部附近，你会看到这样的东西

[![](img/f8a04bc15e7f8d5d5a41079a65466781.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MV__hbhK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jaluetce5mss7kqtsnvv.png)

#### 免责声明

权力越大，责任越大。明智地使用这个命令！在使用别名或组合它们之前，请确保您确切地知道该命令在做什么。

谢谢😁