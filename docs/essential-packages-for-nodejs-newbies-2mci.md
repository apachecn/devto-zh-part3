# Node.js 新手必备包

> 原文：<https://dev.to/uf4no/essential-packages-for-nodejs-newbies-2mci>

在 NPM 寻找解决某个问题的包或者只是让开发人员的生活更轻松的包可能很困难，如果你是 Node.js 的新手就更难了

当我开始学习 Node 时，我让我的一些朋友(他们已经在 JS 项目中工作)告诉我他们最常用的包，这样我就可以提高我的技能。我得到的列表非常简单，每当我发现需要使用它们的时候，我就会添加更多的包。

在这里搜索，我找到了去年由 [@ben](https://dev.to/ben) 写的[这篇文章](https://dev.to/ben/what-are-some-useful-npm-packages-i-might-not-know-about-55i)，虽然人们建议了很多非常有用的软件包，但我认为它们主要是为高级用户准备的。

我知道这个列表很短，所以也许我们可以一起改进它！

后端:
- express: web 应用服务器框架
- body-parser:用于 express 的中间件
- hapi:另一个内置插件的 web 框架，用于授权、缓存、cookies...([@杰里米·肖尔](https://dev.to/w9jds) )
- axios:发送 http 请求。
- dotenv:从. env 文件中加载变量作为环境变量。根据本地、暂存和生产加载不同的数据库详细信息非常有用。

数据库:
-mongose-与 MongoDB
交互- json-server -从 json 文件创建一个假的 REST API。在前端项目中工作或模拟外部 API 时非常有用。

安全、加密:
- jsonwebtoken:生成、验证和解码 JWT
- bcrypt:加密函数

测试
- mocha:测试框架
- chai:断言库
- jest:另一个内置了覆盖报告的测试框架( [@joelnet](https://dev.to/joelnet) )
- chai-http:从你的测试发送 http 请求
- sinon:创建存根
- nock:创建 mock
-NYC:生成测试覆盖报告

其他:
-amqplib:For Rabbit MQ
-debug:在控制台打印调试消息
- watson:另一个调试库，具有不同级别的调试
- url:解析 URL
-moment:格式化日期
- lodash:使用数组、对象、数字的助手函数...

插件/代码格式化器:
- nodemon:更改文件时重启 app
-更漂亮的
- eslint

你们觉得怎么样？哪些是你认为新手应该知道的基本软件包？我会尽可能地更新这个列表，这样对其他人会有帮助:)