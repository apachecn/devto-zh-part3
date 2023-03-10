# 2019 年数据库趋势–SQL 与 NoSQL、顶级数据库、单数据库与多数据库使用

> 原文：<https://dev.to/scalegrid/2019-database-trends--sql-vs-nosql-top-databases-single-vs-multiple-database-use-1nma>

[![SQL vs. NoSQL Usage Pie Chart for 2019 - ScaleGrid Blog](img/87d2c2a80d5dbf69bc88c076f84dfe97.png)](https://scalegrid.io/blog/2019-database-trends-sql-vs-nosql-top-databases-single-vs-multiple-database-use/)

想知道[2019 年哪些数据库是趋势](https://scalegrid.io/blog/2019-database-trends-sql-vs-nosql-top-databases-single-vs-multiple-database-use/)？我们在 [DeveloperWeek](https://www.developerweek.com/) 询问了数百名开发人员、工程师、软件架构师、开发团队和 IT 领导，以了解当前 NoSQL 与 SQL 的使用情况、最流行的数据库、要跟踪的重要指标以及他们最耗时的数据库管理任务。获得关于 [MySQL](https://scalegrid.io/mysql.html "MySQL Hosting") 、 [MongoDB](https://scalegrid.io/mongodb.html "MongoDB Hosting") 、 [PostgreSQL](https://scalegrid.io/postgresql.html "PostgreSQL Hosting") 、 [Redis](https://scalegrid.io/redis.html "Redis Hosting") 和许多其他系统的最新见解，看看今年哪些数据库管理系统最受青睐。

## SQL 与 NoSQL

任何数据库管理员都知道，您必须自问的第一个问题是，您的应用程序是使用 SQL 还是 NoSQL 数据库。两者有什么区别？

### SQL 数据库

也称为关系数据库，基于结构化查询语言(SQL)定义和操作数据。这些是处理结构化数据最常用和最有用的方法，它组织数据元素并标准化它们之间以及与不同属性之间的关系。

### NoSQL 数据库

也称为非关系数据库，允许您使用动态模式存储和检索非结构化数据。NoSQL 因其创建独特结构的灵活能力而被广泛使用，它可以是文档、图形、列，甚至是组织为数据结构的键值。

几十年来，SQL 一直领先于非关系数据库，但 NoSQL 正在迅速缩小与 MongoDB、Redis 和 Cassandra 等流行数据库的差距。尽管许多组织选择从 Oracle 等传统数据库迁移，但并非所有组织都在向 NoSQL 方式迁移。根据我们的调查结果，随着对 PostgreSQL 等系统的需求不断增加，SQL 仍占据 60%的市场份额:

#### SQL 数据库使用率:60.48%

#### NoSQL 数据库使用率:39.52%

[![SQL vs. NoSQL Usage Pie Chart for 2019 - ScaleGrid Blog](img/1d92b825855de729efbb80fa302c41f9.png)](https://scalegrid.io/blog/wp-content/uploads/2019/02/SQL-vs-NoSQL-Cloud-Database-Use-Stats.png)

## 最流行的数据库

那么，2019 年哪些数据库最受欢迎呢？知道超过 3/5 的受访者使用 SQL，您可能会认为 Oracle 抢了风头。再猜。MySQL 以 38.9%的使用率在这份报告中占据主导地位，其次是 MongoDB 的 24.6%，PostgreSQL 的 17.4%，Redis 的 8.4%，以及 Cassandra 的 3.0%。Oracle 在这些数据库报告中仅落后 1.8%，CouchDB、Berkeley DB、Microsoft SQL Server、Redshift、Firebase、Elasticsearch 和 InfluxDB 用户合计占我们其他类别的 2.4%。

[![Most Popular Databases in 2019: MySQL, MongoDB, PostgreSQL, Redis, Cassandra, Oracle](img/61ba1d4703c36b2e9b6688a90f168765.png)](https://scalegrid.io/blog/wp-content/uploads/2019/02/Most-Popular-Databases-Used-MySQL-MongoDB-PostgreSQL-Redis-Cassandra-Oracle.png)

虽然这些数字可能令人震惊，但 MySQL、MongoDB 和 PostgreSQL 的流行程度的上升是毋庸置疑的。那么，这份调查与数据库管理系统趋势的最佳来源相比如何呢？ [DB-Engines 排名-趋势流行度](https://db-engines.com/en/ranking_trend)报告将这些领导者排在前五名，但甲骨文仍保持第一，微软 SQL Server 排在第三。

[![DB-Engines Ranking Trend Popularity - All Databases February 2019](img/c8537be558a0a3e3547d7cc97aeb1133.png)](https://scalegrid.io/blog/wp-content/uploads/2019/02/DB-Engines-Ranking-Trend-Popularity-All-Databases-February-2019.png)

虽然我们期望看到更多的 Oracle 数据库用户出席，但他们在世界上最大的开发者博览会上的表现却很差。

## 单数据库与多数据库的使用

与孤注一掷的传统策略相比，多数据库类型的使用在过去的十年中呈爆炸式增长。多少钱？我们采访过的几乎一半的组织实际上使用多种类型的数据库来支持他们的应用程序，而不是单一的数据库！44.3%的人报告使用多个数据库，而 55.7%的人使用一个数据库:

[![Single Database vs. Multiple Database Use Trends - ScaleGrid Blog](img/5cf1da10d5582d7d8677baea452289d4.png)](https://scalegrid.io/blog/wp-content/uploads/2019/02/Single-Database-vs-Multiple-Database-Use-Trends.png)

### SQL 和 NoSQL 多数据库组合

那么，了解到几乎一半的受访者正在组合多个数据库来支持他们的产品，他们在一起使用什么类型的数据库管理系统呢？这一点并不令人吃惊，75.6%的多数据库类型使用是由 SQL 和 NoSQL 数据库的组合构成的。这进一步证明，对于许多组织来说，一种尺寸并不适合所有人。虽然您可能更喜欢 SQL 和 NoSQL，但不可否认的事实是，它们都提供了对方的明显优势。不要将您的组织局限于一种数据库类型，而是发展(或开发)您的数据策略以实现兼容性，以便这些强大的数据库管理系统可以相互补充并填补您的数据需求中的空白！

#### SQL + NoSQL 数据库使用率:75.6%

#### SQL + SQL 数据库使用率:14.6%

#### NoSQL + NoSQL 数据库使用率:9.8%

[![Multiple Database Use Combinations - SQL and NoSQL](img/bd3b74c0f379baea1d7bd2fbcd1fb098.png)](https://scalegrid.io/blog/wp-content/uploads/2019/02/Multiple-Database-Use-Combinations-SQL-and-NoSQL.png)

### 最流行的多种数据库类型组合

如果您是一个单一数据库类型的用户，并且正在考虑将另一种数据库类型添加到您的组合中，那么这一部分可能会引起您的高度兴趣——SQL 和 NoSQL 等哪些数据库是最常一起使用的。

超过 1/3 的多数据库类型使用的明显赢家是 MySQL 和 MongoDB 的组合。虽然 MongoDB 通常被认为是 MySQL 的替代产品，但如果设计得当，这两个数据库确实可以很好地协同工作。第二受欢迎的组合是 MySQL 和 PostgreSQL。这两个 SQL 数据库是明显的竞争对手，但是可以联合使用来存储不同的数据集。正如您在上面的截面图中看到的，MySQL 和 PostgreSQL 的 9.76%表示构成了多个数据库中 SQL + SQL 使用的大部分。

#### MySQL + MongoDB: 34.15%

#### MySQL + PostgreSQL: 9.76%

#### MongoDB + PostgreSQL: 7.32%

#### 蒙戈布+里兹:7.32%

#### MySQL + MongoDB + PostgreSQL: 4.88%

#### MySQL+mongbd+PostgreSQL+redis:4.88%

[![Most Popular Multiple Database Type Combinations - ScaleGrid Blog](img/511cc44d0cd708bf729c310a125e478a.png)](https://scalegrid.io/blog/wp-content/uploads/2019/02/Most-Popular-Multiple-Database-Type-Use-Combinations.png)

## 最耗时的数据库管理任务

现在，我们知道了哪些数据库管理系统、类型和使用组合是最受欢迎的，让我们看看在数据库管理方面是什么在消耗我们的时间。任何以前管理过数据库的人都知道，维护一个健康的生产部署涉及到无数的任务。因此，在我们最耗时的数据库管理任务问题中看到如此多样的回答，我们并不感到惊讶。

我们的受访者以 12.6%的比例将监控排在第一位，仅次于备份、管理磁盘空间、扩展和连接表，三者各以 11.6%的比例并列第二。排在第三位的是维护和重新分配视图和存储程序之间的变化，占 8.7%，再次并列第四，每次清理和数据库设置占 7.2%。升级排在第五位，占 6.5%，其他十几项任务占 11.6%，包括迁移、查询、比较、调优和复制。

[![Most Time-Consuming Database Management Task Chart - ScaleGrid Blog](img/8c0595a56432af991cc2a50d75c019de.png)](https://scalegrid.io/blog/wp-content/uploads/2019/02/Most-Time-Consuming-Database-Management-Task-Chart.png)

## 跟踪数据库性能的最重要指标

虽然我们看到了对最重要的数据库管理任务的各种各样的响应，但跟踪性能的最重要的指标有三个重要的领导者。

查询响应时间不仅是跟踪最多的指标，也是最多的指标，有 51.8%的响应！我们预计这一问题会领先，因为我们在 2018 年 10 月编制的一份[最耗时的 PostgreSQL 管理任务](https://scalegrid.io/blog/latest-postgresql-trends-most-time-consuming-tasks-important-metrics-to-track/)报告中，该问题占 30.8%，但当我们将该问题扩展到所有数据库管理系统时，该问题显著增加。查询速度是一个持续跟踪的非常重要的指标，因此您可以识别可能影响应用程序性能的运行缓慢的查询。许多 DBA 使用[慢速查询分析器](https://help.scalegrid.io/docs/mysql-monitoring-slow-query-analyzer)工具来识别有问题的查询，查看它与哪种类型的查询相关联，按时间范围理解他们的查询，并在您的系统中找到导致读取负载的最大查询，以识别那些没有索引的查询。

排在第二位的是可靠性，有 18.2%来自我们的受访者。不用说，虽然停机不像慢速查询那样常见，但是如果您的数据库停机，将会对您的性能产生最严重的影响。这就是为什么为您的生产部署实施[高可用性框架](https://scalegrid.io/blog/mysql-high-availability-framework-explained-part-1/)至关重要，以便在其中一个数据中心出现故障时保持数据库在线。

记忆以 8.2%的回答排在第三位。可用的内存越多，数据库的性能就应该越好。理解和[监控内存使用](https://scalegrid.io/blog/tracking-mongodb-memory-usage/)应该在你的列表中占很高的位置，因为内存不足或耗尽会导致你的数据库向你的磁盘读写数据，这会大大降低速度。

[![Most Important Metric Tracked For Database Performance - Queries, Reliability & Memory](img/e152c1a3a3111b71b736ee5a51614080.png)](https://scalegrid.io/blog/wp-content/uploads/2019/02/Most-Important-Metric-Tracked-For-Database-Performance-Queries-Reliability-Memory.png)

感谢在 DeveloperWeek 2019 上为云数据库趋势报告做出贡献的数百名参与者！我们很高兴分享这些见解，并希望在我们的评论中听到您的想法。