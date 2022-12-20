# CORS 教程:跨源资源共享指南

> 原文：<https://dev.to/auth0/cors-tutorial-a-guide-to-cross-origin-request-sharing-4hhe>

在本文中，我们将了解 CORS，需要它的环境，它提供的好处，以及如何配置 Node + Express 应用程序来支持 CORS。如果您愿意，可以从 GitHub 获取附带的源代码！

跨来源资源共享(CORS)是一种协议，它使浏览器客户端上运行的脚本能够与来自不同来源的资源进行交互。这很有用，因为由于 XMLHttpRequest 和 fetch 遵循同源策略，JavaScript 只能调用与脚本运行位置位于同一原点的 URL。例如，如果一个 JavaScript 应用程序希望对运行在不同域上的 API 进行 AJAX 调用，由于同源策略，它将被阻止这样做。

但是为什么这是必要的，它是如何工作的？

[继续阅读🛠](https://auth0.com/blog/cors-tutorial-a-guide-to-cross-origin-request-sharing/?utm_source=dev&utm_medium=sc&utm_campaign=tut_cors)