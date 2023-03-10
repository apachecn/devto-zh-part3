# GraphQL 基础:第 1 部分——Prisma(为什么)

> 原文：<https://dev.to/doylecodes/graphql-basics-part-1---prisma-the-why-45h0>

## 我们开始吧

在我的上一篇关于我为什么使用 GraphQL 的文章之后，我开始着手用我的 GraphQL 后台建立一个小的教程项目。我以前在教程中设置过后端，但这是第一次从头开始做所有的事情，我不得不说这是我为自己做的最好的事情。我在我的上一篇文章中说过，但是我发现知道你理解某事(或者认为你至少理解它)的最好方法是试着把它教给别人。在这篇文章中，我希望能为 Prisma 的入门打下基础。

## 为什么/(什么是)Prisma？

好的，所以你不需要在 GraphQL 中使用 Prisma，但是当我第一次看到 Prisma 可以做什么时，它节省了大量的时间。本质上，Prisma 为您的数据库创建了所有可能的 CRUD 操作。这并不意味着它创建了所有的逻辑，只是创建了与数据库交互的实际方法。

这种巫术是如何运作的？！
嗯，这不完全是巫术。基本上，你告诉 Prisma 你的数据模型是什么，它从那里开始创建你所有的基本 CRUD 操作。Prisma 的另一种思维方式是，你在提供你的数据模型或者你想保存在你的数据库中的对象的结构，Prisma 做一个 API 让你和数据库交互。

**例如:**
想象从你的数据库中读取数据。假设您将“文章”存储在数据库中。它们可以有这样的结构:

```
type Post {
  id: ID! @unique
  title: String!
  published: Boolean!
  author: User
} 
```

Enter fullscreen mode Exit fullscreen mode

当您设置 prisma 时，您将把它写在一个文件(datamodel.prisma)中，并将它部署到 Prisma。然后，您可以下载一个生成的文件，该文件为这些对象提供了各种查询(读取)和变化(创建、编辑、删除)。因此，对于您提供的数据模型，您将得到类似这样的结果:

```
type Query {
  post(where: PostWhereUniqueInput!): Post
  posts(where: PostWhereInput, orderBy: PostOrderByInput, skip: Int, after: String, before: String, first: Int, last: Int): [Post]!
  postsConnection(where: PostWhereInput, orderBy: PostOrderByInput, skip: Int, after: String, before: String, first: Int, last: Int): PostConnection!
  node(id: ID!): Node
}

type Mutation {
  createPost(data: PostCreateInput!): Post!
  updatePost(data: PostUpdateInput!, where: PostWhereUniqueInput!): Post
  updateManyPosts(data: PostUpdateManyMutationInput!, where: PostWhereInput): BatchPayload!
  upsertPost(where: PostWhereUniqueInput!, create: PostCreateInput!, update: PostUpdateInput!): Post!
  deletePost(where: PostWhereUniqueInput!): Post
  deleteManyPosts(where: PostWhereInput): BatchPayload!
} 
```

Enter fullscreen mode Exit fullscreen mode

看看《突变》中的删除许多帖子。我首先想知道的一件事是...所以我可以看到它接受这个“where”参数，但是 PostWhereInput 应该是什么呢？幸运的是，所有这些都提供了！下面来看看:

```
input PostWhereInput {
  id: ID
  id_not: ID
  id_in: [ID!]
  id_not_in: [ID!]
  id_lt: ID
  id_lte: ID
  id_gt: ID
  id_gte: ID
  id_contains: ID
  id_not_contains: ID
  id_starts_with: ID
  id_not_starts_with: ID
  id_ends_with: ID
  id_not_ends_with: ID
  title: String
  title_not: String
  title_in: [String!]
  title_not_in: [String!]
  title_lt: String
  title_lte: String
  title_gt: String
  title_gte: String
  title_contains: String
  title_not_contains: String
  title_starts_with: String
  title_not_starts_with: String
  title_ends_with: String
  title_not_ends_with: String
  published: Boolean
  published_not: Boolean
  author: UserWhereInput
  AND: [PostWhereInput!]
  OR: [PostWhereInput!]
  NOT: [PostWhereInput!]
} 
```

Enter fullscreen mode Exit fullscreen mode

那是相当多的选择！你可以看到，如果我只是想删除一堆帖子，我可以使用
`deleteManyPosts(where: { author: thatCrazyGuyThatGotBanned })`。
疯狂发帖的用户会被删除所有帖子！就这么简单！

## 原来如此？

嗯，不完全是。Prisma 的美妙之处在于，它为您的 CRUD 操作提供了所有可能的方法。你不需要担心创建一个方法，比如说一个商店里的商品，来计算如何根据它们的价格来查询它们。Prisma 通过给你大于，小于，id，所有这些东西来帮你。它也是数据库不可知的，所以你可以使用许多不同类型的数据库，如 MySQL 或 Mongo。

Prisma 没有给你的是逻辑，或者实际的控制器方法，你需要在你的应用程序中实际实现这个 Prisma“API”因此，如果您正在开发一个应用程序，其中有人可以发帖，那么您仍然需要编写逻辑来确保，例如，他们已经登录或者拥有正确的权限。但是一旦你解决了这个问题，你就可以很容易地达到
`createPost(data: putYourDataHere )`。

## 设置棱镜

在我的下一篇文章中，我将使用 Prisma 的演示数据库来设置 Prisma。这是为个人项目做好一切准备或了解一切的好方法。如果你不耐烦，去 [Prisma.io](https://www.prisma.io/) 看看他们的教程。在我的例子中，我将尝试把它分解得更简单一点，并设置它自动生成和下载 prisma.graphql 文件。您可以查看 prisma 为您生成了什么。[现在就来看看第二部分吧！- >](https://dev.to/ryanmdoyle/graphql-basics-part-2---prisma-the-how-1356)

## 文章不再可用