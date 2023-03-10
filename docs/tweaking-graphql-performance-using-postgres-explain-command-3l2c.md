# 使用 Postgres 的 Explain 命令调整 GraphQL 性能

> 原文：<https://dev.to/hasurahq/tweaking-graphql-performance-using-postgres-explain-command-3l2c>

[![](img/b996a10f29db32d79663176181e6573e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JD2Rs_hl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/tweaking-graphql-performance-using-postgres-explain-command-6a3d84fd9c9a/1-kNkakwKusPEd8nvDs-kC9A.png)

### 利用 Postgres 的执行计划信息发现性能瓶颈

由于 Hasura GraphQL 引擎的 API 旨在由许多前端客户端应用程序并发使用，我们投入了大量精力来确保 Hasura 具有超强的性能(*在延迟和资源消耗方面*)以及内在的可伸缩性。在这篇文章中，让我们看看我们是如何利用 Postgres 流行的执行计划分析工具`Explain`为用户引入性能分析特性的。

## 在 Hasura GraphQL 引擎中的表现

<figure>[![](img/38010c24589830e49e6d7d955d836728.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--60q6S7am--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/downloaded_images/tweaking-graphql-performance-using-postgres-explain-command-6a3d84fd9c9a/1--U7I8Ek0GURsp5-KBAacNQ.png)

<figcaption>graph QL 引擎世界中一个查询的生命周期</figcaption>

</figure>

对 Hasura 的传入请求经过以下几个阶段:

1.  请求体被解析成一个 GraphQL AST
2.  验证 GraphQL 查询(*检查语义正确性和 ACL/授权*)，如果它通过了所有检查，
3.  经验证的查询被转换为 SQL 语句，并由 Postgres ( *执行，Postgres 还将结果集转换为 JSON*
4.  JSON 响应被返回给客户机。

我们之前已经在博客中讨论了为优化 Hasura GraphQL 引擎的性能而做出的[架构决策。网络延迟也可以忽略不计，因为 Hasura 和配置的 Postgres 实例应该位于同一个数据中心/网络中。所以，剩下的就是数据库优化，比如索引等等。作为提高 GraphQL API 性能的关键选项。这些优化在很大程度上依赖于数据库模式和所处理的数据量。](https://blog.hasura.io/architecture-of-a-high-performance-graphql-to-sql-server-58d9944b8a87)

Postgres' `Explain`命令显示为所提供的 SQL 语句生成的执行计划。执行计划显示了如何扫描语句引用的表，如果引用了多个表，将使用什么连接算法将每个输入表中的所需行连接在一起，以及估计的语句执行成本([*Postgres docs*](https://www.postgresql.org/docs/10/sql-explain.html)*)；看看这篇关于如何解释 Postgres 执行计划*的 [*帖子*](https://robots.thoughtbot.com/reading-an-explain-analyze-query-plan) *。*