# SQL Server 中参数嗅探的简单解决方案

> 原文：<https://dev.to/praneetnadkar/my-simple-solution-to-parameter-sniffing-in-sql-server-94j>

前几天，我在调查产品中的一个 bug。有一个存储过程运行得非常慢。我想让我们看看过程中写了什么。当我复制查询并单独运行它时，我被吓呆了。该查询在 2-3 秒内运行，而过程至少需要 4-5 分钟。

这让我很好奇，在挖掘中我发现了一个叫做“参数嗅探”的概念。简单地说，根据 MSDN 的 SQL 博客，参数嗅探就是:

SQL Server 使用(嗅探)第一次编译过程时发送的参数来编译存储过程，并将其放入计划缓存中。此后，每当该过程再次执行时，SQL Server 都会从缓存中检索执行计划并使用它(除非有重新编译的理由)。当第一次执行存储过程时，潜在的问题出现了，参数集为该参数集生成了一个可接受的计划，但对其他更常见的参数集却非常糟糕。[链接此处](https://blogs.msdn.microsoft.com/turgays/2013/09/10/parameter-sniffing-problem-and-possible-workarounds/)

这个问题的解决方案是，我只是将存储的过程参数复制到过程的一个局部变量中。然后，我使用这些局部变量来运行 proc，瞧！。这工作完美！

有人遇到过这样的场景或者一些简单但很棒的解决方案吗？

我很好奇。