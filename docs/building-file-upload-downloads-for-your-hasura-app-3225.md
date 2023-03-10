# 为您的 Hasura 应用程序构建文件上传/下载

> 原文：<https://dev.to/hasurahq/building-file-upload-downloads-for-your-hasura-app-3225>

[![](img/5e01f6e31a69321a8de9d7028e07d65b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lc-3AqnJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/04/Storage.png)

*本教程由[约翰·埃利亚松](https://twitter.com/elitasson)撰写，并作为 [Hasura 技术作者计划](https://blog.hasura.io/the-hasura-technical-writer-program/)的一部分发布，该计划旨在支持为开源 Hasura GraphQL 引擎编写指南和教程的作者。*

Hasura 在通过 Postgres 使用实时 GraphQL 处理数据方面很棒，但 Hasura 没有提供如何处理用户生成文件的内置解决方案。例如当用户想要上传文件或下载文件时。

在这篇博文中，你将学习如何在你的 Hasura 应用中管理用户生成的文件。文件将由使用 S3 兼容对象存储的微服务处理。存储微服务还将使用 JWT 声明处理读写权限。

所以你的用户想要上传一个文件。如果您正在构建一个图像托管服务，这个文件可以是一个图像。或者，如果您正在构建 CRM，它可以是一个 Excel 文档。用户想要上传什么类型的文件并不重要，你需要能够保存和提供这些文件。

## 先决条件

这篇博客文章假设你已经有了用 React 和 Apollo GraphQL 建立一个项目的知识。

在我们开始为 Hasura 构建存储微服务之前，我们必须首先从 Hasura 和我们的 S3 服务中获取一些变量。我们正确地设置了我们的域。

### 哈苏拉

我们需要以下内容

*   Hasura Endpoint
*   访问键

可以在 Hasura 控制台的“GraphiQL”选项卡下找到 Hasura 端点

[![](img/23560c1b3335c0b3970c08a6b329ace7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9751dKz5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/ptQUIIa78SCOSD4fYXrh4U1yPkpRsI_GkdxqotERTa7SqbYFPelkY3OeKkRxfFwO7MT-d9UqLugXnPFbG6A3WP1hiOMrTdBCOb31DS15BCpDnAwsrRUrBjsUI7LBt-ViRBPQ2TiO)

访问键位于 docker-compose.yaml 文件中。

[![](img/4cef038a04681afc37e00a3af41e694b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dJAwHPZL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh6.googleusercontent.com/3z-0ooxpMPPTZmj231JZ2H58j28xO0sZp77gPENBNi8wyqJgiO8vvRKb0GdhOL94JbIO3pURsk-bn1qYqMXQX4Z-yaDdRRFtGsEgrqpcd0QsCP7UFi4ewYmRouxCTsX_PwdQk7_y)

### S3 斗

我们需要什么:

*   S3 _ 访问 _ 密钥 _ID
*   S3 _ 秘密 _ 访问 _ 密钥
*   S3 _ 终点
*   S3 _ 水桶

接下来，我们将创建一个 S3 存储桶。S3 存储桶是存储所有文件的地方。有几种与 S3 兼容的对象存储服务。S3 是最受欢迎的。然而，在这个例子中，我们将使用数字海洋空间。网上有几个关于如何开始使用数字海洋 S3 的指南。

*   [https://www . digital ocean . com/community/tutorials/how-to-create-a-digital ocean-space-and-API-key](https://www.digitalocean.com/community/tutorials/how-to-create-a-digitalocean-space-and-api-key)

### 存储微服务

现在，我们将设置存储微服务。我们的微服务将由两个文件组成。Index.js 和 storage.js

<figure>[![](img/1b120fad872324adff2309ef06feda43.png)T3】

<figcaption>index . js</figcaption>](https://res.cloudinary.com/practicaldev/image/fetch/s--F7y3MM_k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/04/hasura-storage-index-6.png) </figure>

## 上传(写)

接下来创建 storage.js 文件，并添加以下代码。

<figure>[![](img/9cf96cf7e32726cdc53c920286d881dc.png)T3】

<figcaption>storage . js</figcaption>](https://res.cloudinary.com/practicaldev/image/fetch/s--wli64Yum--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/04/hasura-storage-storage-index-2.png) </figure>

对于这个文件，我们需要 config.js

<figure>[![](img/7bfd6316b70fabcf0387e1e76e1b3a1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KPL2N58N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/04/hasura-storage-config-3.png)

<figcaption>config . js</figcaption>

T6】</figure>

该代码将处理对端点/存储/上传的上传请求，并通过该微服务将所有文件从客户端传输到 S3 存储桶。端点将返回 S3 桶中文件的密钥列表(文件路径)。

接下来，我们将创建一个 React 组件来处理文件上传:

<figure>[![](img/e3e41d855b25dcdf1fb3aa97725ceabe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hLu3bDsy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/04/upload-1.png) 

<figcaption>上传. js</figcaption>

</figure>

`window.config.backend`是你后端的 url。可以是 localhost:3010。

确保您的存储微服务已经安装了所有适当的软件包，并且正在运行。您可以使用`node index.js`命令运行 index.js。

现在文件上传工作正常。接下来，我们希望列出所有文件，并能够查看和下载所有文件。

## 下载(阅读)

首先让我们列出所有文件。我们将创建对文件表的订阅，并实时获取更新。

[![](img/e08138191f2255c8b815f322cdf48363.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--utMR376---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/04/list-2.png)

在查看实际文件之前，我们需要向 storage.js 添加一个新的路由，它将把内容从 S3 流回客户端。

[![](img/2251a69750ff08d418ff8d6049209a24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_C4Tc5aP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/04/file-route-storage-1.png)

我们现在在 Hasura 中有一个上传文件、列表文件和下载文件的工作解决方案。

## 安全

现在，任何人都可以通过我们的存储微服务上传和下载文件。这可不好。我们可能只希望登录的用户上传和下载内容。此外，我们可能需要将文件从一个组织的用户中分离出来。

这可以用不同的方法来处理，我们将在概述中查看一种处理安全性的方法。安全性与您的身份认证服务紧密相关，不同的身份认证服务解决方案可能会有很大差异。

当使用 Hasura 和您的存储微服务之间共享的 JWT 令牌时，您必须确保它们共享相同的 JWT 秘密。JWT 秘密是 hasura 中的一个环境变量，位于 docker-compose.yaml 文件中。

### JWT 声称

Hsaura 使用 JWT 声明来处理数据库中的 CRUD 权限，因此使用 JWT 声明来保护文件也是有意义的。因此，为了让我们的存储微服务使用 JWT 声明来处理读写访问，我们需要以某种方式将我们的 JWT 令牌发送到我们的存储微服务以供使用。

Cookies 是向我们的存储微服务发送 JWT 声明和请求的一种方式。另一种方法是将 JWT 声明作为报头发送。

一旦您可以在微服务中检索 JWT 声明，您就可以开始根据文件、密钥和 JWT 声明制定规则，以确定是否应该处理该请求。

这里有一个例子:

[![](img/4651b18ea1ae898d4fcb35495789a83b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TH_yjiCV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/04/storage-example-rule-1.png)

在上面的示例中，允许用户上传和下载文件到其/company/ <company_id>文件夹。还允许每个用户从/public 文件夹中读取文件。所有其他请求都可以驳回。</company_id>

## 部署

我们的代码可以部署在任何可以运行 Node.js 的地方，例如:DigitalOcean、AWS Lambda、OpenFaaS、Zeit Now 等。我们将启动自己的 express 服务器来处理所有请求，并让我们看看如何设置它。

服务器将被部署在数字海洋水滴上。

[![](img/76e7dd8b6b080a6755e8aa767a70fc5a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KhtBRhIn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh4.googleusercontent.com/INv4ibo3XYJTnDcLPfDa-eVo8Ec8VxaaHP1lhk1SvCDSDBRdvc5hxD2BVhnN05ryqHXpH7U_bpoqeEEgTuubTx9W0YfP-c_fa5KNX5eu3E35MxlSNDsDCPr3EMBNAKbZxKbDOBMB)

DigitalOcean Marketplace 有一个 [Node.js 一键式应用程序](https://www.digitalocean.com/docs/marketplace/nodejs/)，可以用来快速创建针对 Node.js 优化的 droplet。它带有 Node.js 版本 8.10.0 和 npm 3.5.2。

选择一个标准的 5 美元计划，我们可以随时扩大/调整以后。

哈苏拉部署在哪里呢？

[![](img/30a310a9de428ad1711b048a61638640.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u6x8IGPr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh5.googleusercontent.com/oLl5u2U-o5R_geYR3PgqNWXcEheXmJ7vpH-t5SLypjkpUYY9f2-F-m-7pNcbWVzSFQhdegio80KrmQV_y6PZx4euJR66NskwZmZll2VIxWYdcW5DVtuqB-2vz5BYGFLHymY-x1cX)

选择一个合适的数据中心区域，添加您的 SSH 密钥并点击 **Create** 。

创建好 droplet 后，记下 droplet 的 IP 地址。

我们需要 **ssh** 进入 droplet 来继续设置。在终端中，运行以下命令:

```
$ ssh root@<do-ip-address> 
```

用你自己的 droplet 地址替换<do-ip-address>。</do-ip-address>

我们将使用 Node.js 的进程管理器 [PM2](http://pm2.keymetrics.io) ，以确保我们的 Node.js 和 express 服务器作为后台守护程序保持运行。

让我们从全球安装 PM2 开始

```
$ npm install -g pm2 
```

让我们创建一个 **pm2.json** 配置文件来配置 pm2 进程管理器。

<figure>[![](img/bf690c1d96f1e82ee2e342958882cd73.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TNsW-Eu2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/04/hasura-storage-pm2-1.png)

<figcaption>PM2 . JSON</figcaption>

</figure>

接下来，我们将安装所有 npm 依赖项。

```
npm install --save pm2 express cors morgan joi boom uuid multer multer-s3 aws-sdk 
```

**配置 S3 凭证**

使用以下内容创建 config.js:

<figure>[![](img/b1062451aae244622be908d5489f6555.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aNBBzlCA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/04/hasura-storage-config-4.png)

<figcaption>config . js</figcaption>

T6】</figure>

**创建 index.js**

创建完整的 index.js 文件，配置 express 和 routes，如下所示:

<figure>[![](img/a4916902b2e052cb59712f54442d6fce.png)T3】

<figcaption>index . js</figcaption>](https://res.cloudinary.com/practicaldev/image/fetch/s--pGhI2j9B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/04/hasura-storage-index-7.png) </figure>

**创建 storage.js**

创建 storage.js，它按如下方式处理文件上传:

<figure>[![](img/8acf818c22b7329ce4b69f7c7a1f95f9.png)T3】

<figcaption>storage . js</figcaption>](https://res.cloudinary.com/practicaldev/image/fetch/s--Li4ta2sg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/04/hasura-storage-storage-index-3.png) </figure>

**配置环境变量**

转到[https://github.com/elitan/hasura-backend-plus#env-variables](https://github.com/elitan/hasura-backend-plus#env-variables)，通过导出来配置这些环境变量。

例如，要配置 USER _ REGISTRATION _ AUTO _ ACTIVE:' false '，请在终端中运行以下命令

```
$ export USER_REGISTRATION_AUTO_ACTIVE=’false’ 
```

同样，配置此服务所需的环境变量。

最后，使用以下命令启动服务器:

```
$ pm2 start pm2.json 
```

使用 PM2s 监视器`pm2 monit`确保服务器正在运行。

我们现在有了一个工作方法，使用 S3 兼容的对象存储服务来处理用户为 Hasura 生成的文件。

## 接下来是什么？

一旦你让这个服务器工作，并且阅读了代码，你应该对如何使用 Hasura 处理用户生成的文件有更好的理解。接下来，您需要仔细查看的是您的权限规则，以及您的 JWT 令牌提供商，以便为您的所有文件强制执行正确的读写规则。

此外，您可能希望确保服务器连接使用 HTTPS 加密。球童服务器([https://caddyserver.com](https://caddyserver.com))是一个伟大的工具，为网站提供自动 HTTPS。

## 关于作者

Johan Eliasson 是一位来自瑞典的企业家，他在计算机科学方面有很强的背景。他目前正在重建一个大型内部业务系统，以应对/ Apollo / GraphQL / Hasura。在空闲时间，他喜欢和家人呆在一起，读书和健身。你可以通过发 [johan@eliasson.me](mailto:johan@eliasson.me) 或者发 [Twitter](https://twitter.com/elitasson) 联系他。