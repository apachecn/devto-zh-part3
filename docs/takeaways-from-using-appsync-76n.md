# 使用 AppSync 的要点

> 原文：<https://dev.to/pfulop/takeaways-from-using-appsync-76n>

[![](img/6c64e95bdf487bcbd751ed2421d20ab0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cqR-P41w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AnrM0x66w4UCfs_clT8h9QQ.png) 

<figcaption>**来源:undraw.co**</figcaption>

最近我一直在为我的朋友开发一个 web 应用程序，并决定使用 AppSync(起初我使用我的旧堆栈，但后来改用 AppSync)。一路走来，我学到了一些东西，与世界分享这些东西似乎是理所当然的。

该应用程序将很快发布，它由您访问数据的典型查询组成，包括分页结果和变更/创建/删除记录。除此之外，它还包括一些对应用程序的整体业务逻辑至关重要的订阅。它还依赖 dynamoDB 触发器来处理某些数据被删除以及服务器端需要更多操作的情况。

### 为什么要 appsync

我正在考虑暂时改变我通常的堆栈，进入整个无服务器的世界。考虑到当时的服务器提供商不允许我使用 Docker 容器，这使得环境分离变得复杂，这个想法看起来很有希望。此外，这个应用程序有巨大的增长潜力，并且在某些方面 AWS 已经处理了可扩展性。

#### 我以前用过什么

在跳上无服务器宣传列车之前，我使用我的默认堆栈，包括 MySQL/PostgreSQL，react，一个使用 apollo-server-express 的 GraphQL 服务器。通常都在你的标准 VPS 中托管。

### 美好的事物

我注意到(并立即爱上)的第一件事是，我可以在 yaml 文件中编写任何东西来定义我需要的所有东西。也许这是一个很好的机会来说明我使用的是无服务器框架，而不是 amplify。我最初想尝试 amplify-cli，但当我想在一个特定的路由上引入 SSR 并设置该路由的缓存策略时，感觉时间很短。据我所知，在高级配置方面，amplify-cli 还不存在。

无服务器框架允许我指定 yaml 文件中的所有内容，并用一个命令部署它们。从 AppSync 网关到数据库、函数、SSR 入口点、CloudFront 设置、lambda@edge 函数、域和 SSL 证书的一切。这真的很实用，而且有一些插件很容易安装。

另一个好处是可以指定从查询/变异到 dynamoDB 表的直接映射。很多时候，我不需要写必要的代码来做简单的操作，相反，我只是复制一些 VTL 模板并修改它们。现在可以将它们链接起来，所以操作可能会稍微复杂一些。当我别无选择，只能写 lambda 函数时，通常，我有所有可用的环境变量来快速编写操作逻辑。

我还喜欢 AppSync 客户端基本上是一个阿波罗客户端，所以这是我非常熟悉的领域。编写订阅也变得更容易了，我只需指定哪个突变应该触发客户端的更新。