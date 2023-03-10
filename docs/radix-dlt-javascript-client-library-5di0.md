# DLT - JavaScript 客户端库

> 原文：<https://dev.to/radixdlt/radix-dlt-javascript-client-library-5di0>

# 简介

用于与 [Radix](https://www.radixdlt.com/) 分布式账本交互的 JavaScript 客户端库。

要了解该库的主要组件以及它们是如何组合在一起的，请阅读这篇[博客文章](https://www.radixdlt.com/post/introducing-the-radix-javascript-library)。

> 注意:这个库和网络本身目前处于 Alpha 开发阶段。请在 [GitHub 问题跟踪器](https://github.com/radixdlt/radixdlt-js/issues)中报告任何问题。

# 特性

-完整的类型脚本支持
-使用[RxJS](https://rxjs-dev.firebaseapp.com/)T3】遵循反应式编程模式-使用[椭圆](https://github.com/indutny/elliptic)库
的密码术-在分片环境中自动管理到 Radix [Universe](https://docs.radixdlt.com/alpha/learn/glossary#universe) 的连接
-通过 WebSockets 使用 RPC 与 Radix 网络通信
-读取任意地址中的[原子](https://docs.radixdlt.com/alpha/learn/glossary#atoms)
-将原子写入分类帐
-使用 ECIES 进行端到端数据加密

# 安装

使用您喜欢的软件包管理器安装库:
或`npm install radixdlt --save`

# 示例应用

- [使用 Vue.js 的前端示例](https://github.com/radixdlt/radixdlt-js-skeleton)
- [Express.js 服务器示例](https://github.com/radixdlt/radixdlt-js-server-example)

# 打造

要使用首选的软件包管理器构建库:

`yarn install && yarn build`或`npm install && npm build`

# 测试

用`yarn test`运行测试。

# 已知问题

有角的

显然，在 Angular 6+版本上，webpack 的节点模块`polyfills`并没有捆绑。解决您的加密、路径等问题。去…

`node_modules/@angular-devkit/build-angular/src/angular-cli-files/models/webpack-configs/browser.js`并做如下修改:

`node: { crypto: true, path: true }`

> 注意:这不是一个可重现的修复。如果您将模块安装在新位置，您将丢失此更改。

# 加入 Radix 社区

[电报](https://t.me/radix_dlt)普通聊天
[不和](https://discord.gg/7Q7HSZZ)开发者聊天
[Reddit](https://reddit.com/r/radix) 普通讨论
[论坛](https://forum.radixdlt.com/)技术讨论
[推特](https://twitter.com/radixdlt)发布公告
[电子邮件](https://radixdlt.typeform.com/to/nyKvMV)简讯每周更新
邮件[hello@radixdlt.com](//hello@radixdlt.com)普通查询