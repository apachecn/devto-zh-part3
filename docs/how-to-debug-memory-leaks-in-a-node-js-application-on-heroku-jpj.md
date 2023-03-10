# 如何在 Heroku 上调试 Node.js 应用程序中的内存泄漏

> 原文：<https://dev.to/sqreenio/how-to-debug-memory-leaks-in-a-node-js-application-on-heroku-jpj>

调试内存泄漏很少是小菜一碟，尤其是当它们只发生在生产中的时候。我发现在 Heroku 上调试 Node.js 应用程序内存泄漏的最好方法是分析堆转储。

在生产环境中获取这样的堆转储可能很困难，因为可能很难使用调试器远程连接到生产实例。

在本文中，我们将介绍从正在运行的 Heroku dyno 获取和分析堆转储所需的步骤。只要有可能执行类似的操作，这个方法也可以在其他平台上工作。

要获得堆转储，我们需要:

*   确保 Node.js 进程有调试器侦听
*   将 Chrome 开发工具连接到 Node.js 进程
*   收集堆转储并将其下载到本地

## 启用 Node.js 检查器

在我们分析任何东西之前，我们需要确保我们有一个调试器在监听。有两种方法可以在 Node.js 流程上启用检查器:

### 解决方案 1:更改启动命令

默认情况下，Heroku 通过运行`npm start`来启动 Node.js 应用程序。通常，这将调用应用程序的`package.json`中定义的脚本: