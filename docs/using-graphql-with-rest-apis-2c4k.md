# 将 GraphQL 与 REST APIs 结合使用

> 原文：<https://dev.to/smizell/using-graphql-with-rest-apis-2c4k>

人们选择 GraphQL 是因为它让他们忘记了所有的细节。在使用 GraphQL 生态系统的强大模式和构建良好的工具后，很难回到现代 REST 方法。但也许我们不必在 GraphQL 和 REST 之间做出选择。也许我们可以得到两种方法的好处。

为了探索这一点，我进行了概念验证。我把它叫做[可图形化的 JSON](https://github.com/smizell/graphablejson) 。它是一个 JavaScript 库，接受一个 GraphQL 查询，并通过链接从 REST API 中缓慢地获取所有请求的数据。通过在 GraphQL 的查询语法中指定所需的数据，它允许 API 消费者超越资源、URL、HTTP 方法和状态代码。所有这些都发生在客户端，而不是将查询发送到某个地方的 API。这是一个需要记住的重要区别。

# 工作原理的一个例子

我觉得用例子来解释这种方法是最容易的。对于这些例子，我们将使用一个 [order API](https://graphablejsonapi.glitch.me/) ，并且我们将编写一个 GraphQL 查询来从中获取数据。这个 API 是一个没有 GraphQL 端点的 REST API，所以 Graphable JSON 库必须自己想办法获取数据。

该 API 由几个不同的示例组成，用于表示订单数据。对于每个例子，我们的 GraphQL 查询都是相同的。这将表明，我们可以有不同类型的响应和数据表示，并在不破坏客户端的情况下发展 API。我们还将看到客户端开发人员如何指定他们想要的数据，而不用担心 REST API 的细节。

这是我们获取订单数据的 GraphQL 查询。

```
{
  order {
    order_number
    total
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 没有订单响应

我们将从第一个例子的[开始，它是一个没有订单数据的空对象`{}`。](https://graphablejsonapi.glitch.me/examples/example0)

由于没有订单，查询的结果将是:

```
{ "order": [] } 
```

Enter fullscreen mode Exit fullscreen mode

Graphable JSON 将以同样的方式处理`null`，这意味着我们永远不用担心`null`和`undefined`的值。数据的形状总是相同的。

## 单个订单的响应

[第二个例子](https://graphablejsonapi.glitch.me/examples/example1)有一个单。该库将首先查找单个值，如果该值存在，则生成该值。

```
{
  "comment": "A single embedded order object",
  "url": "https://graphablejsonapi.glitch.me/examples/example1",
  "order": {
    "url": "https://graphablejsonapi.glitch.me/orders/1000",
    "order_number": "1000",
    "total": 150,
    "unit": "USD"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行查询时，我们只得到我们请求的数据。

```
{
  "order": [
    {
      "order_number": "1000",
      "total": 150,
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

尽管只有一个订单，但我们在结果中看到了一个由`order`项组成的数组。在继续之前，有几件事情需要提及。

首先，可图形化的 JSON 库利用了[异步生成器](https://jakearchibald.com/2017/async-iterators-and-generators/)。不要太深入这些细节，这意味着它将返回一个可以产生许多值的函数，而不是返回一个值。这个小魔术让我们以同样的方式处理零值、单值和——我们将在下一节看到——多值。

第二，用单数单词`order`指代数组可能会让人觉得奇怪。最好将每个属性视为描述给定对象和对象的一个或多个值之间的关系。返回的对象可以有零个或多个`order`项，尽管在这个上下文中只有一个`order`关系的概念。以这种方式处理关系，让我们发展 API，并且当我们需要扩展相关条目的数量时，不要破坏客户端。

目标是在客户机中返回相同形状的数据，即使 API 可能会改变数据的表示方式。将图形化 JSON 的响应与 API 数据的表示分离开来是实现这一想法的关键。

## 多个订单在响应

为了简洁起见，我将第三个例子缩短了。它在单个响应中显示多个订单。

```
{
  "comment": "All order objects embedded",
  "url": "https://graphablejsonapi.glitch.me/examples/example2",
  "order": [
    {
      "url": "https://graphablejsonapi.glitch.me/orders/1000",
      "order_number": "1000",
      "total": 150,
      "unit": "USD"
    },
    {
      "url": "https://graphablejsonapi.glitch.me/orders/1001",
      "order_number": "1001",
      "total": 25,
      "unit": "USD"
    },
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所料，我们得到了与上面相同的形状，只是订单更多。

```
{
  "order": [
    {
      "order_number": "1000",
      "total": 150,
    },
    {
      "order_number": "1001",
      "total": 25,
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

## 关联订单

当我们想给每个订单一个自己的 URL 时会发生什么？我们如何提供缓存来自 API 的全部或部分数据的方法？我们这样做的方法是通过[在响应](https://restfuljson.org/)中添加链接，并让图形化的 JSON 客户端跟随这些链接。考虑我们上面的“单一订单”例子。我们可以用一个`order_url`属性来链接订单，而不是嵌入订单。

```
{
  "comment": "Links to each order rather than embedding",
  "url": "https://graphablejsonapi.glitch.me/examples/example3",
  "order_url": "https://graphablejsonapi.glitch.me/orders/1000"
} 
```

Enter fullscreen mode Exit fullscreen mode

Graphable JSON 将返回与“单一订单”示例相同的响应。它是这样工作的。图书馆先找`order`。如果没有找到，它会寻找`order_url`或`orderUrl`。如果找到，它请求链接。在这两种情况下，查询的结果是相同的。这也适用于多个链接。

```
{
  "comment": "Links to each order rather than embedding",
  "url": "https://graphablejsonapi.glitch.me/examples/example3",
  "order_url": [
    "https://graphablejsonapi.glitch.me/orders/1000",
    "https://graphablejsonapi.glitch.me/orders/1001",
    "https://graphablejsonapi.glitch.me/orders/1002",
    "https://graphablejsonapi.glitch.me/orders/1003"
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

该库将请求每个`order`链接，并提供响应，就好像它包含在内一样。

## 收集订单

最后，有时我们需要在 API 中对数据进行分页。大多数 GraphQL 方法都要求消费者处理查询中的分页。Graphable JSON 通过查找数据集合并跟踪链接(如果存在的话)来进行分页，这是自动发生的。

在我们的[示例](https://graphablejsonapi.glitch.me/examples/example3)中，如果`order`值包含一个`$item`属性，它将对每一项做出响应，并跟随一个`next`链接(如果存在的话)。下面是一个带有订单链接的响应，但这次是一个订单集合。

```
{
  "comment": "Links to a paginated order collection",
  "url": "https://graphablejsonapi.glitch.me/examples/example4",
  "order_url": "https://graphablejsonapi.glitch.me/ordersCollection"
} 
```

Enter fullscreen mode Exit fullscreen mode

链接订单集合如下所示。

```
{
  "$item": [
    {
      "url": "https://graphablejsonapi.glitch.me/orders/1000",
      "order_number": "1000",
      "total": 150,
      "unit": "USD"
    }
  ],
  "next_url": "https://graphablejsonapi.glitch.me/ordersCollection?page=2"
} 
```

Enter fullscreen mode Exit fullscreen mode

该库将返回所有项目，然后移动到下一页，直到不再有任何`next`链接。像上面所有的例子一样，这导致了相同的数据形状，所有都使用相同的查询。所有这些对客户端开发人员都是隐藏的。

# 这种方法的好处

虽然 Graphable JSON 没有解决过蚀刻和欠蚀刻的问题，但它确实提供了其他好处，这些好处可能超过了这个问题。

1.  API 设计者可以考虑数据的关系，而不是数据的严格结构。
2.  API 开发人员可以根据技术需求发展 API，而不会破坏客户端实现。
3.  API 开发人员可以根据客户需求优化响应。
4.  API 消费者不必拼凑资源来理解 API 的相互联系——链接指明了方向。
5.  API 中的任何数据都可以被赋予一个 URL，这样就可以在以后刷新更小批量的数据，而不需要请求更大的查询。
6.  客户端能够根据响应中的缓存设置缓存数据。对于浏览器来说，这种实现是免费的。

可图形化的 JSON 只是一个概念证明。但也许它会激发其他人的想法。REST 有很多好处，当我们转向 GraphQL 之类的东西时，我们会失去一些好处。也许有办法两全其美。