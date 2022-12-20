# 轻松的 GraphQL 模式拼接

> 原文：<https://dev.to/stephen/hassle-free-graphql-schema-stitching-d6d>

GraphQL 允许您跨多个数据源构建统一的 API，客户端只需发出一个请求就可以查询这些数据源。最初，这主要用于公开跨多个 REST 源的单个 GraphQL API，但是随着 GraphQL 越来越流行，公开跨多个下游 GraphQL 服务的 GraphQL API 的需求变得更加普遍。

就这样，[模式拼接诞生了](https://blog.apollographql.com/graphql-tools-2-0-with-schema-stitching-8944064904a5)！GraphQL 模式拼接允许您将多个 GraphQL APIs 合并到一个模式中，而无需重新实现所有现有的解析器逻辑。

实际上可能是这样的:

实现该模式的作者服务

```
type  Author  {  id:  ID!  name:  String!  }  type  Query  {  author(id:  ID!):  Author  } 
```

以及实现该模式的图书服务

```
type  Book  {  id:  ID!  title:  String!  authorId:  ID!  description:  String  }  type  Query  {  book(id:  ID!):  Book  booksByAuthorId(id:  ID!):  [Book!]!  } 
```

可以组合到实现该模式的 GraphQL 网关服务中

```
extend  type  Author  {  books:  [Book!]!  }  extend  type  Book  {  author:  Author!  } 
```

给出最终的模式

```
type  Book  {  id:  ID!  title:  String!  author:  Author!  description:  String  }  type  Author  {  id:  ID!  name:  String!  books:  [Book!]!  }  type  Query  {  author(id:  ID!):  Author  book(id:  ID!):  Book  } 
```

这允许您在一个查询中查询给定作者及其所有书籍，或者获取同一作者写的所有书籍作为给定书籍。这很简洁，但是通常需要在 GraphQL 网关中实现映射解析器，知道如何从作者 id 到图书列表以及从图书到作者。不幸的是，网关服务需要了解这些实体或它们之间的关系，而它需要处理的唯一问题是模式拼接。

令人欣慰的是，GraphQL 有一个非常活跃的社区，贡献了很多很棒的库。基于 Apollo 团队与 graphql-tools 的伟大工作，我构建了 T2 merge-remote-graph QL-schemas T3，这是一个 JS 库，用于自动将 GraphQL APIs 拼接在一起，而不需要在拼接服务中实现映射解析器。

以上面的例子为例，我们的作者服务只需要公开与之前相同的模式

```
type  Author  {  id:  ID!  name:  String!  }  type  Query  {  author(id:  ID!):  Author  } 
```

图书服务会公开这个稍微不同的模式

```
type  Book  {  id:  ID!  title:  String!  author:  Author!  description:  String  }  type  Author  {  id:  ID!  books:  [Book!]!  }  type  Query  {  book(id:  ID!):  Book  author(id:  ID!):  Author  } 
```

与上一个示例相同，图书服务拥有图书和作者之间的关系，但现在它以更习惯的方式表达它们。

[merge-remote-graphql-schemas](https://github.com/StephenCodesThings/merge-remote-graphql-schemas)获取这些服务公开的 graph QL 模式，并将它们合并(包括合并类型)到

```
type  Book  {  id:  ID!  title:  String!  author:  Author!  description:  String  }  type  Author  {  id:  ID!  name:  String!  books:  [Book!]!  }  type  Query  {  author(id:  ID!):  Author  book(id:  ID!):  Book  } 
```

在幕后，它创建了一个可执行的 GraphQL 模式，该模式自动将传入的查询转换为每个服务都知道如何解析的子查询，然后将这些结果合并成一个结果。缝合服务不需要添加映射解析器或类型定义，节省了开发工作，并允许我们很好地分离我们的关注点。

如果你对模式拼接感兴趣，请查看 GitHub repo 并告诉我你的想法！