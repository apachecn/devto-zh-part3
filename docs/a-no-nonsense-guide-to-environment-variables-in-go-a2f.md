# Go 中环境变量的实用指南

> 原文：<https://dev.to/craicoverflow/a-no-nonsense-guide-to-environment-variables-in-go-a2f>

环境变量是为软件应用程序设置配置值的最佳方式，因为它们可以在系统级定义，与软件无关。这是[十二因素应用](https://12factor.net/config)方法的原则之一，并使应用程序能够以可移植性构建。

[![](img/79e6db07034063dfecf97b71febb5b49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V9p2sA_2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/1eNgrht.jpg)

## 使用环境变量

你需要与环境变量交互的只是标准的 [`os`](https://golang.org/pkg/os/) 包。这里是一个关于如何访问系统环境变量`PATH`的例子
。