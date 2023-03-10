# 即时生成无服务器 HTML 表单

> 原文：<https://dev.to/lucaskardo/create-responsive-html-contact-forms-for-free-in-an-instant-2gj8>

作为一名开发人员，我喜欢完全控制我使用的工具。我搜索了一个符合这个标准的表单生成器，但是没有找到。

所以我决定建立自己的表单并与大家分享- [超级简单表单](https://supereasyforms.com/)
使用超级简单表单，你可以在瞬间创建 100%可定制的 HTML 表单，无需服务器后端。

Super Easy Forms 是开源和免费的。它的使用方式没有限制。您可以创建无限数量的表单，存储无限数量的表单提交，注册无限数量的电子邮件，轻松地从数据库中导出提交，最重要的是，您可以完全控制您的数据。所有这些都是免费的！
[https://www.youtube.com/embed/UMZDvlx7sn0](https://www.youtube.com/embed/UMZDvlx7sn0)

## 工作原理

Super Easy Forms 是一个用 node js 构建的简单 CLI。它可以帮助你向 AWS SES 注册你的电子邮件，自动在 AWS 中为你创建必要的资源，并输出一个已经连接到你的 API 的引导 HTML 表单，准备好复制粘贴到你的网站。

[![](img/46c47963cde21791c2a430c7fef5b5d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r-AkFf2D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/biyeu0b15ecgwduk3spr.png)

### 资源创建完毕

*   带有所需字段的 Dynamodb 表。
*   一个 lambda 函数将发送一封电子邮件到您的注册地址，并将条目添加到数据库中。
*   带有 POST 方法的 lambda 函数的 REST API 端点。
*   表单
*   响应 bootsrap 4 布局
*   带标签的自动表单域
*   JQuery CDN
*   连接到 API 端点的 JQuery 处理程序

## 自定义您的表单

CLI 输出一个 html 文件，其中包含您的现成表单，该表单预置了默认的引导类和内联 JQuery 处理程序。

您可以添加自定义 CSS，修改表单的 HTML 布局，并根据您的项目进行调整。

如果你需要灵感，你可以查看 codepen 中的 bootstrap html 联系表单的[集合](https://codepen.io/collection/DpzJme/)。

[https://codepen.io/gkpty/embed/Ezoaxa?height=600&default-tab=result&embed-version=2](https://codepen.io/gkpty/embed/Ezoaxa?height=600&default-tab=result&embed-version=2)

## AWS 在超简易表单中包含哪些内容？

你需要有一个 AWS 帐户才能使用超级简单的表格。在不久的将来，我们将解决我们的供应商锁定，但目前 AWS 是一个很好的开始。

Super Easy Forms 使用 AWS 的以下服务:

简单的电子邮件服务。每月 1000 封免费邮件，然后每收到 1000 封邮件收取 0.10 美元。
**DynamoDB** 完全托管 NoSQL 数据库。25GB 的存储空间永远免费。
**Lambda** 让你不用管理服务器就能运行代码。每月 100 万次请求，永远免费。那么每 100 万次请求 0.20 美元。
**API 网关** API 管理解决方案。12 个月每月 100 万个 REST API 请求，然后每 100 万个请求 3.50 美元。
**S3** 为您的文件提交存储桶对象(即将推出！).5Gb 免费 12 个月，然后每 GB 0.023 美元。

想要更多信息？[https://aws.amazon.com/free](https://aws.amazon.com/free)

## 路线图

超级简单的形式不止于此。我们计划了一系列功能，让它变得更容易、更超级！

其中包括:

*   文件提交(图像、视频、pdf 等！)
*   支付形式(与领先的解决方案提供商集成)
*   与 gmail API 直接集成
*   向 XLS 和 CSV 直接导出提交内容
*   可选验证码(垃圾邮件防护)

我们非常感谢您的反馈。请使用超级简单的表格，我们相信你会发现它非常有用！