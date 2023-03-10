# React & GraphQL 入门第一部分

> 原文：<https://dev.to/robmatyszewski/getting-started-with-react-graphql-part-i-31da>

[Javascript 2018 年调查阶段](https://2018.stateofjs.com)由 2 万名用户完成，其中包含一项分析，分析了人们喜欢&不喜欢网络技术的哪些方面。我发现 React & GraphQL 很有趣，因为它们相互补充，并从调查结论中脱颖而出:

> React 是前端框架中最受欢迎的。
> 
> GraphQL 是最想学习的技术。

接下来，我将流行的样板文件和初学者应用程序结合起来，开始使用 React 和 GraphQL。网上的东西太多了，所以我决定把两篇文章分开。

## 议程:

1.  通用 React+GraphQL 入门套件
2.  SaaS 样板文件
3.  React apollo todo 应用
4.  通用反应阿波罗应用程序
5.  继电器骨架

# [1。通用 React+GraphQL 入门套件](https://reactql.org/)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [李本森](https://github.com/leebenson) / [ reactql](https://github.com/leebenson/reactql)

### 通用 React+GraphQL 入门套件:React 16、Apollo 2、MobX、Emotion、Webpack 4、GraphQL 代码生成器、React 路由器 4、PostCSS、SSR

<article class="markdown-body entry-content container-lg" itemprop="text">

[![ReactQL v4.3.0](img/4f8ae1a404bde5550fea8f0101182494.png)](https://camo.githubusercontent.com/3dc3122f71a509b66f767680445a8277c547323b/68747470733a2f2f7265616374716c2e6f72672f7265616374716c2f6c6f676f2e737667)

[![license](img/98f2af28eb31b9a3d3e824c836716c4e.png)](https://camo.githubusercontent.com/78d537f42349d2218e5084c86270e3dc3a434882/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6c6963656e73652f6c656562656e736f6e2f7265616374716c2e7376673f7374796c653d666c61742d737175617265)[![Twitter Follow](img/762035c65a4b8d616c6e385386281e51.png)](https://twitter.com/reactql)[![Deploy to Netlify](img/c24aadf2449916f84ebbec7fb34ca03d.png)](https://app.netlify.com/start/deploy?repository=https://github.com/leebenson/reactql)

通用前端 React + GraphQL 入门套件，用 Typescript 写的。

[https://reactql.org](https://reactql.org)

## 特征

### 前端堆栈

*   [React v16.8](https://facebook.github.io/react/) (带[挂钩的那个](https://reactjs.org/docs/hooks-intro.html)！)用于 UI。
*   [阿波罗客户端 2.5 (React)](http://dev.apollodata.com/react/) 用于连接 GraphQL。
*   [MobX-React-Lite](https://github.com/mobxjs/mobx-react-lite) 用于声明式、类型安全的流量/存储状态管理。
*   [Emotion](https://emotion.sh/) CSS-in-JS，内嵌`<style>`标签生成，只包含需要渲染的 CSS。
*   导入`.css/.scss/.less`文件时 [Sass](https://sass-lang.com/) ， [Less](http://lesscss.org/) ，[postscs](https://postcss.org/)。
*   [React Router 4](https://reacttraining.com/react-router/) 为声明式浏览器+服务器路由。
*   [GraphQL 代码生成器 v1.1](https://graphql-code-generator.com/) 用于解析远程 GraphQL 服务器模式，用于自动构建完全类型的 Apollo React HOCs，而不是手动编写`<Query>` / `<Mutation>`查询
*   声明/动态`<head>`部分，使用[反应头盔](https://github.com/nfl/react-helmet)。

### 服务器端渲染

*   内置 [Koa 2](http://koajs.com/) 网络服务器，带异步/等待路由。
*   初始 HTML 的完全路由感知服务器端呈现(SSR)。
*   universal building——将浏览器+ Node.js web 服务器编译成静态文件，以实现快速的服务器重生。
*   每个请求的 GraphQL 存储。商店状态通过 SSR 脱水，并且…

</article>

[View on GitHub](https://github.com/leebenson/reactql)

### 前端堆栈

*   [React v16.8](https://facebook.github.io/react/) (带[挂钩的那个](https://reactjs.org/docs/hooks-intro.html)！)用于 UI。
*   [阿波罗客户端 2.5 (React)](http://dev.apollodata.com/react/) 用于连接 GraphQL。
*   [MobX-React-Lite](https://github.com/mobxjs/mobx-react-lite) 用于声明式、类型安全的流量/存储状态管理。
*   [Emotion](https://emotion.sh/) CSS-in-JS，内嵌`<style>`标签生成，只包含需要渲染的 CSS。
*   导入`.css/.scss/.less`文件时 [Sass](https://sass-lang.com/) ， [Less](http://lesscss.org/) ，[postscs](https://postcss.org/)。
*   [React Router 4](https://reacttraining.com/react-router/) 为声明式浏览器+服务器路由。
*   [GraphQL 代码生成器 v1.1](https://graphql-code-generator.com/) 用于解析远程 GraphQL 服务器模式，用于自动构建完全类型的 Apollo React HOCs，而不是手动编写`<Query>` / `<Mutation>`查询
*   声明/动态`<head>`部分，使用[反应头盔](https://github.com/nfl/react-helmet)。

### 服务器堆栈

*   内置 [Koa 2](http://koajs.com/) 网络服务器，带异步/等待路由。
*   初始 HTML 的完全路由感知服务器端呈现(SSR)。
*   universal building——将浏览器+ Node.js web 服务器编译成静态文件，以实现快速的服务器重生。
*   每个请求的 GraphQL 存储。存储状态通过 SSR 脱水，并在客户端自动恢复。
*   MobX 用于应用程序范围的流量/存储状态，用于自动重新呈现任何“监听”状态的 React 组件。全类型状态！
*   整个页面通过内置的 SSR 组件反应-你的 HTML 的每一个字节都是反应。
*   开发和生产中的 SSR，甚至热代码重载。

### 实时

*   热代码重载；零刷新，开发中实时更新。
*   开发 web 服务器，自动发送代码更改的补丁，并重新启动内置的 Web 服务器进行 SSR 渲染，以反映您在生产中看到的内容。
*   WebSocket `subscription`支持实时数据的查询(只需在[中设置`WS_SUBSCRIPTIONS=1`)。环境](//.env)

### 代码优化

*   [Webpack v4](https://webpack.js.org/) ，随着[树摇动](https://webpack.js.org/guides/tree-shaking/)——死代码路径被自动消除。
*   在函数内部进行异步代码加载。
*   针对每个供应商的自动块分割/散列，用于主动缓存(尤其是在 HTTP/2 代理之后！)
*   Gzip/Brotli 静态资产的缩减。
*   CSS 代码被自动组合、缩小和优化——即使你同时使用 SASS、LESS 和 CSS！

### 样式

*   [Emotion](https://emotion.sh/) ，用于内联编写 CSS 样式并生成正确呈现组件所需的最小 CSS。
*   [PostCSS v7](http://postcss.org/) ，带[下一代 CSS](https://preset-env.cssdb.org/) ，导入`.css`、`.scss`或`.less`文件时自动添加厂商前缀。
*   [SASS](http://sass-lang.com) 和 [LESS](http://lesscss.org/) 支持(也通过 PostCSS 解析。)
*   自动厂商前缀——编写现代 CSS，让编译器负责浏览器兼容性。
*   混搭 SASS，少而正则 CSS——不冲突！
*   CSS 模块——你的类被自动散列，以避免命名空间冲突。
*   兼容基础、Bootstrap、Material UI 等。只需通过`.global.(css|scss|less)`导入进行配置，以保留类名。

### 生产就绪

*   通过`npm run production`进行产品捆绑，生成优化的服务器和客户端代码。
*   [静态压缩](https://webpack.js.org/plugins/compression-webpack-plugin/)使用 Gzip 和 [Brotli](https://opensource.googleblog.com/2015/09/introducing-brotli-new-compression.html) 算法将静态资产作为预压缩的`.gz`和`.br`文件(您整个应用的`main.js.bz` -包括所有依赖项-从 346kb 到> 89kb！)
*   通过`npm run build:static`进行静态捆绑。不需要服务器端渲染？没问题。将仅限客户端的 SPA 轻松部署到任何静态 web 主机(Netlify 等)。)

# [2。SaaS 样板](http://codefoundries.com/products/UniversalRelayBoilerplate.html)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [加工精度](https://github.com/MachineAcuity) / [螺纹钢](https://github.com/MachineAcuity/rebar)

### 多租户 SaaS 样板+带有 React、Material-UI、Relay、GraphQL、JWT、Node.js、C * d b-Cassandra/Elassandra/Scylla 的通用 web 应用程序示例。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 钢筋

多租户 SaaS 样板+带有 React、Material-UI、Relay、GraphQL、JWT、Node.js、C * d b-Cassandra/Elassandra/Scylla 的通用 web 应用程序示例。

# 技术栈

## 客户

| **技术** | **描述** |
| --- | --- |
| [找到了](https://github.com/4Catalyzer/found) | React 应用程序的可扩展的基于路由的路由。 |
| [发现-中继](https://github.com/relay-tools/found-relay) | 找到的继电器集成。 |
| [图表 QL](http://graphql.org/docs/getting-started/) | 一种查询语言，用于描述客户端-服务器应用程序的数据模型的功能和要求。 |
| [JSS](http://cssinjs.org/) | JSS 是对 CSS 更强大的抽象。它使用 JavaScript 作为一种语言，以声明性和可维护的方式描述样式。 |
| [材料界面](http://www.material-ui.com/) | React 中实现[材料设计](https://www.google.com/design/spec/material-design/introduction.html)的库。该工具包中的所有用户界面主要是使用材料 UI 组件构建的。 |
| [MDI-物料-用户界面](https://www.npmjs.com/package/mdi-material-ui) | 该模块为所有材质设计图标提供了材质 UI 组件。 |
| [反应](https://facebook.github.io/react/) | 构建基于浏览器的现代移动用户界面的最佳库。 |
| [反应头盔](https://github.com/nfl/react-helmet) | 可重用的 React 组件将管理您对文档头的所有更改，并支持文档标题、元、链接、脚本和基本标签。 |
| [继电器](https://facebook.github.io/relay/) | A |

…</article>

[View on GitHub](https://github.com/MachineAcuity/rebar)

### 前端堆栈

| **技术** | **描述** |
| --- | --- |
| [找到了](https://github.com/4Catalyzer/found) | React 应用程序的可扩展的基于路由的路由。 |
| [图表 QL](http://graphql.org/docs/getting-started/) | 一种查询语言，用于描述客户端-服务器应用程序的数据模型的功能和要求。 |
| [JSS](http://cssinjs.org/) | JSS 是对 CSS 更强大的抽象。它使用 JavaScript 作为一种语言，以声明性和可维护的方式描述样式。 |
| [材料界面](http://www.material-ui.com/) | React 中实现[材料设计](https://www.google.com/design/spec/material-design/introduction.html)的库。该工具包中的所有用户界面主要是使用材料 UI 组件构建的。 |
| [反应](https://facebook.github.io/react/) | 构建基于浏览器的现代移动用户界面的最佳库。 |
| [反应头盔](https://github.com/nfl/react-helmet) | 可重用的 React 组件将管理您对文档头的所有更改，并支持文档标题、元、链接、脚本和基本标签。 |
| [继电器](https://facebook.github.io/relay/) | 用于构建数据驱动的 react 应用程序的 Javascript 框架。 |

### 服务器堆栈

| **技术** | **描述** |
| --- | --- |
| 阿帕奇·卡珊德拉 | 当您需要可扩展性和高可用性而又不影响性能时，这是正确的选择。商用硬件或云基础设施上的线性可扩展性和经验证的容错能力使其成为任务关键型数据的完美平台。Cassandra 对跨多个数据中心复制的支持是同类最佳的，为您的用户提供了更低的延迟，并让您高枕无忧，因为您知道您可以经受住区域性中断。 |
| [数据加载器](https://github.com/facebook/dataloader) | 用作应用程序数据提取层一部分的通用实用程序，通过批处理和缓存为各种后端提供一致的 API，并减少对这些后端的请求。 |
| [艾拉桑德拉](http://www.strapdata.com/) | Elassandra 将 Cassandra 和 Elasticsearch 结合在一个强大的集成解决方案中。URB 和艾拉桑德拉合作得很好。 |
| [快递](https://expressjs.com) | Node.js 的快速、非个性化、极简的 web 框架 |
| [快递-卡珊德拉](https://express-cassandra.readthedocs.io) | Cassandra ORM/ODM/OGM for Node.js，可选支持 Elassandra & JanusGraph |
| [快递图表 QL](https://github.com/graphql/express-graphql) | 允许创建 GraphQL 服务器的 Node.js express 库。 |
| [JWT](https://jwt.io/) | JSON Web Tokens 是一种行业标准 [RFC 7519](https://tools.ietf.org/html/rfc7519) 方法，用于在双方之间安全地表示声明。 |
| [Node.js](https://nodejs.org/en/) | 基于轻量级且高效的 JavaScript 的事件驱动的非阻塞 I/O 运行时。 |
| 温斯顿 | 几乎所有事情的记录者。 |

### 开发工具

| **技术** | **描述** |
| --- | --- |
| [巴别塔](http://babeljs.io) | 将 ESX 传送到 ESX 并执行中继转换。 |
| [同时](https://www.npmjs.com/package/concurrently) | 同时运行多个命令。 |
| [ESLint](https://eslint.org) | 一个完全可插拔的工具，用于识别和报告 JavaScript 中的模式。 |
| [流量](http://flowtype.org/) | 静态类型检查器，旨在发现 JavaScript 程序中的类型错误。 |
| [哈士奇](https://github.com/typicode/husky) | 为 eslint 和更漂亮的 Git 挂钩。 |
| [局部隧道](https://localtunnel.github.io/www/) | 向世界展示你自己。 |
| [更漂亮](https://github.com/prettier/prettier/) | javaScript 的代码格式化程序。 |
| [反应热加载器](//gaearon.github.io/react-hot-loader/) | 允许实时调整 React 组件。 |
| [网络包](http://webpack.github.io) | 捆绑 npm 包、应用程序 Java Script、CSS、图像等。成捆。 |

# [3。React apollo todo app](https://hasura.io/hub/projects/hasura/react-apollo-todo-app)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[k8s-平台-枢纽](https://github.com/k8s-platform-hub) / [反应-阿波罗-待办事项-app](https://github.com/k8s-platform-hub/react-apollo-todo-app)

### 使用 hasura 的 graphql 查询在 react 上构建一个简单的 todo 应用程序

<article class="markdown-body entry-content container-lg" itemprop="text">

# 反应-阿波罗-待办事项-应用程序

这是一个基于 react 的基本 todo 应用程序。该应用程序使用 Hasura 作为其后端，利用了 Hasura 的以下功能:

*   部署 react 应用
*   Hasura 身份验证身份验证 UI 工具包
*   Hasura 数据库
*   Hasura 提供的 GraphQL 查询

# 先决条件

*   确保您在本地机器上安装了 [HasuraCLI](https://docs.hasura.io/0.15/manual/install-hasura-cli.html) 。
*   通过在命令 shell 上运行以下命令登录到 Hasura

```
hasura login
```

# 快速启动

## 获得项目

要获取项目，请在命令 shell 中运行以下命令:

```
hasura quickstart hasura/react-apollo-todo-app
```

## 部署项目

部署项目相当于推送到远程 git repo

```
cd react-apollo-todo-app
git add . && git commit -m "Initial commit"
git push hasura master
```

## 在浏览器中打开 react 应用程序

完成项目部署后，您可以通过以下方式在浏览器中打开 react 应用程序:

```
hasura microservice open ui
```

# React 应用代码

你可以…

</article>

[View on GitHub](https://github.com/k8s-platform-hub/react-apollo-todo-app)

这是一个基于 react 的基本 todo 应用程序。该应用程序使用 Hasura 作为其后端，利用了 Hasura 的以下功能:

*   部署 react 应用
*   Hasura 身份验证身份验证 UI 工具包
*   Hasura 数据库
*   Hasura 提供的 GraphQL 查询

该项目带有以下定制微服务-一个使用 GraphQL 和 Apollo 客户端制作的 React TODO 应用程序

# [4。通用 React 阿波罗 app](https://github.com/WeLikeGraphQL/universal-react-apollo-example)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ WeLikeGraphQL ](https://github.com/WeLikeGraphQL) / [宇宙-反应-阿波罗-例子](https://github.com/WeLikeGraphQL/universal-react-apollo-example)

### 通用反应阿波罗应用程序(GraphQL)消费:https://github . com/welikegraphql/WordPress-graph QL API 示例！

<article class="markdown-body entry-content container-lg" itemprop="text">

# 通用反应阿波罗示例

[![Gitter](img/54a280a815a3981e4f4180450cdfd483.png) ](https://gitter.im/WeLikeGraphQL/react-apollo-example?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge) [ ![Build Status](img/8b728ed8359f369481f41420c9d95321.png)](https://travis-ci.org/WeLikeGraphQL/universal-react-apollo-example)

> 一个应用程序的例子，展示了如何使用一个现代堆栈(强调 GraphQL！).

## 特征

*   [GraphQL](http://graphql.org/) 用于取数据！项目最牛逼的部分！
*   通用渲染(服务器端渲染)
*   带[巴别塔](https://babeljs.io/)的 ES6/ES2015
*   [Apollo 客户端](http://dev.apollodata.com/react/)的 React 数据容器
*   将可预测状态容器 [Redux](http://redux.js.org) 与 Apollo 集成
*   使用[反应热加载器](https://github.com/gaearon/react-hot-loader)进行热加载
*   [将](https://github.com/acdlite/recompose)重组为 React 多功能传送带，用于更高级别的组件
*   由 [Webpack](https://webpack.github.io) 开发和生产捆绑
*   CSSNext :使用未来 CSS 的官方 W3C 语法
*   通过 [PostCSS](http://postcss.org/) 用 JS 插件转换样式
*   用于 CSS 动画的 [Wow.js](http://mynameismatthieu.com/WOW/)
*   [Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) 用于解决[布局疲劳](http://blog.karenmenezes.com/2014/apr/13/floats-inline-block-or-display-table-or-flexbox/)并使应用程序响应迅速(在桌面、移动或其他地方打开应用程序...).在 IE 9 上不起作用，但现在应该不成问题。参见[浏览器用法](http://caniuse.com/usage-table)。
*   CSS 模块:告别 CSS 中的全局范围和大部分冲突
*   [Jest Snapshot Testng](https://facebook.github.io/jest/blog/2016/07/27/jest-14.html) ，一些…

</article>

[View on GitHub](https://github.com/WeLikeGraphQL/universal-react-apollo-example)

### 特性

*   [GraphQL](http://graphql.org/) 用于取数据！项目最牛逼的部分！
*   通用渲染(服务器端渲染)
*   带[巴别塔](https://babeljs.io/)的 ES6/ES2015
*   [Apollo 客户端](http://dev.apollodata.com/react/)的 React 数据容器
*   将可预测状态容器 [Redux](http://redux.js.org) 与 Apollo 集成
*   使用[反应热加载器](https://github.com/gaearon/react-hot-loader)进行热加载
*   [将](https://github.com/acdlite/recompose)重组为 React 多功能传送带，用于更高级别的组件
*   由 [Webpack](https://webpack.github.io) 开发和生产捆绑
*   CSSNext :使用未来 CSS 的官方 W3C 语法
*   通过 [PostCSS](http://postcss.org/) 用 JS 插件转换样式
*   用于 CSS 动画的 [Wow.js](http://mynameismatthieu.com/WOW/)
*   [Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) 用于解决[布局疲劳](http://blog.karenmenezes.com/2014/apr/13/floats-inline-block-or-display-table-or-flexbox/)并使应用程序响应迅速(在桌面、移动或其他地方打开应用程序...).在 IE 9 上不起作用，但现在应该不成问题。参见[浏览器用法](http://caniuse.com/usage-table)。
*   CSS 模块:告别 CSS 中的全局范围和大部分冲突
*   [Jest Snapshot Testng](https://facebook.github.io/jest/blog/2016/07/27/jest-14.html) ，[酵素](http://airbnb.io/enzyme/)和[西农](http://sinonjs.org/)中的部分
*   林挺既有 JS 又有 CSS，分别由: [ESLint](http://eslint.org/) 和 [Stylelint](http://stylelint.io/)

# [5。继电器骨架](https://github.com/fortruce/relay-skeleton)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [福特鲁斯](https://github.com/fortruce) / [接力骨架](https://github.com/fortruce/relay-skeleton)

### React、Relay、GraphQL 项目框架

<article class="markdown-body entry-content container-lg" itemprop="text">

# 反应完整堆栈框架

该框架项目旨在搭建一个完整的 stack React、Relay 和 GraphQL 项目。该框架使用 webpack 和 gulp 来管理构建并提供出色的开发体验。前端堆栈是 React，并且使用 [react-hot-loader](http://gaearon.github.io/react-hot-loader/) 自动热重新加载所有 React 更改。此外，后端服务器会使用 [nodemon](http://nodemon.io/) 自动重启。

服务器和前端代码都是使用 webpack 构建和传输的，而 gulp 主要用于启动 webpack-dev-server 和 nodemon。

## 入门指南

```
npm start  # Launch the GraphQL server and the Webpack dev server
open http://localhost:3000 
```

## 目录结构

```
build/                  // webpack build output
  public/               //  publicly served assets
    index.html
    bundle.js           // frontend bundle  built w/ webpack
  server.js             // backend server   built w/ webpack
src/
  frontend/
    components/         // React components
    containers/         // Relay Containers
    routes/             // Relay Routes
    index.js            // React.render Root component
    index.template.html //
```

…</article>

[View on GitHub](https://github.com/fortruce/relay-skeleton)

这个框架项目旨在搭建一个完整的 stack React、Relay 和 GraphQL 项目。skeleton 使用 webpack 和 gulp 来管理构建并提供良好的开发体验。前端堆栈是反应和中继。使用 react-hot-loader 自动热重装所有 React 更改。此外，后端服务器会在使用 nodemon 进行任何更改时自动
重启。

服务器和前端代码都是使用 webpack 构建和传输的，而 gulp 主要用于启动 webpack-dev-server 和 nodemon。

### 典型用法

这个框架是用后端 api 服务于 React SPA 的典型用例设计的。该框架通过 webpack-dev-server 将所有对`/graphql`的请求自动代理到后端 GraphQL 服务器。

每当您保存文件时，前端会自动热重装。参见
[react-hot-loader]了解更多关于其工作原理的细节。它使您能够立即看到 React 组件中的变化，而不会丢失应用程序状态或重新加载页面！

每当您保存文件时，后端服务器会自动重启。
例如，如果您修改了 GraphQL 模式，那么 GraphQL 服务器将重新启动以反映这些更改，将使用自省查询重新生成`schema.json`，并且将重新编译前端代码以通过`babel-relay-plugin`重新运行`Relay.QL`查询。