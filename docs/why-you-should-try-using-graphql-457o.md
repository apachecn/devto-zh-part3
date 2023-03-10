# 为什么应该尝试使用 GraphQL

> 原文：<https://dev.to/allenwoods/why-you-should-try-using-graphql-457o>

[![GraphQL icon courtesy of Wikipedia](img/986a63200872c3f1f006322727c2f0d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3Y2IN2Jh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/thumb/1/17/GraphQL_Logo.svg/450px-GraphQL_Logo.svg.png)

通常在创建全栈应用时，后端开发会消耗大量时间——通常比预期的要多。

数据查询必须是特定的，这通常意味着在与 RESTful API 交互时，我们需要的数据会有一些过剩或不足。这可能会进一步影响用户体验和应用程序性能，因为在大规模部署时，低效的请求会带来额外的开销。

如果我们不必为了检索或更新特定信息而小心翼翼地翻阅我们的数据或删除大量数据，那就太好了。

幸运的是，一项技术就是为此而设计的，它的名字叫做 [`GraphQL`](http://graphql.org) 。今天我们将从一个好奇的新手的角度来回顾它对我们下一个项目的潜在影响。

### 简明问题内的详细数据

[![GraphQL lets you ask for only what you need](img/5c30fff7270f870ddb11c16d7b9a72c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JHvhxaYs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pluspng.com/img-png/png-pointing-finger-pointing-finger-png-image-43096-878.png)

GraphQL 是由[脸书](//www.facebook.com)开发的开源查询语言，于 2015 年发布。它的主要前提是为客户端请求提供一种方式来询问关于 API 中数据的简明问题。

*   只有你需要的

实现 GraphQL 的一个最好的方面是，您可以只检索或更新您想要与之交互的信息，同时保留如何构建数据存储的自由。

*   数据类型化

工作流的另一个极好的补充是通过 GraphQL 发送和接收的更强的数据类型，并配有调试工具，使开发总体上更容易。

*   数据库不可知

GraphQL 可以用于关系和非关系数据库，比如`PostgreSQL`和`MongoDB`，这绝对是一个不容忽视的优势。

### 不是中间件

在实践中，GraphQL 本质上充当了数据库的一种转换器，客户端通信通过它进行中继，而不是直接传递给数据存储。

从短期来看，这给项目增加了一层规划，需要分配时间来设计请求本身。然而，从长远来看，直观的语法提供了更大的自由度、更快的周转和更准确的调试。

简而言之，仅工作流中的附加值就足以弥补实施它所花费的时间。

让我们更仔细地看一下 GraphQL 的结构概述，以便更好地理解它的功能。

### 服务

通过 GraphQL 返回的所有数据都是有效请求的值，对应于匹配特定字段的键。这些键和值存在于一个特殊的*“root”*对象上，该对象用于向客户端传递数据。

服务可以用任何语言编写，允许无缝集成到您的服务器上——即使您的应用已经部署。

GraphQL 与数据交互的方式是通过`services`，定义为由包含一个或多个`fields`的数据`type`组成的块。然后，这些类型和字段与用于处理信息的回调函数相关联。例如，下面的结构:

```
type Query {
  opponent: Player
}

type Player {
  id: ID!
  name: String!
}

function Query_opponent(request) {
  return request.auth.player;
}

function Player_name(player) {
  return player.getName();
} 
```

要运行上面的代码，请求将采用以下形式:

```
{
    opponent {
        name
    }
} 
```

虽然语法看起来与`JSON`相似，但需要注意的是，上面的 GraphQL 语法是以多行字符串格式编写的，通常由反勾号(也称为*“庄重音调符号”*)包围，并且必须在翻译期间由包含的服务器运行时解析。

GraphQL 的定义和使用涉及许多主题，兔子洞可能会很深。

现在，我们最感兴趣的是对主要的构建模块有一个大致的了解。让我们从查询和突变开始。

### 查询和突变

查询是使用 GraphQL 更直观地编写和更动态地执行的`GET`请求。相反，突变实际上是`POST`、`PUT`或`PATCH`请求。

为此，原则上，所有通过 GraphQL 发出的请求在功能上与效率较低的 RESTful 请求没有什么不同。然而，GraphQL 使得将条件嵌入请求本身成为可能，并且将数据更紧密地聚合到单个数据返回中。

下面是 GraphQL 中查询和变异的一个例子:

```
{
  hero {
    name
    # Queries can have comments!
    friends {
      name
    }
  }
}

mutation CreateReviewForEpisode($ep: Episode!, $review: ReviewInput!) {
  createReview(episode: $ep, review: $review) {
    stars
    commentary
  }
} 
```

> 资料来源:[图表 SQL 文件](https://graphql.github.io/learn/queries/)

### 模式和类型

模式有效地作为 GraphQL 服务的规则集运行，从而使用给定服务块的主定义来验证返回的数据。

类型是您定义的对象类型，用于描述您期望从服务器返回的数据的属性。这些是用于引用包含数据字段的给定服务块的容器对象。

下面是一个如何编写 GraphQL 对象类型模式的例子:

```
schema {
  query: Query
  mutation: Mutation
}

query {
  hero {
    name
  }
  droid(id: "2000") {
    name
  }
} 
```

> 资料来源:[图表 SQL 文件](https://graphql.github.io/learn/schema/#the-query-and-mutation-types)

### 付诸实践

这一概述的粗略性质是为了给出实现 GraphQL 的可能性的印象，而不是一个深入的指导性演练。

为了提供一个可以实现的快照，让我们跳到一个例子，看看所有特性在实践中是什么样子的。

```
query Hero($episode: Episode, $withFriends: Boolean!) {
  hero(episode: $episode) {
    name
    friends @include(if: $withFriends) {
      name
    }
  }
}

query HeroForEpisode($ep: Episode!) {
  hero(episode: $ep) {
    name
    ... on Droid {
      primaryFunction
    }
    ... on Human {
      height
    }
  }
} 
```

> 来源:
> [GraphQL 文档-指令](https://graphql.github.io/learn/queries/#directives)
> [GraphQL 文档-内联片段](https://graphql.github.io/learn/queries/#inline-fragments)

关于如何在 MongoDB 环境中使用`MERN`栈来使用 GraphQL 的一个很好的例子可以在 YouTube 上正在进行的 Code Realm 系列教程中找到:
[https://www.youtube.com/embed/HKqbBrl_fKc](https://www.youtube.com/embed/HKqbBrl_fKc)

### 结论

我希望这个概述已经让您对 GraphQL 在您的下一个项目中能做什么有了一个概念，并且您的 RESTful APIs 将会更加轻松。

一旦我有机会在我的下一个应用程序设计中利用这项技术，我期待着编写一个深入的演练。