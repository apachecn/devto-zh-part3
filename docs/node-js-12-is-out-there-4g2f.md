# 节点 JS 12 就在那里

> 原文：<https://dev.to/teodeleanu/node-js-12-is-out-there-4g2f>

当您需要了解 Node.js 的新信息时，您从哪里获得信息？当新事物出现时，被告知和知道是好的。
比如新的更新、补丁、安全问题、漏洞、泄露的 npm 包或全新的功能？

我会告诉你我从哪里得到一些信息:

1.  [中间节点基础](https://medium.com/@nodejs)
2.  [Node js 推特账号](https://twitter.com/nodejs)
3.  贝丝·格里戈斯——代码节点维护者之一
4.  [麦可·道森](https://twitter.com/mhdawson1) - NodeJS 社区领导

事情是你不仅会知道什么出来了，但你会知道为什么要升级。在其中一次更新中，我们通过简单地更新库和 Node.JS，使后端服务响应速度提高了 100%。

让我们来看看上一个版本发布了什么:

1.  **V8 获得升级** : V8 更新到 V8 7.4 异步堆栈跟踪，参数不匹配的调用更快，**更快的 await 和更快的脚本解析**。
2.  **默认支持 TLS 1.3**-如果需要，可以禁用。这是一个重要的安全措施。
3.  **将默认的 http 解析器切换到 llhttp**——不再是实验性的。在升级之前，检查您是否在代码中使用了 http 解析器。
4.  **使原生模块更容易**——改进对此的支持
5.  工作线程(Worker Threads)-虽然这是一个老特性，但它可以在运行时不带标志地使用
6.  **堆转储和诊断报告** -不再需要使用第三方库，您可以在不更改生产代码的情况下诊断您的应用。
7.  **启动时的速度**——节点 12 在构建时提前为内置库生成代码缓存。这使得主线程的启动时间加快了大约 30%。
8.  **新的编译器和平台最低要求** -代码库现在要求在 macOS 和 Windows 之外的平台上最低要求 GCC 6 和 glibc 2.17。

Windows 最低要求与 Node.js 11 保持一致，至少需要 Windows 7、2008 R2 或 2012 R2 以及 Visual Studio 2017 的最低编译器。需要编译 Node.js 的 macOS 用户将需要最低 Xcode 8，nodejs.org 上提供的 Node.js 二进制文件将只支持最低 MAC OS 10.10“Yosemite”。

更多详细信息请访问 Node.js [BUILDING.md](https://github.com/nodejs/node/blob/v12.x/BUILDING.md#platform-list) 。

从这篇[文章](https://medium.com/@nodejs/introducing-node-js-12-76c41a1b3f3f)中得到灵感

文章首次发表于 Appseed.us 博客:
[https://blog.appseed.us/node-js-12-is-out-there/](https://blog.appseed.us/node-js-12-is-out-there/)