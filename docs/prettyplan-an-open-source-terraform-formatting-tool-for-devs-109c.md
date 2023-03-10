# Prettyplan:一个面向开发者的开源 Terraform 格式化工具

> 原文：<https://dev.to/chrislewisdev/prettyplan-an-open-source-terraform-formatting-tool-for-devs-109c>

*TL；dr: prettyplan 是一个在线工具，你可以粘贴来自`terraform plan`的输出，并看到它以易于阅读的表格格式呈现。[这里有](https://chrislewisdev.github.io/prettyplan/)！*

我不确定这里有多少人使用 Terraform T1，这是一个流行的基础设施代码工具，但是如果你使用过，你可能在大型项目中试图读取它的命令行输出时遇到过麻烦。

Terraform 提供的默认输出让您知道它将做出什么样的改变，这是非常好的，但在某些情况下，它可能很难阅读...例如，考虑尝试阅读此处发生的变化:

[![terraform console output](img/ac950b46f184138188913d37adfa1ff9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ChIFiNya--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gu0wpyqp3ph3ibl3ehpt.png)

为了帮助这一点，我创建了 prettyplan:一个小型的 web 应用程序，它解析你的 Terraform 输出，并以一种更容易阅读的表格格式呈现出来。这是一个有趣的项目，我希望能帮助其他开发者。

对于 prettyplan，前面的示例看起来像这样:

[![prettyplan formatted output](img/1f3a4e7b0ed3143f3577560d9cc297c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SQAE1wmV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pe3y2b8moy0ibfwpodm8.png)

这(至少对我来说)看起来更好，也更容易管理！因此，如果您发现自己以前阅读过这种控制台输出，请尝试一下。:)

# 贡献

如果 prettyplan 对您有用，并且您有兴趣参与其中，那么开始并做出更改是非常简单的，因为该项目目前都是没有依赖或构建的普通 JS(除了一些在提交时运行的单元测试)。我欢迎人们认为可以做出的任何改进！