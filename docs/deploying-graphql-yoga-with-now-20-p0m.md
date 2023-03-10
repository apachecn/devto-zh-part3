# 使用 Now 2.0 部署 graphql-yoga

> 原文：<https://dev.to/garethbk/deploying-graphql-yoga-with-now-20-p0m>

# 用 Now 2.0 部署 graphql-yoga

[graphql-yoga](https://github.com/prisma/graphql-yoga) 让一个轻量级的、全功能的 graphql 服务器轻松启动并运行。Zeit 的 [Now](https://zeit.co/now) 提供了一个云部署平台，利用无服务器基础设施为您的应用提供动力。让我们看看如何将这些结合起来部署一个利用 Now 的一些特性的 GraphQL 服务器，同时注意一些潜在的缺陷。

本教程假设您对 GraphQL 有所了解，但是如果您以前从未构建过服务器，也没关系，我们将简要介绍一下我们正在部署的服务器。

这篇文章源于我在将一个可以在 Now 1.0 上完美运行的服务器移植到 Now 2.0 时遇到的困难，因此它并不是真正关于将无服务器与 graphql-yoga 一起使用，而是关于如何让一个普通的 graphql-yoga 服务器与 Now 2.0 一起工作。

最终代码可在此参考:[https://github.com/garethpbk/graphql-yoga-now/tree/now](https://github.com/garethpbk/graphql-yoga-now/tree/now)

## 先决条件

如果你以前没有用过 Now，安装 [Now 桌面](https://zeit.co/download)应用程序并注册一个 Now 账户。运行`now -v`以确保它工作正常——它将打印一个版本号(在撰写本文时是 13.1.2)。

我们将部署一个非常基本的`graphql-yoga`服务器，1)连接到[口袋妖怪 API](https://pokeapi.co/) 和 2)返回一个口袋妖怪列表或一些关于单个口袋妖怪的信息。克隆服务器回购:`git clone https://github.com/garethpbk/graphql-yoga-now.git`

## GraphQL 服务器

在克隆的目录中，运行`yarn`来安装依赖项，然后运行`yarn start`来启动服务器。浏览你的浏览器到 [http://localhost:4000](http://localhost:4000) ，你应该看到 [GraphQL Playground](https://github.com/prisma/graphql-playground) IDE 打开了。`graphl-yoga`默认包含这个探索你的服务器的牛逼工具。

在 *schema.graphql* 中有三种类型:`PokemonList`由只有名称和 url 的`ShortPokemon`和有更多信息的`LongPokemon`组成。根`Query`类型注册了两个解析器，一个返回一列`ShortPokemon`，另一个返回一个`LongPokemon`。在 GraphQL Playground 中使用如下查询:

```
query GET_ALL_POKEMON {
  allPokemon(limit: 30) {
    pokemon {
      name
      url
    }
  }
} 
```

```
query GET_SINGLE_POKEMON {
  pokemon(id: 140) {
    id
    name
    height
    weight
    frontImage
    backImage
  }
} 
```

## 准备部署

激动人心的部分，是时候让我们的口袋妖怪服务器对世界开放了。在项目的根目录下创建一个名为`now.json`的新文件——这是一个配置文件，告诉我们现在如何构建我们的项目。

首先指定您想要使用 Now 2.0

```
{
  "version": 2
} 
```

(从前有一个不同的平台使用基于容器的部署方法；现在 2.0 将这一点彻底转变为无服务器模式。如果您尝试在 2.0 发布后创建的帐户上使用版本 1，您会看到一条“请立即使用 2.0”的消息，并且会失败。)

接下来告诉我们*如何使用`builds`键*来构建这个项目

```
{
  "version": 2,
  "builds": [
    {
      "src": "src/index.js",
      "use": "@now/node-server"
    }
  ]
} 
```

这就是奇迹发生的地方。现在“构建者”将在`src`中指定的代码转换成一个无服务器的“lambda”函数。

因为我们的服务器是 Node.js 服务器，所以我们想使用 Node.js 构建器。这里有一个问题——现在的文档推荐对 Node.js 函数使用`@now/node`构建器，但是因为这个不是为无服务器编写的，所以`@now/node-server`构建器是我们想要的。

我们最不需要的是告诉 HTTP 请求指向哪里的路由定义

```
{
  "version": 2,
  "builds": [
    {
      "src": "src/index.js",
      "use": "@now/node-server"
    }
  ],
  "routes": [
    {
      "src": "./*",
      "dest": "src/index.js
    }
  ]
} 
```

所有流量都被定向到服务器公开的端点。

在我们尝试部署之前，最后一件事是:创建一个名为*的文件。现在忽略根节点的*，添加 node *模块。这告诉现在不要直接上传 node_modules 文件夹，因为它是在部署过程中构建的。就像*。gitignore_。

## 现在正在部署与

好了，各就各位，开始吧！在终端中键入`now`,然后看着你的项目在你眼前构建。完成后，您会看到“成功！部署就绪”消息。打开它给你的链接...哦不，发生什么事了？ **HTTP 错误 500** ！？

如果您从 [online Now 仪表板](https://zeit.co/dashboard/)查看构建日志，您会看到以下错误消息:

```
Error: No schema found for path: /var/task/user/src/schema.graphql 
```

换句话说，它找不到我们的模式，如果没有模式，GraphQL 服务器就没有多大用处。这个问题来自于构建器如何改变路径引用，而不是它在你的计算机上是如何工作的。幸运的是，这很容易解决；打开 *index.js* 并找到服务器实例:

```
const server = new GraphQLServer({
  typeDefs: './src/schema.graphql',
  resolvers,
}); 
```

我们所要做的就是使用`__dirname`将`typeDefs`属性从相对路径更改为一个路径:

```
const server = new GraphQLServer({
  typeDefs: __dirname + '/schema.graphql',
  resolvers,
}); 
```

构建者现在知道在哪里寻找模式了。再次运行`now`，这一次，打开链接应该会导航到熟悉的 GraphQL Playground 界面。

就是这样！您的`graphql-yoga`服务器现在可以在云端使用，任何有互联网连接的人都可以访问。相当酷。

## 添加环境变量

作为奖励，让我们看看如何在 Now 2.0 中使用环境变量，对于所有这些 API 键，我们宁愿保密。Zeit 有一个在本地开发中使用`process.env`变量的包，反映了它在 Now 部署中是如何完成的:

```
yarn add now-env 
```

在项目根目录下创建一个名为 *now-secrets.json* 的新文件。例如，我们将使 PokéAPI url 成为一个环境变量，所以添加以下内容:

```
{
  "@pokemon-api-base-url": "https://pokeapi.co/api/v2/pokemon"
} 
```

在 *now.json* 中添加一个“env”字段，我们将在这里指定`process.env`中可用的内容:

```
{
  "version": 2,
  "builds": [
    {
      "src": "src/index.js",
      "use": "@now/node-server"
    }
  ],
  "routes": [
    {
      "src": "./*",
      "dest": "src/index.js"
    }
  ],
  "env": {
    "API_BASE_URL": "@pokemon-api-base-url"
  }
} 
```

最后，我们将在查询解析器中使用它；打开 *src/resolvers/query.js* 并将`require('now-env')`添加到文件的顶部，然后用环境变量替换这两个 API 调用:

之前:

```
const allPokemonRes = await axios(`https://pokeapi.co/api/v2/pokemon?limit=${limit}`); 
```

之后:

```
const allPokemonRes = await axios(`${process.env.API_BASE_URL}?limit=${limit}`); 
```

之前:

```
const pokemonRes = await axios(`https://pokeapi.co/api/v2/pokemon/${id}`); 
```

之后:

```
const pokemonRes = await axios(`${process.env.API_BASE_URL}/${id}`); 
```

运行`yarn start`,您应该看到服务器在本地正常工作，API url 现在来自一个环境变量。请注意，在一个真实的项目中，您可能希望将 *now-secrets.json* 添加到您的*中。gitignore* 列表。

接下来，将密码添加到您的 Now 帐户中:

```
now secret add pokemon-api-base-url https://pokeapi.co/api/v2/pokemon 
```

再键入一次`now`，服务器将使用环境变量进行部署。请记住，现在秘密与你的*账户*相关，而不是特定的*项目*或*部署*——我建议用细节来命名你的秘密，例如“口袋妖怪-api-base-url”而不是“api-base-url”，因为相同的秘密可以在多个项目中使用。

## 总结起来

本教程到此结束！在将一个`graphql-yoga`服务器从现在的 1.0 迁移到现在的 2.0 时，我面临的主要困难是理解如何设置构建、路由、模式路径和环境变量；希望您现在已经掌握了如何与它们一起工作。

请关注第 2 部分:Now 2.0 的一个核心特性是 monorepo 支持，这意味着您可以在一个项目的根目录下配置一个 *now.json* ，它允许部署多个服务器和前端(甚至使用不同的语言！)-我计划在这篇文章之后给出一个例子，在同一个 repo 中为这个服务器部署一个前端。