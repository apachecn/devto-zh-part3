# 为什么 PostgreSQL 有 pg_locks

> 原文：<https://dev.to/j0nimost/why-does-postgresql-have-pglocks-mei>

本周早些时候，我在[安德拉](https://andela.com/)进行了一次面试，他们给了我们一个挑战，用 postgreSQL 和 Flask 构建一个 API。在我 3 年的软件开发工作中，我从未接触过 PostgreSQL。

很快，我开始在我的 API 中遇到奇怪的错误。Postgres 有时会冻结，这是因为 postgres 会锁定我的数据库请求。这个我还是不明白，为什么？pg_locks 的用途是什么？