# 一个初学者在一天之内学到了什么

> 原文：<https://dev.to/tangweejieleslie/what-a-beginner-learned-about-backend-in-a-day-1ag0>

我的目标是启动我的第一个 web 应用程序，我今天的目标是让用户的上传可以保存在 MongoDB 中。在研究了如何实现这一点之后，我总结出有 4 种主要的实现方式。

*   ~~设置前端接口~~(已经完成，在 Vue 上)
*   设置一个后端服务器(我选择了 Express)
*   建立一个数据库(MongoDB)
*   在前端和后端之间设置 API(Axios)

在克服了我遇到的问题之后，今天我实现了前端和后端之间的通信。

在审查了我的代码之后，我列出了一个清单，作为未来实现的指南。也是为了提醒我遇到的各种错误以及相应的解决方案。

## 实现列表

#### 快递

*   利用“CORS”NPM 包
    *   我已经被本地主机上的 CORS 错误困扰了一段时间，使用“CORS”NPM 包似乎是我发现的最简单的解决方案
*   创建 HTTP 请求
    *   并仔细检查了端点，结果我在 POST 请求中遗漏了一个'/'，我想知道为什么 POST 请求不起作用
*   使用 Postman 来测试您的 HTTP 请求是否按预期工作

#### 前端视图

*   使用`v-on:click`指令调用处理相应 HTTP 请求的特定方法
*   利用 Axios 轻松处理 HTTP 请求
*   最重要的是，为每个 HTTP 请求创建一个单独的组件
    *   我开始把它们都放在一个组件中，结果文件变得如此混乱，以至于我开始出错。

> 可读性更差的代码导致更多的混乱
> 更多的混乱导致更多的错误
> 更多的错误导致更多的挫折
> 更多的挫折导致更低效的调试
> 更低效的调试导致时间浪费
> 
> 故事的寓意:写干净简单的代码！

简而言之，上述实现允许前端发送将由后端处理的 HTTP 请求。此时，所有数据都是测试数据，因此是硬编码的。

考虑到这一点，我明天的主要目标是实现它，使每个 HTTP 请求都可以与 MongoDB 交互，并有一个正确定义的数据结构来接受动态用户输入。

干杯！

## 参考文献

*   [Stackoverflow -如何修复 CORS 错误](https://stackoverflow.com/questions/7067966/how-to-allow-cors)
*   [颜色 npm 包](https://www.npmjs.com/package/cors)
*   [设置快速路线](https://expressjs.com/en/guide/routing.html)
*   [Axios npm 包](https://www.npmjs.com/package/axios)