# 一种为开源项目做贡献的新方式

> 原文：<https://dev.to/kasuken/a-new-way-to-contribute-to-open-source-projects--2fm7>

有时我想学习新的东西或挑战自己，但我没有任何新的想法。我经常去 GitHub 寻找新的东西来贡献。

几天前，肖恩·博耶([https://github.com/spboyer](https://github.com/spboyer))创造了一个新的工具。NET Core 全球工具，帮助喜欢寻找新项目的人做出贡献。

这个项目叫做 UpForGrabs([https://github.com/spboyer/dotnet-upforgrabs](https://github.com/spboyer/dotnet-upforgrabs))。

## 要求

首先，该工具仅适用于。网芯 2.2。

[![alt text](img/c0e7394fa58b658038b03940290d7505.png ".NET Core Logo")](https://res.cloudinary.com/practicaldev/image/fetch/s--FUJ8JSC3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dotnet.microsoft.cimg/redesign/downloads-dot-net-core.svg)

要安装它，您可以访问。网址:[https://dotnet.microsoft.com/download](https://dotnet.microsoft.com/download)

您可以使用命令
验证安装是否正确

```
dotnet --info 
```

Enter fullscreen mode Exit fullscreen mode

之后，您可以使用命令安装该工具:

```
dotnet tool install -g upforgrabs 
```

Enter fullscreen mode Exit fullscreen mode

## 如何使用

打开你最喜欢的终端(例如，我用 Hyper ),输入:

```
upforgrabs 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用参数-n:
更改结果的数量

```
upforgrabs -n 15 
```

Enter fullscreen mode Exit fullscreen mode

您也可以决定使用参数-o:
自动打开问题

```
upforgrabs -n 15 -o 
```

Enter fullscreen mode Exit fullscreen mode

下面你可以看到这个工具的运行:

[![](img/8219af4e0cd6957e052dd05718dbbf4a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RZap8yWh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pgeqb5wldz7yhsiftp1g.gif)

享受您对开源项目的贡献吧！:)