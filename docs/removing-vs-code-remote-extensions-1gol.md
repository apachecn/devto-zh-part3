# 移除 VS 代码远程扩展

> 原文：<https://dev.to/azure/removing-vs-code-remote-extensions-1gol>

如果你错过了上周的公告，VS 代码团队已经发布了一些[远程开发扩展](https://code.visualstudio.com/docs/remote/remote-overview?WT.mc_id=devto-blog-aapowell)，允许你在远程环境中运行 VS 代码，无论是 WSL、SSH 还是在容器中运行。这是一个**奇妙的**扩展，我完全爱上了它，但总有出错的可能性(当你生活在边缘时就会发生这种事)。

今天我去开始写博文(不是这篇！)这样做的时候，我安装了[拼写正确](https://marketplace.visualstudio.com/items?itemName=ban.spellright&WT.mc_id=devto-blog-aapowell)，这是我用于拼写检查的扩展。一旦 VS 代码重新加载出错，它将不再连接 WSL，结果是[拼写错误](https://github.com/bartosz-antosik/vscode-spellright/issues/279)导致 VS 代码远程扩展挂起。

这并不是拼写正确的问题，我使用的是 VS 代码的预览版和一个预览扩展包，我只是惊讶我没有早点发现这个问题！🤣

无论如何，我们现在有一个问题，我需要从远程主机上删除那个扩展，但是这里有一个问题，你使用 VS 代码来管理远程安装的扩展，如果它不能连接到远程主机，那么你就不能管理扩展！

## 删除没有 VS 代码的远程扩展

我开始通读文档，试图找出如何删除扩展，但没有成功。我的下一步是尝试打开扩展包，并希望我能在里面找到一些东西，但这听起来并不是一个有趣的想法…谢天谢地，我与一个真正博学的团队合作，我的同事[布鲁诺·博尔赫斯](https://twitter.com/brunoborges)来救我。

原来扩展是在`~/.vscode-remote/extensions`安装在远程主机上的，所以我启动了 WSL，并删除了这个有问题的扩展。如果您需要完全删除扩展，您可以`rm`其中的所有文件夹。

谢谢布鲁诺。

此外，如果您在`~/.vscode-remote`文件夹中四处搜寻，您会在那里找到一堆有趣的东西，比如您的远程环境的用户配置文件等等。我不建议编辑它们，但是如果你想尝试和诊断问题，它们可以是一个很好的地方。

所以移除了扩展之后，我可以写这篇关于如何移除远程扩展的文章，然后回到我*实际上*来这里写的文章！😜