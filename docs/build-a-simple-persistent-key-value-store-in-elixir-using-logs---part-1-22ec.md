# 使用日志在 Elixir 中构建一个简单的持久性键值存储——第 1 部分

> 原文：<https://dev.to/alvisesus/build-a-simple-persistent-key-value-store-in-elixir-using-logs---part-1-22ec>

[![image](img/63bf2119b56d57b82dfcadac7db8f921.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JhVm8QaE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y3a4i8w026i8zykqtlvo.png)

[使用日志在 Elixir 中构建一个简单的持久键值存储——第 1 部分](https://www.poeticoding.com/build-a-simple-persistent-key-value-store-in-elixir-using-logs-part-1/?utm_source=reddit&utm_campaign=devto)

在这一系列文章中，我们将看到键值存储(日志、段、压缩、Memtable、SSTable)背后的不同概念，它们在 Elixir 中实现了一个简单的引擎，这是一种构建高并发和容错架构的优秀语言。