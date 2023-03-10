# 使用 Lambda、API Gateway、DynamoDB 和 S3 以及无服务器框架部署 web 应用程序

> 原文：<https://dev.to/yloganathan/deploying-a-web-app-using-lambda-api-gateway-dynamodb-and-s3-with-serverless-framework-4b1b>

## 第 0 步:了解架构

我们今天将使用最简单的架构来创建和部署一个 web 应用程序。
[![](img/484f1541707b329d74ab8d7222615c7d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--pGeyVw6s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/37md0szn7oukd8d9xr0i.png)

## 第一步:写 app

我们正在创建的应用程序是一个简单的想法板应用程序，任何人都可以创建一个想法，添加评论，投票或删除一个想法。在 git 中找到这个应用程序的代码。
[![](img/035a2927a9134ba7cbc1be1f4b923f15.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--b8bKYZ_e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/we0gpr317a5lzciojyq6.png)

1.  React 前端——Jennifer peav ler 创建了一个简单的 react 应用程序。
2.  后端——一个无服务器的 python crud 处理程序。

与传统的服务器后端相比，无服务器后端的编写方式有所不同。

Lambda 需要一个入口点，而入口点是一个将事件、上下文作为参数的函数。