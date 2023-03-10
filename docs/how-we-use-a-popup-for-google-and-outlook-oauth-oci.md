# 我们如何为 Google 和 Outlook OAuth 使用弹出窗口

> 原文：<https://dev.to/dinkydani21/how-we-use-a-popup-for-google-and-outlook-oauth-oci>

在[别管我](https://leavemealone.xyz)我们使用谷歌和微软 OAuth 进行用户登录。为此，我们将用户重定向到相关的登录页面，用户输入他们的详细信息，然后被重定向回我们的站点并登录。一个不幸的结果是，我们的分析报告了大量来自“accounts.google.com”和“login.microsoft.com”的推荐流量。

[![How we use a popup for Google and Outlook OAuth](img/fd306f95d99d23f485ebe2ca3c2e6918.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iVB6NjPJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.squarecat.io/conteimg/2019/04/login-popup.jpg)

为了解决这个问题，如果我们能为 OAuth 流打开一个新的窗口或弹出窗口，而不是重定向，会更好。此外，对于用户来说，这可能是比被重定向更好的体验。

# 我们是如何实现的

我们使用 [Passport](http://www.passportjs.org/) 进行身份验证，因此当用户登录后被引导回我们的应用程序时，URL 包含一些我们需要的参数，包括我们用来在服务器上对他们进行身份验证的令牌。

因为我们想要使用弹出窗口，所以在流程中间需要一个额外的步骤来捕获重定向、检索 URL 参数、关闭弹出窗口，并在打开的窗口(不是弹出窗口)中使用令牌。

[![How we use a popup for Google and Outlook OAuth](img/703282a3b7f277f8c6a0ea46f16b88a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0xRtPF6y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.squarecat.io/conteimg/2019/04/oauth-popup.png)

我们允许用户使用 Google 和 Outlook 登录，两者的实现是相同的。为了便于阅读，这个例子将使用谷歌。

### 第一步:打开弹出窗口

要打开一个新窗口，我们使用带有 passport 登录 URL 的 [Window.open()](https://developer.mozilla.org/en-US/docs/Web/API/Window/open) (在我们的例子中是/auth/google ),这将在一个新窗口中打开“登录，让我单独和 google 在一起”页面。我们也给窗口一个名字，并传递我们希望它拥有的[请求的特性](https://developer.mozilla.org/en-US/docs/Web/API/Window/open#Window_features)。

我们分配窗口引用并记录以前的 URL，这样，如果用户再次尝试单击登录按钮，即使是针对不同的提供商，也会使用或聚焦同一个窗口。我们不希望不同供应商的两个弹出窗口四处浮动，造成混乱。

最后，我们为消息添加了一个事件监听器，因为 popup 将在完成后发送 URL 参数和 auth token。