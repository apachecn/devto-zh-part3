# Firebase 到 GraphQL:迁移数据库

> 原文：<https://dev.to/malgamves/firebase-to-graphql-migrating-your-database--1dk3>

你好！我注意到很多网络应用程序都使用 Firebase 作为后端服务。我找到了 [firebase2graphql](https://github.com/hasura/graphql-engine/tree/master/community/tools/firebase2graphql) ，一个 CLI 工具，可以帮助你在你的 firebase 数据上尝试实时 graphql。它获取从 firebase 导出的数据，并通过 Hasura GraphQL 引擎将其导入 Postgres。

[![malgamves](img/de2c5b8f35d0fd19ad172bb8922e7555.png)](/malgamves) [## Hasura GraphQL 引擎上的迁移(Heroku 到数字海洋)

### 丹尼尔·马达里索·菲里 11 月 29 日 181 分钟阅读

#graphql #vue #javascript #youtube](/malgamves/migrations-on-the-hasura-graphql-engine-heroku-to-digital-ocean--3b3c)

为此，我不得不从头开始构建一个应用程序，它确实帮助巩固了许多 GraphQL 操作，因为当从 Firebase 更改时，您必须配置应用程序以 GraphQL 的方式显示数据。看一下视频

[https://www.youtube.com/embed/E7fbcu2IT3k](https://www.youtube.com/embed/E7fbcu2IT3k)

请告诉我这是否对您有用，以及您是否打算迁移。