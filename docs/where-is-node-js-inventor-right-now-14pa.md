# Node.js Inventor 现在在哪里？

> 原文：<https://dev.to/realabbas/where-is-node-js-inventor-right-now-14pa>

"策划者，有计划的人"

Node.js 的创造者在对 javascript 世界做出无可挑剔的贡献后，经过大量的努力，又创造了另一个前沿产品。近 10 年后，Dahl 解构了 Node.js 中仍然存在的错误，重新推出了一个用 Rust/Go 编写的新平台，该平台在安全的沙盒环境中与 TypeScript & Google V8 运行时引擎一起工作。
[![Nodejs](img/c78e29476d5f3901927e51ef8275e39d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--5uPp5WYL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/00hsooyi9hmazjydiaze.png)

从他第一次演讲开始

Ryan Dahl:原始 Node.js 演示文稿(2009 年)

"...V8: Google
libev:事件循环库
libeio:线程池库
http-parser:一个 ragel HTTP 解析器
evcom:libev 之上的流套接字库
udns:非阻塞 dns 解析器……”
详细了解原 Node.js web 服务器的设计。

Node.js 我后悔的 10 件事- Ryan Dahl - JSConf EU (2018)

"支持现成的 TypeScript。使用 V8 的最新版本。
No package.json.
No npm。
与节点不明确兼容。
仅导入参考源代码 URL。
可以控制文件系统和网络访问，以便运行沙盒代码。
单个可执行文件。”为了解决这个问题，他建立了 Deno。了解关于 Deno 的更多信息:构建于 V8 之上的安全 TypeScript 运行时。

Node.js 中的问题:Web 安全性、模块、Index.js 和构建过程

在[上介绍 Deno http://JSConf。EU](http://JSConf.EU) 事件中，Dahl 指出“服务器端 JavaScript 需要事件循环才能成功”的发现是 Node.js 在 IT 实践中被广泛采用的最大部分。在 HTTP & SSL 支持协议建立之后，Dahl 于 2012 年离开了 Node.js 项目。当时，Node.js 有一个小的核心，Windows Server 支持，一个稳定的 API 系统，以及一个通过 NPM 不断增长的外部模块生态系统。在过去的 6 个月里，Dahl 带着不同的目标在平台上重新开始编码。

关于 Node.js web 服务器项目的遗憾，Dahl 说:

遗憾:对 async/await 的承诺没有用
遗憾:web 安全、写权限、&文件访问
遗憾:构建系统(GYP/GN/Python)
- >最好是一个外部函数接口(FFI)
遗憾:JSON & NPM 中央存储库依赖关系
遗憾:node_modules 太重&经常需要而没有扩展
遗憾:Index.js 使模块加载系统变得复杂&过度“可爱”
总的来说，Dahl
[![Ryan Dahl](img/cbe5d59b4b2ec2267880d00df149be4a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Inf7TFRj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v3kmzbrpx1w3zbcj5roq.png)

Ryan Dahl 在 JSFest 中介绍 Deno。
[![Ryan Dahl](img/51b9da5ae263e58bf107356ecc3adad8.png)T3】](https://www.youtube.com/watch?v=z6JRlx5NC9E&feature=youtu.be)

见过瑞安·达尔后，我唯一记得的是

[![Quote](img/7536c749957ad7891b8b304f35e18796.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z1NIvOA7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c2fjjxw1gf1www9t5wl3.jpeg)

干杯。瑞安·达尔。真正的灵感。

如果你喜欢它的内容，如果你能花点时间为我在[https://www.linkedin.com/in/ali-abbas-19b630161/](//Linkedin.)的技能背书，那就太好了

谢谢你