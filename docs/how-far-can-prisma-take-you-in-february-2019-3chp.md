# Prisma 能带你走多远？(2019 年 2 月)

> 原文：<https://dev.to/sgwilym/how-far-can-prisma-take-you-in-february-2019-3chp>

在一个单独的项目中，你需要做一些你不擅长的事情。在我的例子中，那是数据库。我有两个选择:

*   A.做正确的事，最后学会 SQL
*   B.保持幸福的无知，使用一些利用我现有知识的东西，让我继续下去。

在过去的几年里，我在 GraphQL 客户端和 API 方面做了很多工作。 **Prisma** 是一个工具，它提供了一种方法来把 GraphQL SDL 变成这样:

```
type  User  {  id:  ID!  @unique  name:  String!  email:  String!  posts:  [Post!]!  friends:  [User!]!  }  type  Post  {  id:  ID!  @unique  title:  String!  body:  String!  isDraft:  Boolean!  author:  User!  } 
```

Enter fullscreen mode Exit fullscreen mode

到一个正在运行的数据库中，你可以在你自己的应用程序中读/写它，就像这样:

```
let newUser = prisma.createUser({
  name: "Sally Moobles",
  email: "sally@moobleit.biz",
});

let newPost = prisma.createPost({
  title: "My Great Post",
  body: "I'll finish this later…",
  isDraft: true,
  author: {
    connect: {
      id: newUser.id,
    }
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

通过编写单个 SDL，您可以获得一个数据库和一个从中读写数据的客户端。这有多酷怎么说都不为过！它像宣传的那样工作——您可以在很短的时间内从一无所有到 ORM，可以选择数据库技术(例如 Postgres ),并且生成的客户端与 Typescript 这样的语言结合起来非常好，您可以对所有不同的操作进行自动完成。这将是一个巨大的事件。

但这篇文章是关于截至 2019 年 2 月的 Prisma，以及它可以带你走多远*现在*。下面是一些阻止它成为我今天想要带入生产环境的东西的原因。

## 无数据迁移

当您更改 SDL 并运行`prisma deploy`时，Prisma 会自动迁移您的数据库。但是它不知道如何迁移数据。这意味着重要的模式更改，如创建新的必填字段或更改字段类型，必须手动处理。没有 Rails 式的向上或向下迁移来告诉 Prisma 数据在下一个模式中应该是什么样子。对于新的必填字段，您首先将该字段创建为可选字段，使用脚本填写所有数据，然后打开必填标志并重新部署。

当你用虚拟数据单独工作时，这是…可以忍受的。*就*。但是这种方法怎么可能与在不同的分支工作、部署到不同环境的团队一起工作呢？你是怎么做 CI 的？答案是:非常单调和缓慢。

有一个关于迁移的[问题](https://github.com/prisma/prisma/issues/1263)，它在一个月前被标记为过时。[Prisma 网站](https://www.prisma.io/migrate/)暗示，未来会有更好的系统。它什么时候来？你现在应该做什么？真的不是很清楚。

这引出了我认为 Prisma 现在面临的另一个最大的问题:

## 支持/沟通问题

很难知道 Prisma 的路线图是什么，他们在功能方面的优先顺序是什么，或者许多人要求的功能是否被认为是重要的。有很多问题提供了新功能的规格，但很长时间没有更新。

毫无疑问，Prisma 团队正在努力工作，但缺乏对他们工作和计划的定期交流不可避免地会影响您作为最终用户的信心。这让我想到，这很有趣，但我可能最终不得不以传统的方式来做这件事。

他们似乎在支持方面也有点问题。因为 Prisma 将自己定位为一种简单的做事方式，所以它吸引了很多初学者——这是一件很棒的事情——他们会问很多问题。在他们的 Slack、论坛和 Github 上，看起来大多数问题都没有答案。过去，Prisma [有专人专门支持离开](https://www.prisma.io/forum/t/thank-you-nilan/2436)的人。谁接下来做得好，他们就应该给谁报酬！

## 我在做项目时碰到的一些小事的非详尽列表

*   非平凡的唯一约束。使用 SDL，你可以指定某些字段(例如用户的电子邮件)应该是唯一的，但是*你不能*做一些事情，比如说所有的文章标题在其用户的上下文中应该是唯一的。这显然将由一个神秘的[数据模型 v2](https://github.com/prisma/prisma/issues/3408) 来解决。
*   文本字段不区分大小写
*   暴露连接。连接是一种规范，用于显示您希望分页或只捕获一小部分的长数据列表。将从 Prisma 检索到的连接暴露给自己的面向客户端的 GraphQL API 需要一些奇怪的变通方法。
*   将生成的 Prisma 客户端与 Typescript 一起使用是很棒的，但是弄清楚如何将它与 Javascript 一起使用是很痛苦的。方法名称是根据 SDL 中的字段名称生成的，因此您必须自己弄清楚名称是什么以及它们采用哪种参数。尤其是争论可能非常非常深刻，很容易搞砸。

## 终于

我写这些的原因是因为*我想让 Prisma 成为我唯一急需使用的东西*。以上几点就是为什么我认为我现在做不到的原因。但是有很多理由相信它会到达那里。

但是伙计，等待真糟糕。