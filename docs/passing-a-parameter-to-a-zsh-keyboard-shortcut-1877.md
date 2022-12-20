# 向 zsh 键盘快捷键传递参数

> 原文：<https://dev.to/ecologic/passing-a-parameter-to-a-zsh-keyboard-shortcut-1877>

## [T1】简介](#intro)

这篇文章是我上一篇文章[的续篇，使用 zsh](https://dev.to/ecologic/using-keyboard-shortcuts-with-zsh-16b) 的快捷键。如前所述，我将展示如何将参数/字符串传递给 zsh 键盘快捷键。

例如，我们可以使用此选项通过执行以下操作来创建 git 特性分支:

`[typing the name of the branch] --> [keyboard-shortcut] --> [done]`

[![](img/798dd8820799fff2690604b14949f29e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DjjEmEZS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gg2sr2wikqwij8nz5mx3.gif)

## 给我看看代码！

```
# create a feature branch with <name> and then ctrl-g+f
function featbranch() {
    branch_name=$BUFFER
    zle backward-kill-line
    echo "git checkout -b feat/$branch_name"
    git checkout -b "feat/${branch_name}"
    zle reset-prompt
    zle redisplay
}
zle -N featbranch
bindkey '^gf' featbranch 
```

比我们之前的小部件长一点。让我们一行一行地分解它:

1.  另一个很棒的评论
2.  我们的按键将执行的功能:
3.  将当前终端线保存到变量中
4.  清除当前行
5.  显示我们想要执行的完整命令
6.  执行命令
7.  重置提示。(如果我们在 promt 中显示分支信息，这很有用)
8.  重新显示我们的提示。
9.  我们再次定义一个指向该函数的小部件
10.  并将我们的组合键绑定到小部件

所以我们有它。让我知道它是否对你有用！

## 让我们连接！

我希望与其他技术爱好者一起拓展我的网络。让我们在这里或在 twitter 上联系吧！[👋@langhard](https://twitter.com/langhard)

## 资源

[Z-Shell 用户指南](http://zsh.sourceforge.net/Guide/zshguide04.html)