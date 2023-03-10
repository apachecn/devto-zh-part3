# React GraphQL 项目的完整堆栈模板

> 原文：<https://dev.to/graphqleditor/fullstack-template-for-react-graphl-project-3g24>

在上周，我们研究了 TypeScript 中的一个基本 GraphQL 服务器实现。在这一集的...

> *GraphQL 服务器在 5 分钟内系列*

...我们来看看 React GraphQL app 的全栈模板。

[![React Apollo GraphQL](img/f5e61014d37bbedba14e23b73abdf2ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IOQgV2SE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zoieh3ffuky45zuv30dc.png)

Fullstack React GraphQL 样板 repo 包含最小、基本和高级样板，每个样板都允许立即引导 GraphQL 服务器，无论您是否想要构建一个简单的“Hello world！”或者全功能的企业 app。

|  | [最小](https://github.com/graphql-boilerplates/react-fullstack-graphql/tree/master/minimal) | [基础](https://github.com/graphql-boilerplates/react-fullstack-graphql/tree/master/basic) | [高级](https://github.com/graphql-boilerplates/react-fullstack-graphql/tree/master/advanced) |
| --- | --- | --- | --- |
| **可扩展的 GraphQL 服务器:**服务器使用基于阿波罗服务器& Express 的 graphql-yoga | ✔️ | ✔️ | ✔️ |
| **预配置的 Apollo 客户端:**该项目附带了一个针对 Apollo 客户端的预配置设置 | ✔️ | ✔️ | ✔️ |
| **GraphQL 数据库:**包含绑定到 Prisma(运行在 MySQL 上)的 GraphQL 数据库 | -好的 | ✔️ | ✔️ |
| **工具:**对 GraphQL Playground &查询性能跟踪的现成支持 | -好的 | ✔️ | ✔️ |
| **可扩展:**简单灵活的数据模型——易于调整和扩展 | -好的 | ✔️ | ✔️ |
| **无配置开销:**预配置的 graphql-config 设置 | -好的 | ✔️ | ✔️ |

### 入门

样板文件只有两个命令的距离。只求:

```
npm install -g graphql-cli
graphql create my-app 
```

然后`yarn start`或`npm run start`将在`localhost:4000`上启动您的基本 GraphQL 服务器。基本的&高级设置需要更多的步骤。

安装 [GraphQL CLI](https://github.com/graphql-cli/graphql-cli) 后，使用
引导 GraphQL 服务器

```
graphql create my-app 
```

当出现提示时，选择所需的样板文件，将 Prisma 服务部署到一个`_public cluster_`，然后导航到新项目的`server`目录并启动它:

```
cd my-app/server

yarn dev 
```

这将在 localhost:4000 上运行一个服务器，以及 GraphQL Playground 打开新终端标签&导航回`my-app`，然后运行应用

```
cd ..
yarn start 
```

在[react-full stack-graph QL repo](https://github.com/graphql-boilerplates/react-fullstack-graphql/tree/master/advanced)中阅读更多关于项目结构或可用命令的信息。

##### [源:](#source-reactfullstackgraphql)[响应-全堆栈图 ql](https://github.com/graphql-boilerplates/react-fullstack-graphql)