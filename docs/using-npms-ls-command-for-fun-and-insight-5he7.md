# 使用 npm 的“ls”命令获得乐趣和洞察力

> 原文：<https://dev.to/bnb/using-npms-ls-command-for-fun-and-insight-5he7>

我对 JavaScript 和 Node.js 依赖树的最大问题之一是...理解你所拥有的以及你能做些什么来补救从来都不是一件容易的事。

我最近一直在探索`npm ls` API，并想分享一些我发现的事情，我希望我在过去几年里就已经知道了！

## 简介`npm ls`

如果您不熟悉`npm ls`，npm CLI 中有一个命令可以列出已经安装到`node_modules`的依赖项。此外，如果在`node_modules`解析的依赖树不是从`package.json`解析的应该是*，它将返回一个非零的退出代码。*

这里有一个关于`npm ls`的简单例子，来自我的一个项目，[好的首发](https://github.com/bnb/good-first-issue):