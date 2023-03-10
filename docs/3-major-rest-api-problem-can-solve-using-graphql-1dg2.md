# 使用 GraphQL 可以解决 3 个主要的 REST API 问题

> 原文：<https://dev.to/mshossain110/3-major-rest-api-problem-can-solve-using-graphql-1dg2>

在这里我不打算谈论 GraphQL 是什么和如何做。我想，有数百篇关于它的文章。我想分享一下，我在学习和工作时间里所理解的。如果我错了，请纠正我。

当脸书在 2012 年构建其移动应用程序时，其 REST API 遇到了一些问题，导致他们创建了 GraphQL。我们在设计 REST API 时也面临着这些问题。即:

*   性能差
*   很多端点
*   数据提取过多或不足
*   理解 API 有困难
*   API 版本控制难度

With GraphQL, we get a lot of new features that give you superpowers when you are building your APIs.

## 最小化数据抓取

在 REST API 中，过度提取或提取不足是一个性能问题。基本的 REST APIs 总是返回固定的数据结构。我们应该获取我们需要的数据。使用 GraphQL，我们可以最大限度地减少数据读取，提高 REST API 的性能，尤其是在网络连接速度较慢的情况下。

[![Graphql rest api](img/d85a4b487c9b0c10671f9f7010032884.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qRj567dL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mshossain.me/wp-content/uploads/2019/04/1_odlr0di3IFkR9RxagoTL-g.png)

还有一个信息提取不足的问题。为了获取关系数据，我们需要在服务器中有太多的请求，或者有不必要数据的大型对象。这不仅损害了我们应用程序的性能，还占用了空间。在这种情况下，GraphQL 可以帮助解决这个问题。

## 最小化应用端点

为了获取 REST API 数据，我们都使用 CRUD 系统。对于每个特定的资源，都有 GET、POST、PUT、DELETE、OPTION 请求方法来控制数据。因此，在现实世界的应用程序中，我们最终会有很多资源的很多端点。很难记住或控制这些终点。如果你正在处理像 facebook 或 twitter 这样的大规模应用，维护这些端点是非常困难的。
[![GraphQL endpoint request](img/b2baa0be326514a78e0720d2354d2705.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gkKPGFvO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mshossain.me/wp-content/uploads/2019/04/graphql-request.jpg) 
这个最大的问题可以用 GraphQL 解决。GraphQL 只有一个端点，只是让整个服务器成为一个可以回复所有数据问题的定制端点。你只需要类型或突变来控制你的资源。

## 优化 API 版本

通用 REST API 的最大问题之一是版本控制。出于不同原因和添加新特性，我们需要多次更改后端 API。为了保持牢不可破的前端和 API 用户，我们需要在 REST API 中做广告。

在 GraphQL 中不需要它，因为您是通过添加新类型或删除旧类型来获取 API 的。既然是抓取，我们需要多少数据，在大多数情况下都是无法破解的。
同样，只有一个端点我们不需要端点像

```
https://api.example.com/v1/users/12312
https://api.example.com/v2/users/12312

```

## 结论

GraphQL 初看起来可能很复杂，因为它是一种跨越现代开发许多领域的技术。但是如果你花时间去理解潜在的概念，我想你会发现很多都是有意义的。
同意吗？不同意？有问题吗？请在评论中告诉我。如果你喜欢这篇文章，请考虑并分享它！想要联系我，只需关注 [twitter](https://twitter.com/mshossain110)