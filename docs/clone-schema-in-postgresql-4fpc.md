# PostgreSQL 中的克隆模式

> 原文：<https://dev.to/renatosuero/clone-schema-in-postgresql-4fpc>

大家好，

我的目标是保持这篇文章简短，所以如果你不知道什么是模式访问这个[链接](https://www.postgresql.org/docs/11/ddl-schemas.html)。

模式可能对客户有用，可能是客户的模式，也可能是“类别”的模式。在我的工作中(我们的业务基于足球)，我们使用锦标赛和年份的模式。
一个样本:
ucl_2018
ucl_2017
冠军*就是这个模型

好了，现在我们需要创建一个新的 schema ucl_2019，对吧？

我研究了一下，过了一段时间，我发现了一个可以帮助我的功能，但是我需要做一些改变。我不记得我到底需要做什么，所以我决定分享我一直在使用的功能。另一件事，这个函数将创建一切索引，键，外键等...

好了，这就是你需要在数据库中运行的函数。