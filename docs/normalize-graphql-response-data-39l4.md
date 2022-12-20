# 规范化 GraphQL 响应数据

> 原文：<https://dev.to/monojack/normalize-graphql-response-data-39l4>

我们都喜欢 GraphQL，并且想要使用它。有大量的库和客户端可以帮助我们轻松做到这一点，但是仍然有一个问题...如何持久存储这些数据？

是的，当响应反映了我们所要求的精确结构时，一切都很好，但是我们不希望这样缓存它，不是吗？我们可能需要数据的一个规范化版本，我们可以将它保存到我们的存储中，并有效地读取/修改它。Flux 或 Redux 存储最适用于规范化数据，也有 GraphQL 客户端可以用来执行对本地缓存/状态的查询( [blips](https://github.com/monojack/blips) 或 [apollo-link-state](https://github.com/apollographql/apollo-link-state) )，在这种情况下，我们肯定需要持久化规范化数据。

graphqlnormizr 是简单、快速、轻量级的，它提供了实现这一点所需的所有工具，唯一的要求是你要为所有节点包含 *id* 和 *__typename* 字段(但是如果你太懒或者你想保持你的源代码简洁，它可以为你做到这一点)。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[mono jack](https://github.com/monojack)/[graph QL-normalizr](https://github.com/monojack/graphql-normalizr)

### 规范化 GraphQL 响应，以便在客户端缓存/状态中保持

<article class="markdown-body entry-content container-lg" itemprop="text">

# **graphql-normalizr**

[![Build Status](img/3ec24bec8e156731b477c47d78a50f89.png)](https://travis-ci.org/monojack/graphql-normalizr)[![npm version](img/5243256af9e68daa5e0217d8d6da26c0.png)](https://www.npmjs.com/package/graphql-normalizr)[![npm downloads](img/f3eafced9e06d3eaabeb6c533dfff080.png)](https://www.npmjs.com/package/graphql-normalizr)[![minified size](img/e380528f374fa0a9579991bfc9cc206c.png)T11】](https://bundlephobia.com/result?p=graphql-normalizr@latest)

规范化 GraphQL 响应，以便保存在客户端缓存/状态中。

> 与 [normalizr](https://github.com/paularmstrong/normalizr) 毫无关系，只是无耻地利用了它的流行。此外，“规格化的 T2”Er 也被采用...

**TL；博士**:转换:

```
{
  "data": {
    "findUser": [
      {
        "__typename": "User"
        "id": "5a6efb94b0e8c36f99fba013"
        "email": "Lloyd.Nikolaus@yahoo.com"
        "posts": [
          {
            "__typename": "BlogPost"
            "id": "5a6efb94b0e8c36f99fba016",
            "title": "Dolorem voluptatem molestiae",
            "comments": [
              {
                "__typename": "Comment",
                "id": "5a6efb94b0e8c36f99fba019",
                "message": "Alias quod est voluptatibus aut quis sunt aut numquam."
              },
              {
                "__typename": "Comment",
                "id
```

…</article>

[View on GitHub](https://github.com/monojack/graphql-normalizr)