# 需要帮助更好地理解 Typescript，IoC，六角形架构

> 原文：<https://dev.to/alashahonore/need-help-to-better-understand-typescript-ioc-hexagonal-architecture-3h2f>

**你好，**

我是打字世界的新手，我正在努力学习一些技巧。

假设我想用 Winston 在我的应用程序中实现一个日志记录器。后来我想用班扬代替温斯顿。问题:应该更新所有使用旧记录器的文件。

*注:看起来温斯顿和班扬用的函数是一样的。让我们假设事实并非如此。*

我应该创建一个接口(ILogger)，然后为每个实现创建类(LoggerImplWinston 和 LoggerImplBunyan)吗？你还有其他选择吗？

同时，我也在尝试使用控制反转(Inversify)和六边形架构(接口、应用、域、基础设施)…

因此，我是法国人，所以尽量保持简单

提前感谢。