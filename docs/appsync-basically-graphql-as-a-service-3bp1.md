# AppSync:基本上是 GraphQL 即服务

> 原文：<https://dev.to/raoulmeyer/appsync-basically-graphql-as-a-service-3bp1>

去年，当我参加一个会议时，我了解到 AWS 阵容中增加了一个新成员。它被称为 AppSync，基本上是一种以无服务器方式托管 [GraphQL](https://graphql.org/) 服务的方式。上个月我做了一点实验，在这篇文章中我想分享我的发现。

TLDR:如果你已经在使用或者想要使用 GraphQL，那么你绝对应该试试 AppSync！

## app sync 为你做什么？

您只需要两件东西就可以开始:一个模式和一些解析器。您上传的模式是一个常规的 [GraphQL 模式](https://graphql.org/learn/schema/)。AppSync 将接受基于此模式的查询和变更。您可以随时更改这个模式，甚至不需要部署新版本，它就会立即开始使用它。

您需要的第二部分是解析器。这些解析器将查询中的字段映射到该字段的实际数据，这通常意味着从一些数据存储中获取数据。您可以将 Lambda 作为解析器，但也可以使用一系列不同的数据库作为数据源。

## 一个基本例子

为了理解这是如何工作的，这里有一个小例子。想象一个像这样的网站。有通过某个 id 识别的用户，这些用户有帖子。下面的模式结合了这些类型和一个为用户获取数据的查询:

```
type  Query  {  user(id:  Int):  User  }  type  User  {  id:  Int!  name:  String!  posts(count:  Int):  [Post]  }  type  Post  {  id:  Int!  title:  String!  content:  String!  } 
```

Enter fullscreen mode Exit fullscreen mode

为了解析用户或帖子，我们将运行以下 lambda 解析器:

```
exports.user = async (event, context) => {
  const body = await fetchUser(event.arguments.id);

  return {
    statusCode: 200,
    body: JSON.stringify(body)
  };
};

exports.posts = async (event, context) => {
  const { id, count } = event.arguments;
  const body = await fetchPostsForUser(id, count);

  return {
    statusCode: 200,
    body: JSON.stringify(body)
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

从这段代码中，我们创建了两个 lambda，然后我们需要将它们与模式中的字段挂钩。为此，我们需要指定请求和响应映射。

请求映射会将查询中提供的数据(例如用户 id)转换为 lambda 中可用的事件变量。响应映射会将我们在 lambda 中给出的响应转换回我们在模式中定义的结构。

对于*帖子*解析器，可能是这样的:

```
Request mapping:

{
    "version" : "2017-02-28",
    "operation": "Invoke",
    "payload": {
        "arguments": $util.toJson({
            "id": $context.source.id,
            "count": $context.arguments.count
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
Response mapping:

#if($ctx.result.statusCode == 200)
    $ctx.result.body
#elseif($ctx.result.statusCode == 404)
    $util.error("Not found")
#else
    $util.error("Error")
#end 
```

Enter fullscreen mode Exit fullscreen mode

您可以在这些映射中添加一些魔法。下面的请求映射将直接查询 DynamoDB，而不是使用 lambda:

```
Request mapping:

{
    "version" : "2017-02-28",
    "operation" : "GetItem",
    "key" : {
        "id" : {
            "S" : "${context.arguments.id}"
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想摆弄一些例子，AWS 提供了[样本设置](https://docs.aws.amazon.com/appsync/latest/devguide/quickstart-launch-a-sample-schema.html)，其中包括你需要开始的一切。

## 为什么要用？

我推荐尝试 AppSync 有几个原因。

### 因为便宜

就像其他无服务器服务一样，您只需为您使用的服务付费，仅此而已。启动和运行该服务没有基本成本。这意味着对于用户基数较小的应用程序来说，这是一种非常经济的运行 GraphQL 服务器的方式。[目前](https://aws.amazon.com/appsync/pricing/)，你每做 100 万个请求就要支付 4 美元，这与 API Gateway 的价格相当，只比最便宜的 AWS Lambda 函数贵 5 倍左右。

### 因为它将处理任何负载

即使是黑色星期五也没关系，你的服务仍然会继续运行。这里没有旋钮可以调整。在这方面，它与 Lambda 非常相似，您只需给它您的 GraphQL 模式，它就会为您运行它。

### 因为耦合到 DynamoDB 或 Lambda 真的很容易

目前，AppSync 为 DynamoDB、RDS、Elasticsearch、Lambda 和 HTTP 提供解析器。对于其中的每一个，您都需要将来自 GraphQL 查询的数据映射到对数据源有意义的格式。对于这些数据源中的每一个，都有清晰的[文档](https://docs.aws.amazon.com/appsync/latest/devguide/resolver-mapping-template-reference.html),这使得入门变得很容易。好的一面是，对于最流行的数据库，你不必创建一个 lambda 来运行一个查询。

### 因为它支持实时订阅

AppSync 附带了许多支持实时订阅的客户端库。这样，客户端将保持与 AppSync 的连接开放，并能够订阅和接收发生的突变的更新。我可以看到它在某些用例中非常强大，你可以在这里阅读更多信息。

## 但是有不好的一面吗？

### 表现

在尝试这一点时，很明显 AppSync 内部有相当多的开销。当连接到一个只返回值的 lambda 时，响应时间很少低于 150 毫秒。在大多数情况下，这可能是一个可以接受的数字。与您自己运行 GraphQL 服务器相比，这看起来确实很多。即使与在许多方面提供类似服务的 API Gateway 相比，它也要慢得多。

### 局限性

AppSync 仍然很新，于去年 4 月开始全面上市。正因为如此，它仍然有一些粗糙的边缘。现在对我来说最大的困难是 RDS 解析器只适用于 Aurora 无服务器版。幸运的是，您仍然可以编写自己的 lambda 来从任何类型的 RDS 中获取数据。

同样值得一提的是，并发性是有限制的。当你想解决一个相同类型的事物列表时，它将最多运行 5 个并发查询/lambda 的/HTTP 调用。根据您的使用情况，您可能会也可能不会遇到这个限制。

### 与数据源的耦合有点令人讨厌

我喜欢使用 Cloudformation 来部署我的堆栈。当您想要使用 Cloudformation 部署 AppSync 堆栈时，还需要在您的堆栈模板中定义所有解析器。这包括您想要执行的任何查询，以及您想要在运行这些查询之前和之后应用的任何业务逻辑。

将查询和逻辑放在堆栈定义旁边并不理想。这使得几乎不可能测试您定义的逻辑。此外，在您的业务逻辑中部署变更变成了堆栈变更。这意味着回滚和金丝雀发布之类的东西不再是无关紧要的了。

即使不使用 Cloudformation，也需要指定模式和解析器之间的映射。为此，AWS 选择使用 [Apache Velocity 模板语言](http://velocity.apache.org/engine/1.7/user-guide.html)。不幸的是，您在那里添加的逻辑将很难测试和调试。虽然这不是一个交易破坏者，但随着产品的老化，它可能会得到改进。

## 结论

AppSync 是对 AWS 产品阵容的一个非常有用的补充。它有一些粗糙的边缘，但它服务于一个清晰且越来越受欢迎的目的。如果您打算用 GraphQL 构建一些东西，我建议您试一试。