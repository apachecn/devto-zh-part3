# Au revoir vim:使用 VS 代码在云中工作

> 原文：<https://dev.to/fwuensche/say-goodbye-to-vim-work-in-the-cloud-using-vs-code-2jdm>

**TL；DR.** 使用`go get -u go.coder.com/sshcode`安装 sshcode，并使用`sshcode kyle@dev.kwc.io` ^^ voilà连接到您的远程虚拟机。

* * *

# 动机

在云中设置开发环境有几个原因:

*   从任何设备访问您的开发环境
*   惊人的快速互联网连接
*   在保证本地机器安全的同时尝试新技术
*   通过将快照共享到您的虚拟机，轻松搭载新队友
*   让你的笔记本电池持续更长时间
*   买一个 200 美元的 Chromebook，开始用几乎零设置进行编码

# 上下文

到目前为止，为了 100%在云中工作，您必须创建一个虚拟机并从终端 ssh 到其中。然后，您必须使用 vim 作为您的主要文本编辑器，也许 tmux 用于管理您的终端会话和窗口。它起作用了，但是你别无选择，只能使用 vim。

如今，随着构建在 NodeJS 之上的现代文本编辑器的出现，您可以在浏览器中运行一个完整的类似本机的应用程序。而这正是我今天要向大家介绍的。

在 coder.com 惊人团队**为您带来的两个解决方案`code-server`和`sshcode`的帮助下，您现在可以使用 VS 代码**在云中轻松编码。

# 入门

#### 你需要什么

*   谷歌浏览器(本地安装)
*   Visual Studio 代码(本地安装)
*   远程虚拟机:我强烈推荐数字海洋

#### 分步安装(Mac)

在[主项目回购](https://github.com/codercom/sshcode)中的说明非常简单，所以我在这里详细说明。

```
# install sshcode using go
brew install go
go get -u go.coder.com/sshcode

# add the binary to your path
# in case you're using zsh, for instance:
echo "export PATH=\$PATH:~/go/bin" >> ~/.zshrc

# open a new terminal and launch vscode
sshcode kyle@dev.kwc.io 
```

Enter fullscreen mode Exit fullscreen mode

如果你安装了 Chrome，它会在应用模式下打开浏览器。这意味着没有键绑定冲突，没有地址栏，也没有在浏览器中编码的迹象。感觉就像原生 VS 代码。

[![sshcode demo gif](img/0bf551df597f8b32e374180caf89881b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--msD7HUFm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/codercom/sshcode/master/demo.gif)

# 额外招数

#### 打开一个具体项目

您可以指定一个远程目录作为第二个参数:
`sshcode kyle@dev.kwc.io /root/www/doctolib`

#### 跳过扩展名同步

默认情况下，每次连接时，sshcode 都会将您的本地 VS 代码设置和扩展同步到远程服务器。此操作在慢速连接时可能需要一段时间，但在后续连接到同一服务器时会很快。要完全禁用此功能，请传递`--skipsync`标志。

#### 同步返回

默认情况下，当连接关闭时，远程服务器上的 VS 代码更改不会同步回来。要在连接结束时同步回本地，请传递-b 标志。

# 弊端

*   你会发现有时会有一些延迟，这取决于互联网连接
*   既然你在云中，你应该更加注意安全

有什么问题或想法要分享吗？请在下方留言评论。

* * *

你对 web 开发的生产力热点技巧感兴趣吗？我是一名巴西全栈开发人员，在巴黎生活和工作，我经常分享像您刚刚阅读的文章这样的文章。[在推特上关注我](https://twitter.com/whosantelo)敬请关注。