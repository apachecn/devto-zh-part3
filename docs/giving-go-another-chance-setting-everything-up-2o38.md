# 再给 Go 一次机会:设置好一切

> 原文：<https://dev.to/detunized/giving-go-another-chance-setting-everything-up-2o38>

首先，我需要能够建立和运行围棋程序。我用的是 macOS，所以安装 Go 非常快:

```
$ brew install go 
```

似乎有效:

```
$ go version
go version go1.11.4 darwin/amd64 
```

现在我需要联系我的编辑。我想至少有一些基本的功能，如保存和构建格式代码，并从编辑器中运行。通常我使用 Sublime Text 3，但在快速查看了[安装说明](https://margo.sh/b/hello-margo/)后，我决定尝试一下 Visual Studio 代码，在那里我可以一键设置好一切。

它已经在我的硬盘上放了一段时间了，现在我终于有了一个借口来尝试一下。所以，不仅仅是一种新的语言，还有一个新的编辑器。耶！

设置扩展只花了几分钟，这是快速和绝对无痛的。另一方面，让编辑器在保存时格式化代码是一个完全不同的故事。我花了很长时间摆弄设置、搜索、阅读 Github 上的相关问题等等。VS 代码在大多数时候非常有用，安装相关工具，突出显示我的配置中的错误等等。它拒绝做的唯一一件事是在保存时格式化我的代码。

好像我什么都试过了，我就放弃了，去睡觉了。第二天早上的结果是，它需要的只是重启。我真笨。我想这是因为我对软件和开发人员永远不灭的信心，这一次他们会把事情做好。这次没有这样的运气。至少 Windows 95 对此是诚实的。VS 代码让它看起来不需要重启，一切都有点工作，但不是 100%。更像是 93.76%。

最后，我完成了所有的工作:保存时格式化、构建、转到定义，甚至是成熟的智能感知，自动完成了几乎所有的事情。不错！

让我们下次开始编码吧。

* * *

这就是这些天我是如何依赖谷歌来完成这一点点工作的:

*   go 教程
*   自制安装 golang
*   goimports
*   goreturns
*   goformat
*   vscode 在保存时运行 fmt
*   vscode go 格式在保存时不起作用
*   go vet
*   去构建发布

* * *

耗时:3 小时
总耗时:3 小时