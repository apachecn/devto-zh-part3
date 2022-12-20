# 使用 Hasura GraphQL 构建一个协作式实时 Todo 应用后端

> 原文：<https://dev.to/auth0/build-a-collaborative-real-time-todo-app-backend-using-hasura-graphql-4opn>

TL；DR 在本教程中，我们将使用 GraphQL APIs 构建一个实时待办应用后端，使用 JSON Web 令牌进行保护。

用 Hasura 设置实时后端 Hasura 通过 PostgreSQL 提供即时的实时 GraphQL APIs。它带有内置的基于角色的访问控制，并支持通过 webhooks 和 JWT 进行授权。Hasura 在连接到 PostgreSQL 数据库时会自动生成类型、查询和突变，客户端可以直接用 auth 进行查询。

已经使用 React 实现了一个前端应用程序，它具有以下特性:

*   一个简单的用户界面来列出，添加和删除我们的待办事项
*   面向公共待办事项的协作式实时用户界面
*   使用 Auth0 进行身份验证
*   用于自动同步用户注册和数据库的 Auth0 规则
*   Apollo 客户端连接到 GraphQL APIs

我们将更多地关注用 Hasura 和 Auth0 构建后端，并使用 React 应用程序演示作为例子来展示一切是如何集成的。
[继续阅读🛠](https://auth0.com/blog/building-a-collaborative-todo-app-with-realtime-graphql-using-hasura/?utm_source=dev&utm_medium=sc&utm_campaign=collabapp_hasura)