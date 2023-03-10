# GraphQL 服务器在 5 分钟内完成(TypeScript)

> 原文：<https://dev.to/graphqleditor/graphql-server-under-5-minutes-ts-c2e>

如果你认为建立一个 GraphQL 服务器是一项艰巨的任务，那么 [GraphQL 样板文件](https://github.com/graphql-boilerplates) Github repo 将证明你大错特错。

GraphQL 最好的一点是它可以用于任何技术。让我们来看看最流行的 web 技术的初始项目，由最好的 GraphQL 开发者之一提供。现在首先，让我们看看[的打字稿](https://www.typescriptlang.org/)。

### **TypeScript-graph QL-server**

TypeScript 是 JavaScript 的类型化超集，可以编译成普通 JavaScript，其流行程度不断上升。TypeScript 样板报告包含两个初学者工具包:

*   [基础](https://github.com/graphql-boilerplates/typescript-graphql-server/tree/master/basic)
*   [高级](https://github.com/graphql-boilerplates/typescript-graphql-server/tree/master/advanced)

两者都允许在几秒钟内用 TypeScript 引导您的 GraphQL 服务器。无论您是需要一个简单个人项目的 **[基本选项](https://github.com/graphql-boilerplates/typescript-graphql-server/tree/master/basic)** 还是具有更多特性的 **[高级样板](https://github.com/graphql-boilerplates/typescript-graphql-server/tree/master/advanced)** 您都可以获得可扩展的、生产就绪的 GraphQL 服务器。

所有项目都基于`graphql-yoga`，这是一个 GraphQL 服务器库，致力于提供最简单的设置。

让我们来看看这些版本中的可用功能:

|  | [基础](https://github.com/graphql-boilerplates/typescript-graphql-server/tree/master/basic) | [高级](https://github.com/graphql-boilerplates/typescript-graphql-server/tree/master/advanced) |
| --- | --- | --- |
| **可扩展的 GraphQL 服务器:**服务器使用基于阿波罗服务器& Express 的 graphql-yoga | ✔️ | ✔️ |
| **静态类型生成:**graph QL 查询的类型脚本类型&在构建步骤中生成 | ✔️ | ✔️ |
| **GraphQL 数据库:**包含绑定到 Prisma(运行在 MySQL 上)的 GraphQL 数据库 | ✔️ | ✔️ |
| **工具:**对 GraphQL Playground &查询性能跟踪的现成支持 | ✔️ | ✔️ |
| **可扩展:**简单灵活的数据模型——易于调整和扩展 | ✔️ | ✔️ |
| **无配置开销:**预配置的 graphql-config 设置 | ✔️ | ✔️ |
| **实时更新:**支持 GraphQL 订阅 | -好的 | ✔️ |
| **认证:**注册和登录工作流已准备好供您的用户使用 | -好的 | ✔️ |

两个版本的设置相同:

```
graphql create my-app 
```

出现提示时，选择所需的样板文件，将 Prisma 服务部署到一个*公共集群*，然后导航到新项目的服务器目录并启动它:

```
cd my-app/server

yarn dev 
```

我们的项目结构将是这样的:

[![Project Structure](img/b8c20e17963421bde72e71ed3f6e8930.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VgO-mWmm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qyy75tjyfkh1qnzf34fh.png)

##### 来源: