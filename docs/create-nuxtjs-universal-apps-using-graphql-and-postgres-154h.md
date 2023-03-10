# 使用 GraphQL 和 Postgres 创建 Nuxt.js 通用应用程序

> 原文：<https://dev.to/hasurahq/create-nuxtjs-universal-apps-using-graphql-and-postgres-154h>

# TL；博士:

服务器端使用 Hasura GraphQL 引擎通过 postgres 使用 [Nuxt.js](https://nuxtjs.org/) 和[graph QL](https://graphql.org/)API 渲染网站。即时设置。教程/样板文件👉 [nuxtjs-postgres-graphql](https://github.com/hasura/graphql-engine/tree/master/community/sample-apps/nuxtjs-postgres-graphql)

[![Nuxt.js Hasura GraphQL Postgres](img/54c4ac3149b9933760521cb9cf32c623.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4ANoDWU2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b9kdnvl00v541vm2oolh.png)

构建通用应用程序通常被吹捧为困难的，Nuxt.js 通过高性能和模块化来解决 Vue.js 应用程序的这个问题。Hasura GraphQL 引擎使得在 Postgres 数据库之上获取 GraphQL APIs 变得很容易。在这篇文章中，我们将看看这两者是如何结合的。

# Nuxt.js

js 提供了对 SSR 应用程序的完全支持，并处理了常见的陷阱。做通用 app，更精彩；页面由服务器呈现，然后由 SPA 接管。它是这样工作的:

#### 页面:

Nuxt 在项目目录结构上固执己见。它将改变一切。vue 文件作为应用程序的路由。社区已经接受了不同项目的自以为是的目录结构(看看你 Gatsby，Next.js ),并且很容易熟悉。在样板示例中，您可以看到[页面](https://github.com/hasura/graphql-engine/tree/master/community/sample-apps/nuxtjs-postgres-graphql/pages)是如何构造的。关于 Nuxt 实施的目录结构的更多信息，请在这里阅读更多。

#### 服务器端数据抓取:

Nuxt 社区已经构建了这个 [apollo-module](https://github.com/nuxt-community/apollo-module) ，它为 GraphQL 端点设置了 apollo 客户端。因此，在 index.vue 页面中，我们将 apollo 片段添加到脚本中，如下所示: