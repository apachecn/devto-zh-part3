# 写一个 Postgresql 代理。开始。

> 原文：<https://dev.to/siscia_/write-a-postgresql-proxy-the-beginning-3c9i>

这一系列文章将跟踪我为 Postgres 创建一个 [RediSQL](http://redisql.com/) 代理、 [pg-redis-proxy](https://github.com/RedBeardLab/pg-redis-proxy) 的过程。

这个项目的最终目标是拥有一个代理，它将监听 PG 协议，将查询转发给 RediSQL，并最终将答案返回给原始客户端。

如果该项目成功，我希望将代码集成到 RediSQL 本身，从而为 RediSQL 提供另一个接口，不仅仅是 Redis 协议，还直接包括 PG 协议。

## 告诫

我将要构建的抽象肯定会有漏洞。

事实上，SQLite(RediSQL 基于它)并不支持 PG 的很多特性。典型的例子是 SQLite 中不支持的所有日期数据类型。

然而，我仍然觉得它可能是有用的和有趣的建设。

## 接近

理想情况下，我希望将代码合并到主 RediSQL 中，这肯定会建议在 Rust 中编码。

但是 Rust 是一种“生产”语言。

至少根据我的经验，你需要对问题和设计有一个很好的理解，才能成功地在 Rust 中编写代码。

此外，编程中有一句老话，建议您计划至少一个原型，您最终会扔掉它并从头开始重新编写。

事实上，我不太确定整个架构，也不太确定在写这个代理时我将面临的问题。

pg-redis-proxy 的第一个版本将用 Python3 编写。

## Python 版本的目标

第一个 python 版本的目标是:

1.  了解 PG 协议
2.  快速探索几种可能的架构
3.  为其他想要探索类似项目的人提供一些开源库和指南

当可以对 pg-redis-proxy 实例执行简单的 SQL 文件并让它返回预期结果时，我将停止开发该项目。

我的目标 SQL 文件类似于:

```
CREATE TABLE foo(a INT, b INT, c INT);
INSERT INTO foo VALUES(1,2,3);
PREPARE insertfoo (int, int, int) AS
    INSERT INTO foo VALUES($1, $2, $3);
EXECUTE insertfoo(5,6,7);
INSERT INTO foo VALUES(4,5,6);
EXECUTE insertfoo(8,9,0); 

SELECT * FROM foo; 
```

我的目标不是编码任何超出绝对必要的东西，但我会非常开放地接受拉请求。

因此，如果你对这个项目感兴趣，或者对这个项目的一部分感兴趣，请随意在资源库中投稿。

## 回购

你可以关注这个 github 库的进度。

最后，如果你有兴趣关注这个项目，你可以在 [twitter](https://twitter.com/siscia_) 上关注我，或者订阅[原创博客](https://redbeardlab.com/2019/04/20/write-a-postgresql-proxy-motivation/)的邮件列表。