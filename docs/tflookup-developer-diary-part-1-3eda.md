# tflookup -开发者日记第 1 部分

> 原文：<https://dev.to/dploeger/tflookup-developer-diary-part-1-3eda>

亲爱的日记！

作为一名 DevOps 工程师(或者酷人们称之为 SRE ),我与 [Terraform](https://terraform.io) 密切合作，这是来自 [Hashicorp](https://hashicorp.com) 的另一个基于开源的应用程序，允许在文本文件中定义基础设施(阅读:*“基础设施代码”*)

Terraform 有丰富的文档，尤其是你可以通过不同的提供商做的事情。

因此，在了解了 Terraform 及其 DSL 的基本方法和内容之后，我花了大部分时间点击 Terraform 文档，以找到我想要了解其参数的特定资源或数据源部分。

那是乏味的。

我试着优化那个 T1，但还是不够快。

我需要快速查找。一个输入框，我可以在里面输入一些关键字，它会很快引导我进入正确的文档页面。

但是，嘿，我是个开发者。我了解 REST、web UIs 之类的东西，所以这不会很难，对吗？

讲述者:“他错了”

所以，我的小日记。我将踏上一段旅程，带给我最值得的东西。查找功能。对于地形。我想，我会叫它 [tflookup](https://tflookup.herokuapp.com/) ！

你的，丹尼斯

*这篇文章是 [tflookup 开发者日记系列](https://dev.to/t/tflookup)* 的五篇文章之一

*封面图片:弗雷德里克·鲁本森的《日记写作》*