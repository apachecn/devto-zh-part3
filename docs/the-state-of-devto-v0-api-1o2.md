# 开发到 v0 api 的状态

> 原文：<https://dev.to/alfredosalzillo/the-state-of-devto-v0-api-1o2>

为了防止 web scraper 在 dev.to 上的滥用，我想分享我对 dev.to v0 api 的了解。

## 文章

*   [https://dev.to/api/articles](https://dev.to/api/articles)获得最近 30 篇带有标签职业、讨论和生产力的文章列表(这似乎是默认的标签列表)
*   [https://dev.to/api/articles?tag=react](https://dev.to/api/articles?tag=react)获取最新的 30 篇带有 react 标签的文章
*   https://dev.to/api/articles?page=23&tag = react 获取第 23 页上带有 react 标签的 30 篇文章
*   [https://dev.to/api/articles?username=alfredosalzillo](https://dev.to/api/articles?username=alfredosalzillo)获取用户名为 alfredosalzillo 的用户的最新 30 篇文章

## 用户

*   [https://dev.to/api/users/by_username?url=alfredosalzillo](https://dev.to/api/users/by_username?url=alfredosalzillo)获取用户名为 alfredosalzillo 的用户的信息
*   [https://dev.to/api/users/149157](https://dev.to/api/users/149157)获取 id 为 149157 的用户的信息
*   https://dev.to/api/users/?state=follow_suggestions 获得以下建议列表
*   [https://dev.to/api/users/?state=sidebar_suggestions&tag = react](https://dev.to/api/users/?state=sidebar_suggestions&tag=react)我现在不知道这到底是做什么
*   https://dev.to/api/users/by_username 得到一个用户名为空的用户，我认为这是一个错误。

# 标签

*   [https://dev.to/api/tags](https://dev.to/api/tags)获得最新的 10 个标签列表
*   [https://dev.to/api/tags?page=2](https://dev.to/api/tags?page=2)获取第 2 页的 10 个标签列表