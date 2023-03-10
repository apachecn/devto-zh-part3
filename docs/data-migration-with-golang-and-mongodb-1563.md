# 使用 Golang 和 MongoDB 进行数据迁移

> 原文：<https://dev.to/eminetto/data-migration-with-golang-and-mongodb-1563>

[十二因素应用](https://12factor.net)方法解决的一个问题是管理流程的自动化，例如脚本执行和数据迁移。这正是我将在这篇文章中谈论的:我们如何使用 Go 和 MongoDB 自动化数据迁移。

在 [Codenation](https://codenation.dev) 中，我们选择 Go 作为产品开发的主要编程语言。多亏了这个选择，以及采用[干净架构](https://dev.to/en/post/2018-03-05-clean-architecture-using-go/)，我们可以快速创建 API、lambda 函数、命令行应用(CLI)、机器人等等。我们可以重用干净架构层的逻辑来加速产品的开发和演进。

但是对于数据迁移过程，我们还没有找到在 Go 中实现它的简单方法。所以，我们从使用 [node.js](https://www.npmjs.com/package/migrate-mongo) 中的解决方案开始。

该解决方案令人满意地工作了几个月，但是我们在创建迁移脚本时生产率很低。主要原因是我们不熟悉 node.js 的细微差别，尤其是在 MongoDB 中执行的查询的异步行为。由于我们不能重用在 Go 中实现的逻辑，这让我们不得不“重新发明轮子”。

因此，我们做了一项新的研究，并在 Go 中找到了一个解决方案。第一步来自于这个项目的发现:

[https://github . com/xakep 666/mongo-migrate](https://github.com/xakep666/mongo-migrate)

我们在这个项目中做出了一些贡献，我们提出了一个对我们来说很好的解决方案。

第一步是创建负责创建和执行新迁移的 CLI 应用程序。该应用程序的代码如下所示:

[![main](img/d6081f98feb71586841eed7e90239a10.png)](img/posts/migrations_main.png)

让我们开始创建一个新的迁移，使用命令:

```
go run cmd/migrations/main.go new alter-user-data 
```

Enter fullscreen mode Exit fullscreen mode

结果是这样的:

```
2019/01/23 10:02:36 New migration created: ./migrations/20190123100236_alter-user-data.go 
```

Enter fullscreen mode Exit fullscreen mode

该命令所做的是复制文件 **migrations/template.go** 创建一个新的迁移。这是一个*模板的内容:*

[![main](img/0b2af6ce07fdd097100173a49033d86b.png)](img/posts/migrations_template.png)

我们现在可以更改这个新文件来执行命令。例如:

[![main](img/dc88695dfcd7de58b8209ddab11af977.png)](img/posts/migrations_migration.png)

要执行迁移，您需要:

```
go run cmd/migrations/main.go up 
```

Enter fullscreen mode Exit fullscreen mode

要撤消迁移，请执行以下操作:

```
go run cmd/migrations/main.go down 
```

Enter fullscreen mode Exit fullscreen mode

当我们执行 **up** 命令时，检查集合**迁移**以查看最后一次迁移是否成功。自动执行那些仍然未决的，在这种情况下**20190123090741 _ alter-user-data . go**并且集合被更新。这是在应用程序新版本的部署过程中执行的命令。

**down** 命令通过执行迁移逻辑并将其从集合中移除来执行相反的过程。

我们可以在此库中访问这些示例的代码:

[https://github.com/eminetto/clean-architecture-go](https://github.com/eminetto/clean-architecture-go)

有了这个解决方案，我们可以提高我们的生产力，因为我们在 Go 方面比在 node.js 方面更有经验。此外，我们可以重用项目中创建的代码，例如 Clean Architecture 的用例。我们甚至可以为迁移创建单元测试，这应该是我们实现的下一步。

如果你使用 double Go + MongoDB 我相信这个解决方案可能是有用的，我希望我有所帮助。