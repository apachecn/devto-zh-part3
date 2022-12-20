# 使用 GraphQL 构建和部署实时的 Svelte 3 应用程序

> 原文：<https://dev.to/praveenweb/build-and-deploy-realtime-svelte-3-apps-using-graphql-1fkj>

## 什么是苗条？

Svelte 是一个组件框架，它在构建阶段(不同于 React/Vue)将组件编译成高效的命令式代码来更新 DOM。

Svelte 给出了一个单独的`bundle.js`,可以在应用程序的 HTML 中引用，它不包含任何框架特定的依赖关系(神奇地消失的框架是他们之前的口号)。这些包通常比其他框架更小，更强调可访问性。

这也意味着没有虚拟 DOM，也没有像虚拟 DOM diffing 那样的额外工作，看起来它有性能优势。状态管理变得很容易，因为它只有 javascript 变量，更新它们会通过直接更新 DOM 来重新呈现组件(真正的反应式)

您还将在示例应用程序中看到，与其他框架相比，实现相同功能的样板代码更少。Svelte 上个月发布了他们的稳定版本 3，如果你想了解一些细节，这里有一个由 Rich Harris 写的有趣的博客。

好吧！现在让我们看看如何使用 Svelte 和 GraphQL 制作一个简单的实时应用程序。首先，我们将部署 Hasura。

## 部署 Hasura

[Hasura](https://hasura.io) 是一个[开源](https://github.com/hasura/graphql-engine)引擎，为您提供新的或现有 Postgres 数据库上的实时 graph QL API，内置支持拼接定制 graph QL API 和在数据库更改时触发 webhooks。

按照 [readme](https://github.com/hasura/graphql-engine/blob/master/community/sample-apps/svelte-apollo/README.md) 中的说明部署 Hasura，并创建应用程序所需的表 author 和 article。注意 GraphQL 端点的 Heroku URL。您将在应用程序中对此进行配置。

## 克隆并运行 App

[演示应用](https://github.com/hasura/graphql-engine/tree/master/community/sample-apps/svelte-apollo)带你构建一个简单的实时应用，列出所有作者和文章。

在本例中，我们将研究如何构建一个简单的应用程序来

*   使用 GraphQL 查询获取文章和作者，
*   使用 GraphQL 变体添加作者
*   通过实时更新作者列表，展示使用 GraphQL 订阅的实时功能。

为了将 GraphQL 集成到 Svelte 中，我们将利用 [svelte-apollo](https://github.com/timhall/svelte-apollo) 模块来执行查询。

您可以克隆并运行该应用程序进行演示。

```
$ git clone git@github.com:hasura/graphql-engine.git
$ cd graphql-engine/community/sample-apps/svelte-apollo
$ npm install 
```

Enter fullscreen mode Exit fullscreen mode

安装之后，您必须配置上面在 Heroku 上部署 Hasura 之后获得的 GraphQL 端点。

打开`src/apollo.js`并配置端点。

现在使用
运行应用程序

```
$ npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:这款应用是从标准[模板](https://github.com/sveltejs/template)中分离出来的，并添加了 svelte-apollo 依赖项来集成 GraphQL
下面是一个 svelte 组件进行 GraphQL 查询以获取文章列表的示例:

这里，我们从`svelte-apollo`导入`query`方法，并传入客户端和 GraphQL 查询来获取文章列表。还有一个`restore` API，用于将之前执行的查询恢复到 Apollo 缓存中。之前的查询来自应用程序初始化期间调用的 preload 方法。

订阅也非常简单:

如果仔细观察，这与 React hooks 风格的实现略有相似，因为样板代码更少。

## 捆绑使用汇总

Svelte 附带默认的 Rollup 捆绑包，配置 Rollup 也相当容易。当您运行命令`npm run build`时，rollup 生成一个 bundle.js，它在`index.html`中被引用，bundle.js 不包含任何框架特定的依赖项，如 React 或 Vue，因为 Svelte 在编译时绑定。

## 部署

部署一个苗条的应用程序就像用 javascript 和 css 文件部署一个简单的静态 HTML 应用程序一样简单。
我们用`now-cli`来部署 app。如果您还没有，请安装 now cli。

```
npm install -g now 
```

Enter fullscreen mode Exit fullscreen mode

并从您的项目文件夹中，执行以下命令:

```
now 
```

Enter fullscreen mode Exit fullscreen mode

这将使用`now.json`中可用的配置部署我们的 svelte 3 应用程序。显然，它可以部署在 Netlify、Heroku 等流行的提供商上。

## 最后的想法

尽管偏离了 React 开发人员熟悉的 JSX 风格，但 Svelte 的学习曲线最少，我个人会将语法与 Vue 进行比较，因为你专注于单独编写 HTML。

如果您熟悉 React/Vue 和其他基于虚拟 DOM 的框架，您可以尝试使用 Svelte 来减少样板代码和潜在的性能优势。当然，GraphQL 集成非常容易，而且您可以不费吹灰之力获得所有好处。

如果你关注 SEO，还有一个名为 [Sapper](https://sapper.svelte.dev/) 的 SSR 框架。

试玩一下，让我们知道你的想法。