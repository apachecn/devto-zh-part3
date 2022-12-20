# Laravel 中不安全的 SQL 函数

> 原文：<https://dev.to/brendt/unsafe-sql-functions-in-laravel-38l0>

我最近了解到，并不是 Laravel 中的所有查询构建器功能都是“安全的”。这意味着用户输入不应该直接传递给它，因为这可能会将您的应用程序暴露给 SQL 注入漏洞。

继续阅读 https://stitcher.io/blog/unsafe-sql-functions-in-laravel