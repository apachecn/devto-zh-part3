# GraphQL 与 REST:性能

> 原文：<https://dev.to/graphqleditor/graphql-vs-rest-performance-38jb>

这次让我们看看 GraphQL 与 REST 讨论的另一个重要方面——性能。

## graph QL vs REST；性能与可靠性

GraphQL 的主要特点是比传统的 T2 REST API 少说话。GraphQL 将性能视为其最高优先级，而 REST 则专注于将服务可靠性作为其主要目标。即使 REST API 只返回一个基本的部分，它仍然在传输更多的数据，而 GraphQL 总是以尽可能最小的请求为目标。在一个例子中，如果客户端需要一个字段，他们请求它，如果 API 添加了一个新字段，客户端不会得到它，除非它被添加到 GraphQL 查询中。

## 线上没有浪费的比特

你们都见过这样的 API:首先需要`GET /author`，然后通过`GET /author/:id/books/:id`端点分别获取每本书。这会导致 n+1 次查询，这是 REST APIs 中面临一个众所周知的性能问题。虽然 REST API 调用在最终表示形成显示之前被链接在客户端，但在 GraphQL 中，通过使服务器能够在一个查询中组合客户端的所有数据，从而减少了通过网络传输的位数，这一点得到了简化。

```
{  author  (id:  "1")  {  name  book  (id:  "5")  {  title  }  }  } 
```

```
{  "data":  {  "author: {
      "name": "George  R.R.  Martin",
      "book": [
        {
          "title": "A  Dance  with  Dragons"
        }
      ]
    }
  }
} 
```

## 结论

GraphQL 比 [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) 快，因为你可以选择你想要查询的字段，所以请求总是最小的。此外，使用 GraphQL，您可以在一个请求中查询多个实体，并且因为通过网络传输的比特更少，所以您的项目将比使用 [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) 时执行得更快。