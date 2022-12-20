# 简单明了地用 Apollo 服务器将远程模式连接到本地模式

> 原文：<https://dev.to/bidah/simple-and-to-the-point-stitching-remote-schema-into-local-schema-with-apollo-server-24in>

*封面照片由[两把桨斧和皮革制品](https://unsplash.com/photos/9wNcZprwco8)在 Unsplash 上*

场景:您在 Apollo 服务器上运行了第一个模式，但是出现了一个新的 GraphQL API，您需要立即在客户机上使用它，而不需要太多的询问。

本教程将尝试让你尽快从 A 点到达 B 点。我们将制作一个小的 API 来解决我们的问题并供将来重用。你可以马上使用它，但我们会一步一步地解释每一个部分，以获得内部工作原理。

注意:
缝合本地模式和远程模式，我认为，在你**开始缝合**的情况下，效果非常好。为什么？因为您已经有了第一个模式并正在运行。如果出现一个 GraphQL 服务，只需在上面添加这个新的远程模式，就可以开始使用它了。

我告诉你这个是因为有另一种方法来解决这个问题，但是我们需要启动另一个微服务来作为我们两个 API 的代理(本地和远程模式现在都是远程的)。我认为当您有太多服务时，这可能会工作得很好，但首先，让我们只使用我们当前的服务器，并在其上添加一个新的远程模式来增强它。速度更快，少运行一个服务，很有效。

现在我们应该已经为我们的服务器设置好了。

```
const server = new ApolloServer({ typeDefs, resolvers });

server.listen().then(({ url }) => {
  console.log(`🚀 Server ready at ${url}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们将用这个模块替换以前的代码。

```
const { ApolloServer, makeExecutableSchema } = require('apollo-server')
const { HttpLink } = require('apollo-link-http')
const {
  introspectSchema,
  makeRemoteExecutableSchema,
  mergeSchemas,
} = require('graphql-tools')
const fetch = require('node-fetch')

async function startServer({
  localSchema: { resolvers, typeDefs },
  remoteSchema: { uri },
}) {
  const remoteHttpLink = new HttpLink({
    uri,
    fetch,
  })

  const remoteSchemaInstrospection = await introspectSchema(remoteHttpLink)

  const remoteSchemaExecutable = makeRemoteExecutableSchema({
    schema: remoteSchemaInstrospection,
    link: remoteHttpLink,
  })

  const localSchema = makeExecutableSchema({
    typeDefs,
    resolvers,
  })

  const mergedSchemas = mergeSchemas({
    schemas: [localSchema, remoteSchemaExecutable],
  })

  const server = new ApolloServer({ schema: mergedSchemas })

  return await server.listen()
}

module.exports = startServer 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们将向我们的外部 GraphQL API 发出一个请求。我们向配置对象传递我们的`uri`和对`fetch`库的引用。

```
 const remoteHttpLink = new HttpLink({
    uri,
    fetch,
  }) 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`instrospectionSchema`函数从远程 GraphQL 服务中检索模式。唯一的参数是我们之前定义的`HttpLink`实例。

```
 const remoteSchemaInstrospection = await introspectSchema(remoteHttpLink) 
```

Enter fullscreen mode Exit fullscreen mode

我们在自省之后得到了我们的模式，但是现在我们仍然缺少使它可执行。所以这是一个两步走的过程。可执行性将使我们能够稍后将它与我们的本地文件合并。

```
 const remoteSchemaExecutable = makeRemoteExecutableSchema({
    schema: remoteSchemaInstrospection,
    link: remoteHttpLink,
  }) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们使用`makeExecutableSchema`创建本地模式，用我们的`typeDefs`和`resolvers`传递一个配置对象作为参数，就像我们在开始看到的典型的服务器配置对象一样。

```
const localSchema = makeExecutableSchema({
  typeDefs,
  resolvers,
}) 
```

Enter fullscreen mode Exit fullscreen mode

用`mergeSchemas`和
合并两个模式

```
const mergedSchemas = mergeSchemas({
  schemas: [localSchema, remoteSchemaExecutable],
}) 
```

Enter fullscreen mode Exit fullscreen mode

在配置对象中将它传递给我们的服务器，并像往常一样启动它调用`listen`。

```
const server = new ApolloServer({ schema: mergedSchemas })

return await server.listen() 
```

Enter fullscreen mode Exit fullscreen mode

我们完成了模块分解🙌。现在让我们使用它！

`startServer`有一个配置对象作为参数，该对象有两个属性:

*   `localSchema`有两把钥匙的物体。`resolvers`和`typeDefs`。
*   `remoteSchema`一键对象。`uri`:我们外部 GraphQL API 的字符串。

```
startServer({
  localSchema: {
    resolvers,
    typeDefs,
  },
  remoteSchema: {
    uri: 'https://01uhb.sse.codesandbox.io/',
  },
}).then(({ url }) => {
  console.log(`🚀  => Server ready at ${url}`)
}) 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。现在，我们当前的服务器可以使用我们的两种模式。

自己玩吧。这是一个带有 GraphQL 服务器和我们的模块的代码沙箱。我们有两个疑问。`hello`是本地的，并且正在合并`goodbye`查询。

[https://codesandbox.io/embed/apollo-server-pu0gu](https://codesandbox.io/embed/apollo-server-pu0gu)

可以在这里抓取外部 Graphql 服务:[https://codesandbox.io/s/apollo-server-01uhb](https://codesandbox.io/s/apollo-server-01uhb)

这只是开始。从这里开始，你有很多需要调整和学习的地方。

如果一些类型在模式之间重复会怎样？如果我需要在另一个查询中使用一个查询的结果类型作为参数，该怎么办？

新的挑战带来了扩展和使用组合模式的新方法。希望这张图是你缝纫之旅的第一步！