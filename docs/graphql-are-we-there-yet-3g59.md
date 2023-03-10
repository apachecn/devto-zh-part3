# GraphQL，我们到了吗？

> 原文：<https://dev.to/saulsilver/graphql-are-we-there-yet-3g59>

Shopify 是一个电子商务平台，有很酷的 API(比其他的好很多)。他们的 GraphQL 已经过了 Beta 版，现在稳定了，还在维持 REST。

我们的应用程序与 Shopify REST API 一起工作，最近，我们一直在考虑迁移到 GraphQL。为什么？GraphQL 更新、更酷，而且可能会解决 REST API 限制带来的性能问题。

我开始研究他们的文件，虽然还在进行中，但已经足够好了。

### graph QL 的概要

根据它的名字，你可以想象一个有*个节点*、*条边*和*条连接*的图。我不会深入讨论这个问题，但是你可以查看这篇好文章来了解更多关于 GraphQL 的信息。

1.  GraphQL 没有通话限制，但存在*费用*费率。成本通常取决于多少字段和连接的成本，这在上面提到的文章中有很好的解释。成本率是为了避免服务器因过载或攻击而崩溃。

2.  GraphQL 也存在分页，称为 [*光标分页*](https://blog.apollographql.com/understanding-pagination-rest-graphql-and-relay-b10f835549e7) ，和我用 REST 体验过的有点不一样。~~虽然 GraphQL 的分页可以在 REST APIs 中实现，但我还没见过。如果你知道的话，请告诉我！~~ (Slack & Django do，感谢[@押韵](https://dev.to/rhymes)！)
    -更新 2019 年 8 月 11 日-
    Shopify 刚刚发布他们正在从基于页面的分页转向基于光标的分页。
    —

### 决策步骤

根据我的发现和其他人的评论，我对从 REST 迁移到 GraphQL 持怀疑态度。我看过一些评论，说 GraphQL 对某些进程来说很强大，但对其他进程来说仍然很弱。

在我们的应用中混合使用这两种 API 可以获得两种 API 的优点，但是会增加代码库的复杂性。我宁愿避免复杂性，也不愿受任何一种 API 的限制。

我觉得 Shopify 的 GraphQL 建立在他们的 REST API 设计之上，限制了 GraphQL 的优势。我的怀疑与日俱增，最终，我偶然发现了这篇文章。基于一个简单的 Q & A，您可以看到从 REST 迁移到 GraphQL 是否值得。当然，这个决定最终取决于你，但是我很肯定我有很好的理由持怀疑态度。

### 结论

最终，我们决定暂时搁置迁移，看看 Shopify GraphQL 将来会给我们带来什么。
顺便提一下，我认为为了制作一个好的 GraphQL API，API 设计应该被认为是一个图(带有边&节点),以使它更有效。