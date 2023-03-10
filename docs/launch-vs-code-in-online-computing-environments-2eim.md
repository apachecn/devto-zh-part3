# 在线计算环境中的启动与代码

> 原文：<https://dev.to/nexttech/launch-vs-code-in-online-computing-environments-2eim>

我很高兴地告诉大家，Next Tech 已经发布了对 Visual Studio 代码的 alpha 支持！

VS Code 无疑是最好的代码编辑器之一。近年来，由于微软增加了令人难以置信的功能以及成千上万的开发者创建了庞大的扩展库，它在受欢迎程度上击败了其他公司(如 Sublime 和 Atom)。

安装 VS 代码非常简单，如果你愿意，你可以在你的电脑上自己完成。然而，我们发现我们的 Python、Node、Go、Haskell 和许多其他编程语言的托管环境使得在几秒钟内开始一个新的编程项目变得非常容易。

所以我们想，如果你能使用这些环境…和 VS 代码，那不是很好吗？！

现在，你可以了。这包括安装扩展、更改设置、调试程序、推送至 GitHub、[部署至 Azure](https://c.next.tech/2JM6fqn) 等等。

本指南将带您了解如何在 Next Tech 上开始使用 VS 代码。如果你只是想跳进一个有 VS 代码的沙盒，点击[这里](https://c.next.tech/2K59jNN)。几秒钟后，您就会在浏览器中运行 VS 代码:

[![img](img/8913efc97a5b3961ebc74e351cadd18d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EAxj0Hki--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A2b2JRNx3flF6h1OJCYO85A.png)

或者，继续阅读了解详情！

# 为什么要 VS 码？

多年来，我们收到了许多创新功能的请求，如代码协作、调试器、版本控制集成等，但我们的重点越来越多地放在我们的基础设施产品上。因此，随着我们继续开发创新的基础设施产品，我们认为 VS Code 能够解决许多这样的请求。

VS 代码也是目前*最受*欢迎的代码编辑器。以下是 Stack Overflow 针对最受欢迎的开发环境的 2019 年开发者调查结果:

[![img](img/465983f6d67d8e999efcf382a69c7bf7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yheSukyt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AoJqJ7ZvrI0J_YNjV2N2BYQ.png)

这也让其他编辑付出了代价。这是谷歌关于 VS Code(蓝色)Sublime(红色)和 Atom(黄色)的趋势数据:

[![img](img/b4bda5636a0d2265d6092b1e32c4082e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JkNdk_lk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AAgJiQ7tAv1SxjuQoQasE3w.png)

因此，我们认为我们在集成 VS 代码方面的投资是非常值得的，因为我们将能够在我们的基础设施中提供一个深受欢迎的工具。

该功能目前处于非常早期的 alpha 状态(当前的限制记录在[这里](https://docs.next.tech/sandbox/vscode/alpha-status))。然而，在接下来的几个月里，我们将推出一个更紧密集成的版本和许多其他相关的改进。

## 以下是目前支持的内容:

*   智能代码完成(IntelliSense)。
*   强大的调试功能。
*   许多不同的方式来配置你的界面(多标签，zen 模式等)。).
*   集成终端。
*   多重主题。
*   VS 代码的命令行界面。
*   …以及您可以安装的许多其他扩展。

## 很快，我们还将增加对以下功能的支持:

*   集成的网络浏览器。
*   VS Code 的 Live Share 特性，可以让你和其他人实时协作。

现在，我希望您能看一看并[分享您的反馈](https://feedback.next.tech/vscode)！

# 入门

要开始，前往[沙盒发射台](https://c.next.tech/2KaW6Dp)。一旦你在那里，选择你想使用的语言(本指南使用 Golang)，然后勾选**使用 Visual Studio 代码**，如下所示:

[![img](img/8e181d080b1846a8a126bbcd549e7743.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ymt11XlN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AypD_Mjn2_pMAr26r)

您将看到一个对话框，其中包含对该功能当前限制的解释(也将在本页末尾详细介绍)。点击**听起来很有趣，我们走吧！**按钮，您的沙盒将加载 VS 代码接口:

[![img](img/83b0e1a0c89c300bcc1151722aafc86c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p_XO4UO4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AN00UlUcFrPKFaJ_3)

您可能会注意到，VS 代码只是沙盒界面中的另一种选项卡类型。如果您点击 **+** 来创建一个新的选项卡，您可能会注意到一些选项现在被隐藏了:

[![img](img/7e73726685e2c595bc5f380d1433738c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---k16HD3x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2Apo14izi2eqQHTPgp)

最终这些都将被隐藏，因为我们将它们直接集成到 VS 代码接口中。

为了获得最佳体验，可以点击 VS 代码界面右上角的方块，让 VS 代码全屏显示:

[![img](img/cf3107d7a75918ee19bb86177ce12d53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GWk8NCJu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AlnyAclE7854mooun)

要开始，您可以单击文件，然后单击新建文件:

[![img](img/8b6f4e57c0e0b649950bd2e072cd5256.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vQjvDs02--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2Ah6bIGRBMQCVm31Gd)

(注意，Ctrl+N 在浏览器中不起作用)

将您的文件保存为 main.go，然后将以下代码放入其中:

现在，前往扩展市场并安装 Go 扩展:

[![img](img/0a4e44aef7718a4956fe11760227f264.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--COrssZpb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AmK-6xStVebIVo4sU)

(您可能需要按 Ctrl+Shift+P，键入“reload ”,然后选择 **Developer: Reload Window** ,以使事情正常工作)

右下角会提示你安装几个软件包。去吧！

您可以尝试在代码编辑器中键入 b 来查看智能自动完成功能。这里，它看到 b 实际上是一个包含 5 个 int 的数组:

[![img](img/c08c9ba341a2a7fa1250a99981e1c036.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NcHYjKzc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2ARNAwfGAOP6J_4y_6)

您可以使用通常安装在沙箱中的软件运行代码。要运行此文件，请点按“终端”,然后点按“新建终端”:

[![img](img/b98b0b11cd156ccd5957260e5c6bb6a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--igAPB5PW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AYcYjtU6ehcZfrJt4)

然后，您可以从您的沙箱中使用已经安装的 go 程序:

[![img](img/85cbfb493ac6e50126e0801da7bfc11c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ekzadLF_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AmV37hQpsML4xs4YG)

现在你知道了！您刚刚使用云中的 VS 代码编写了一个 Go 程序。

# 反馈

如果你尝试这个新功能，我很想听听你的想法。欢迎回复此贴或在此提交一张票[。](https://feedback.next.tech/vscode)

感谢阅读！

# 大声呼喊

这个集成使用了一个改编版本的[这个](https://github.com/cdr/code-server)由 Coder 开发的令人敬畏的开源项目！