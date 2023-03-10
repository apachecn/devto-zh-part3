# TIL:前面的空格防止危险的命令进入历史

> 原文：<https://dev.to/stefanjudis/til-a-preceding-space-prevents-dangerous-commands-from-going-into-history-5e02>

阿西姆·侯赛因在推特上分享了他错误地执行了一个危险的命令。这个命令在他的 shell 历史中仍然可以访问，他按向上箭头的次数太多了。

我经历过那种情况，你也可能经历过。🙈

我喜欢 Twitter，有时人们会回复有用的提示，以避免未来的错误。菲利普·马丁也是如此。他分享说，用空格执行的**命令不会进入会话历史。**听起来很棒！

```
# command goes into the history
$ delete everything

# command does not go into the history
$  delete everything 
```

Enter fullscreen mode Exit fullscreen mode

我马上试了试，但是没有效果。我是一个 [Zsh](http://www.zsh.org/) 用户，事实证明你必须通过你的`.zshrc`中的配置来启用它。

```
setopt histignorespace 
```

Enter fullscreen mode Exit fullscreen mode

在 bash 中，它应该立即工作(未经测试)。

不过有一件事要提一下。在执行一个命令后，每个命令都可以通过按向上箭头来访问(这是一个特性)。只有当您执行另一个命令之前的空间命令将无法访问。

这个小技巧可以防止你未来的自己在过多按下向上箭头时执行危险的命令。🎉