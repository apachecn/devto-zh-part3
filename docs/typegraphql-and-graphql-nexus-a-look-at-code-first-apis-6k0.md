# TypeGraphQL 和 GraphQL Nexus——看看代码优先 API👀

> 原文：<https://dev.to/novvum/typegraphql-and-graphql-nexus-a-look-at-code-first-apis-6k0>

自从 Prisma 团队[宣布 GraphQL Nexus](https://www.prisma.io/blog/introducing-graphql-nexus-code-first-graphql-server-development-ll6s1yy5cxl5/) 以来，人们对代码优先的模式开发产生了浓厚的兴趣。在[novuvum](https://www.novvum.io/)这里，我们认为这是朝着正确方向迈出的一大步，但是关于 [Nexus](https://nexus.js.org/) 与 [TypeGraphQL](https://typegraphql.ml/) 有何不同，一直存在很多困惑。这篇文章旨在打破差异，并希望澄清困惑。

#### 代码优先:用于构建 GraphQL 模式的编程 API

在我们开始之前，让我们回顾一下为什么会出现代码优先的模式开发。Node.js 生态系统中编写 GraphQL 服务器的一种流行方法是模式优先或 SDL 优先。在模式优先的约定中，首先用模式定义语言(SDL)定义一个模式，并分别编写解析器。如果使用的是 TypeScript，则需要再次定义要在代码中使用的架构类型。因此，对模式进行更改需要在三个不同的地方进行更改——SDL、冲突解决程序和 TypeScript 类型。

在代码优先中，模式在代码的中定义*，SDL 作为*工件*生成。您不再需要跟踪多个位置的模式更改！🎊这让我们想到了 Nexus 和 TypeGraphQL。这两个库都旨在帮助代码优先的模式开发，但是它们在工作方式上有很大的不同。*

#### 比较 GraphQL Nexus 和 TypeGraphQL 🗒的 API

##### 类型

在构建 GraphQL 模式时，大量的时间都花在编写类型上。正因为如此，Nexus 和 TypeGraphQL 都试图让这个过程尽可能简单。

给定以下 SDL 类型:

[![SDL type image](img/a3f43fa71a767fc0d1b45520ee560e82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kNfnvIgN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.website-files.com/5c2fa00d1b7e00754f79fd3e/5cb61ac138605d021abd5a79_1%2A9NrTjz7jWH94dh7S2jqS-A.png)

下面是它在 TypeGraphQL 和 GraphQL Nexus 中的定义:

[![TypeGraphQL1](img/2a61f568f53368c3c614c648b142279b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CGv6vZqp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.website-files.com/5c2fa00d1b7e00754f79fd3e/5cb61ac1d3250d9f255975d0_1%2A8cYgWicNS9zPz_sd52krxQ.png)

[![Nexus1](img/b12e216b7c2cc517b717d34068158b98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7wngqk2J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.website-files.com/5c2fa00d1b7e00754f79fd3e/5cb61ac1a8c107273dcce60b_1%2ATEQjRCLll5xlov1T8BvGjA.png)

###### 【打字| top graphql nexus | bottom】

如您所见，这两个库的 API 非常不同。TypeGraphQL 使用类并依赖于一个名为 [decorators](https://www.typescriptlang.org/docs/handbook/decorators.html) (以@符号开头的代码行)的实验性类型脚本特性。GraphQL Nexus 使用本地 JavaScript 语法。

使用 TypeGraphQL，TypeScript 类型就是我们编写的用户类。它还可以与其他基于装饰器的库很好地集成，如 [TypeORM](https://github.com/typeorm/typeorm) 、 [sequelize-typescript](https://github.com/RobinBuschmann/sequelize-typescript) 或 [Typegoose](https://github.com/szokodiakos/typegoose) 。

另一方面，Nexus 在我们开发时自动生成类型定义，并在我们的代码中推断它们，为我们提供 IDE 完成和类型错误捕捉。它还与即将推出的 [Yoga 2](https://github.com/prisma/yoga2) 无缝集成了各种数据库。更新:[这里](https://www.prisma.io/blog/using-graphql-nexus-with-a-database-pmyl3660ncst)是一篇概述如何集成 GraphQL Nexus 和 Prisma 的博客文章。

##### 解析器

好了，当涉及到定义类型和消除模式定义和 TypeScript 类型之间的冗余代码时，这两个库都非常简单。现在，我们该怎么写解析器呢？这是两个库采取非常不同的方法的地方。

下面是我们如何在两个库中为我们的用户类型添加解析器:

[![Resolver1](img/1d5029b7629941c9c696045d8b577804.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uhMhJFDV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.website-files.com/5c2fa00d1b7e00754f79fd3e/5cb61ac1cc44c0b52e3b7b03_1%2AEUoMvvGBGHf0g5hdcuwePQ.png)

[![Resolver2](img/57c7e424adea0ad2218adf38476a588f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--15TclLId--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.website-files.com/5c2fa00d1b7e00754f79fd3e/5cb61ac1de49b87d4c9c90d0_1%2Au90eCZeVUxQ7dVC4PN3n0A.png)

###### 打字| top:图表 nexus | bottom

使用 TypeGraphQL，解析器独立于类型定义，类似于 SDL 第一范式(编辑:我站在[纠正](https://twitter.com/typegraphql/status/1118946711102197760))。可以在与类型定义相同的位置定义[解析器。posts 字段的解析器是使用@FieldResolver() decorator 定义的。您可以分别使用@Query(returns = >用户)和@Mutation(returns = >用户)装饰器为根查询和变异添加字段。](https://github.com/19majkel94/type-graphql/blob/v0.17.1/examples/simple-usage/recipe-type.ts#L25-L33)

对于 GraphQL Nexus，解析器是类型定义的一部分。另一个好处是，我们在编写解析器时获得了代码完成，因为 Nexus 会自动为解析器生成类型脚本类型。

#### 仔细查看 API 设计决策🔭

我将让这两个库从它们的文档中为自己辩护。

[类型图 QL](https://typegraphql.ml/docs/introduction.html#why) :

要向我们的实体添加一个新字段，我们必须遍历所有文件:修改实体类，然后修改模式，最后更新接口。输入或参数也是如此:很容易忘记更新其中的一个或在类型上出错。另外，如果我们在字段名中打错了怎么办？重命名功能(F2)将无法正常工作。

基于一年多来在 TypeScript 中开发 GraphQL APIs 的经验，TypeGraphQL 开始解决这些问题。主要思想是通过使用类和一点装饰器帮助来定义模式，从而只有一个真实的来源。依赖注入、验证和授权保护等附加功能有助于完成通常必须由我们自己处理的常见任务。

[GraphQL 关系图](https://nexus.js.org/docs/why-graphql-nexus):

GraphQL Nexus 的核心思想来自于基于 SDL 的模式——保持事物的声明性和简单易懂。它允许您将类型名作为字符串引用，而不是总是需要导入引用类型(如果您愿意，也可以这样做)。

通过将自动类型生成与 TypeScript 的一些更强大的功能(类型合并、条件类型和类型推断)结合起来，我们可以准确地知道我们引用了哪些类型名称，并能够在整个代码中使用这些名称。我们可以知道解析器的参数和返回类型，而无需提供任何类型注释。这需要一点时间来适应，但最终会导致一个巨大的自我注释类型的反馈循环。

#### 总结:选哪个？

我希望这些例子能帮助您理解这两个库的不同之处。让我们来看看每个库的一些权衡。

##### 类型图 QL

优点:

*   将跟踪模式的位置从三个减少到两个或一个。TypeScript 类型和架构类型是组合在一起的，但是冲突解决程序可以单独定义，也可以与类型定义一起定义。
*   已经存在了很长时间，所以在编写本文时，它有更多的特性，比如字段验证和授权。
*   你所看到的就是当涉及到 TypeScript 类型时你所得到的。

缺点:

*   仅适用于 TypeScript，因为它依赖于[元数据反射](https://github.com/rbuckton/reflect-metadata)。
*   当定义字段和解析器时，我们注释类型的方式感觉是多余的，这可能导致无声的不匹配错误。更多详情[此处](https://github.com/19majkel94/type-graphql/issues/296)。

注意:如果您目前正在使用 TypeGraphQL，并且它正在为您或您的团队提供价值，请[向他们捐赠](https://opencollective.com/typegraphql)！

##### GraphQL 关系

优点:

*   将跟踪模式的位置从三个减少到一个。架构、类型脚本类型和解析器都在一个地方。
*   坚持使用标准 JavaScript 语法并生成 TypeScript 类型，因此它适用于两种语言。
*   ide 的自动完成和类型检查支持为开发人员提供了很好的体验。

缺点:

*   两个库中较年轻的一个，所以它缺少一些有用的特性。有些人已经在地平线上。
*   由于它依赖于类型生成，服务器必须在开发时运行。更多信息[点击这里](https://github.com/prisma/nexus/issues/77)。

#### 我们更喜欢哪一个🙌

在 Novvum，我们一直在广泛使用 Nexus，并且我们移植了 MarvelQL ，一个围绕漫威 API 的 GraphQL 包装器，来使用它。在尝试了 TypeGraphQL 之后，我们觉得 Nexus 更加友好，给了我们更多的灵活性，让我们能够更快地行动。然而，这对我们来说很好，我们知道所有的团队都有不同的运作方式。

#### 让我们知道你对✍️的看法

我希望这篇文章对那些不确定这两个库有什么不同的人有所帮助。如果还有困惑，请[联系](https://www.novvum.io/contact-us)我们！

###### 关于我们: [Novvum](https://www.novvum.io/) 是一家专业从事工程、战略、&设计的现代软件开发机构。