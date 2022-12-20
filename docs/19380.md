# 使用 Node.js 生成和存储 API 键的最佳实践？

> 原文：<https://dev.to/nagarjun/best-practices-to-generate-and-store-api-keys-using-nodejs-534d>

我正在构建一个主要服务于 API 的 Node.js 应用程序。这些 API 分为两类:

*   用户 APIs 访问特定的用户端点，如 GET /users/:id/purchases
*   管理 API——允许使用我们的 API 的公司/客户调用管理级端点，如 POST /users(创建一个新用户)

我已经使用 OAuth 访问令牌和刷新令牌来保护用户 API。保护我的管理 API 的最佳实践是什么？更具体地说，我希望实现这样的解决方案-[https://mailchimp.com/help/about-api-keys/](https://mailchimp.com/help/about-api-keys/)。

我们的想法是让我的客户登录我们的管理应用程序来创建 API 密钥，这样他们就可以调用我们的管理 API:

1.  如何生成类似于 MailChimp 生成的密钥？
2.  如何使用 Node.js 在服务器上验证这些键？

我已经找到几十篇关于 jwt 的文章，但是我怀疑它们是否是这个特定用例的正确解决方案。