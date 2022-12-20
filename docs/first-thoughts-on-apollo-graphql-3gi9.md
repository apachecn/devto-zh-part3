# 关于阿波罗图表的初步想法

> 原文：<https://dev.to/zephinzer/first-thoughts-on-apollo-graphql-3gi9>

## 它开始了

虽然我一直在研究更多与运营相关的技术，但我的团队一直忙于推进一项使用 Apollo GraphQL 的新服务。所以我决定去看看宣传到底是怎么回事。

## 设置

设置相当无缝。安装完依赖项，就可以开始了。我遵循了官方文件中的[指南](https://www.apollographql.com/docs/apollo-server/getting-started.html)

Apollo GraphQL 包含了一个方便的 GraphQL 平台，它看起来比我之前接触 GraphQL 时使用的 GraphQL 要好。

要开始，只需运行:

```
npm i apollo-graphql 
```

然后在代码中:

```
const {ApolloServer, gql} = require('apollo-graphql');

const staticData = [{a: 1, b: 2,},{a: 2, b: 3}];
const typeDefs = gql(`
  type Data {
    a: Number
    b: Number
  }
  type Query {
    data: [Data]
  }
`);
const resolvers = {
  Query: {
    data: () => staticData,
  },
};

const server = new ApolloServer({typeDefs, resolvers);
server.listen().then(({url}) => console.info(`Listening on ${url}`)); 
```

## 监控

作为一名运营工程师，我通常问的第一个问题是*“我如何监控这个东西？”*

官方的 Apollo GraphQL 文档推荐使用 Apollo 引擎，但是这需要一个 API 键，这也意味着在评估服务性能时，我可能需要监控不同的资源。我在寻找诸如响应时间、远程 IP 地址、有效负载大小、请求体结构*等等*

如果能有办法用这个来使用普罗米修斯就好了。

### 选项 1:使用上下文

所以我稍微探索了一下，找到了可以传递给`ApolloServer`构造函数的`context`选项。它公开了一个函数，该函数公开了传入请求和传出响应的属性。上下文可以用来注入普罗米修斯度量监听器吗？

### 方案二:使用 ApolloServer 作为中间件

Apollo GraphQL 的一个优点是它可以用作中间件。将依赖关系更改为`apollo-graphql-express`允许我使用带有`express-prom-bundle`的 Express 服务器来监控传入的请求。然而，有一个问题:所有的路径都是`/graphql`，这实际上没有意义。

## 代码结构化

通过这个基本的例子，很容易看出传递给`gql`函数的字符串是如何模块化扩展的。

假设我们有一个模仿数据模型的目录结构，这些模型中的每一个都可以导出一个 GraphQL 类型定义，然后可以将它合并到一个主模式中。参考了[这篇博文](https://blog.apollographql.com/modularizing-your-graphql-schema-code-d7f71d5ed5f2)。

## 最后的想法

GraphQL 对我来说仍然很有吸引力。根据我目前所了解的情况，Apollo GraphQL 还提供 React Apollo GraphQL，这似乎使编写查询更容易，并消除了使用 Redux 的需要，这是我正在寻找的真正好处。

*   我对定义响应模式的能力非常感兴趣，它允许我们通过契约驱动的测试来轻松测试与其他消费/提供服务的兼容性。
*   尽管有好处，我们如何监控它？在谷歌上快速搜索并没有发现太多关于 GraphQL 查询的监控。我宁愿要一个可以被监控的未优化的 RESTfully 编写的服务，而不是一个使用 GraphQL 的优化服务。