# 使用 GraphQL 后端在几分钟内生成 React 应用程序

> 原文：<https://dev.to/8base/generate-react-app-with-graphql-backend-in-minutes-1651>

8base CLI 中的一个新的实验性特性从您的数据模型生成一个工作的 React 应用程序。这对于开始使用 GraphQL 和 React 或者快速为您的应用程序生成管理 UI 非常有用。类似于 Ruby-on-Rails 中的脚手架。

生成的 React 应用基于`Create React App`，使用`Apollo Client`进行 GraphQL 请求，包括由`Auth0`支持的认证以及上传文件的能力。

几个简单的步骤:

1.  在 [8base](https://www.8base.com/)
    [![Define your data model](img/b8b029742218cbc947c3a56d1c349948.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--voPk6-yw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h3dalq8bkuh5jlaylyda.gif) 中定义你的数据模型

2.  [使用`npm`安装开源 8base CLI](https://docs.8base.com/docs/cli-install) 并运行`8base login`

3.  运行`8base generate app ${YOU_APP_NAME}`生成 app 模板

4.  运行`8base generate scaffold ${TABLE_NAME}`为选择的数据表生成 CRUD UI(您可以为想要添加的每个表重复此步骤)

5.  `yarn install && yarn start`启动应用程序

所有终端步骤组合:
[![Generate React code](img/1120b054a7c30e3534bbc637e7c89634.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WZUt7sSD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cf6ffjvsfp7l7srjhs0u.gif)

这里有一个生成的 React 应用程序的演示:
[![Start app](img/37c4e5af00a9b16c7c3aa3a2e8d4f15a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TE7PJP9x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k85fhsmp3s433oigqdwg.gif)

让我知道你的想法和快乐的产生！更多信息[点击这里](https://www.8base.com/generate)。

P.S. 8base 目前处于测试阶段，完全免费。它将永远对小型开发团队免费。