# 在 PostgreSQL 的模式中运行迁移

> 原文：<https://dev.to/renatosuero/run-migrations-in-schemas-in-postgresql-306o>

Hy 各位，

这篇文章是那个[链接](https://dev.to/renatosuero/clone-schema-in-postgresql-4fpc)的延续。
现在我们有了模式，但是我们如何在模式中创建新表呢？

我一直在使用一个函数来处理模式中的迁移，它可以用于任何事情。创建表、表中的变化、触发器、函数、索引等...

好了，这就是你需要在数据库中运行的函数。