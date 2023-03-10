# 使用 Next.js 8 和 Zeit Now 构建和部署无服务器 React 应用程序

> 原文：<https://dev.to/hasurahq/build-and-deploy-serverless-react-apps-with-nextjs-8-and-zeit-now-22op>

**TL；DR** :配置 Next.js 8 无服务器模式，为每个页面生成 lambdas。使用 Hasura GraphQL API 作为数据层。立即部署到 Zeit。即时设置。教程/样板文件👉[next js-8-无服务器](https://github.com/hasura/graphql-engine/tree/master/community/sample-apps/nextjs-8-serverless)

[![Next.js Serverless Now Hasura GraphQL](img/573228341d64c5c9836783b794386e31.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YAG8ybQn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/02/nextjs-serverless-now-hasura-graphql.png)

[Next.js 8](https://nextjs.org/blog/next-8#serverless-nextjs) 上周登陆，支持无服务器部署。在这个版本中，Next.js 将应用程序拆分成无服务器的 lambdas，有助于提高可靠性和可伸缩性。“按使用付费”模式是无服务器化的主要优势之一。还有其他明显的好处，如分布式故障点和扩展。对无服务器的现成支持令人兴奋！

为了快速测试，我创建了一个集成了 Hasura GraphQL 引擎的 Next.js 8 应用程序。这个应用程序有几个页面；索引和关于。索引页面呈现从 Hasura GraphQL 获取的作者列表。“关于”页面呈现一些静态文本。这是给你的入门指南。

## 部署 Hasura

按照 [readme](https://github.com/hasura/graphql-engine/blob/master/community/sample-apps/nextjs-8-serverless/README.md) 中的说明部署 Hasura 并创建应用程序所需的表格作者。注意 GraphQL 端点的 Heroku URL。您将在应用程序中对此进行配置。

## 克隆并运行 app

[演示应用](https://github.com/hasura/graphql-engine/tree/master/community/sample-apps/nextjs-8-serverless)带你构建一个简单的应用，列出所有作者。您可以克隆并运行该应用程序进行演示。

```
$ git clone git@github.com:hasura/graphql-engine.git
$ cd graphql-engine/community/sample-apps/nextjs-8-serverless
$ npm install 
```

Enter fullscreen mode Exit fullscreen mode

安装之后，您必须配置上面得到的 GraphQL 端点。

打开 **lib/init-apollo.js** 并在 **uri**
中配置 Hasura GraphQL 端点现在使用以下命令运行应用:

```
$ npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

并访问 [http://localhost:3000](http://localhost:3000) 查看 app。

**启用无服务器模式**
为了启用无服务器模式，我们在 next.config.js 中添加了无服务器构建目标

```
module.exports = {
  target: "serverless",
}; 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在通过运行以下命令构建无服务器应用程序:

```
$ npm run build 
```

Enter fullscreen mode Exit fullscreen mode

由 Next.js 生成的构建具有**非常低的内存占用**，无服务器函数的基本压缩大小为 **50KB** 。输出的函数不需要任何其他依赖项，因为所有需要的东西都已经捆绑在一起了。当然，根据页面的内部依赖关系，最终大小会超过 50KB。

在**中。在下一个**文件夹中，您将看到一个在构建之后生成的**无服务器**文件夹。里面有一个 **pages** 文件夹，每页输出 lambda。

```
pages/index.js => .next/serverless/pages/index.js
pages/about.js => .next/serverless/pages/about.js 
```

Enter fullscreen mode Exit fullscreen mode

这些页面现在可以独立部署到其他云提供商。

## 部署到 Now 平台

Now 平台通过 [now](https://zeit.co/now) ( [下载](https://zeit.co/download))提供了一种无缝的方式将其部署到云:运行以下命令来部署

```
$ npm install -g now
$ now 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:now-CLI 的旧版本不支持无服务器模式。

一旦部署成功，您将能够导航到页面 **/** 和**/关于**，每个页面内部都是一个 lambda 函数，现在管理。
Now 平台负责跨几个地区的路线安排。lambdas 全部部署在 AWS 上。你可以在这里阅读更多关于可用路线和区域[的信息。](https://zeit.co/docs/v2/platform/regions-and-providers/)

构建过程的输出可以是静态文件或无服务器 lambdas。静态文件将直接由 Zeit 的[边缘缓存](https://zeit.co/docs/v2/domains-and-aliases/cdn/)提供，而 lambdas 包含动态和按需执行的代码。

正如您在下图中看到的，关于和索引页面是 lambdas。静态资产由 Zeit 缓存。

[![Now Deployment](img/06c3242e7d2146993c68dacd0f79ac63.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i93CousR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/02/nextjs-now-serverless.png)

## 从旧版本迁移

js 8 保持了向后兼容性，因此对于大多数应用程序来说，你需要做的就是运行:

```
$ npm i next@latest react@latest react-dom@latest 
```

Enter fullscreen mode Exit fullscreen mode

并在 next.config.js 中设置配置以获得无服务器构建目标，您应该可以开始了。

## 最后的想法

这是一个将 web 应用部署为无服务器 lambdas 的有趣转变。由于该应用程序的每个页面都是一个 lambda，您只需为调用次数付费，并且可以有选择地扩展高流量页面。静态资产也可以被缓存，从第一天起就提供了一个负担得起的、可伸缩的选项。
在 [github](https://github.com/hasura/graphql-engine/tree/master/community/sample-apps/nextjs-8-serverless) 上查看示例应用程序。