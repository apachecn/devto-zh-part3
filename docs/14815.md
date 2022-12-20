# 使用 GraphQL 和 Prisma 按创建日期排序

> 原文：<https://dev.to/mikewheaton/sorting-by-created-date-with-graphql-and-prisma-318c>

在与 Prisma 和 GraphQL 一起做兼职项目时，我遇到了一个常见的情况。我想显示从最新到最早排序的项目列表。

我的第一个想法是向模型中添加一个用于创建日期的字段，就像其他任何字段一样。这可以在创建项目时在变异解析器中设置。

```
type Item {
  ...
  created: DateTime!
} 
```

但是，我担心将来它可能会被意外更新。鉴于这种需求如此普遍，我认为可能会有更好的解决方案。

通过挖掘文档，我了解到 Prisma 的[系统字段](https://www.prisma.io/docs/reference/service-configuration/data-modelling-(sdl)-eiroozae8u#system-fields)。这些是包含在底层数据库中的只读字段，即使它们没有在数据模型中定义。它们是每个节点的一种隐藏元数据。

是的，通过添加一个带有匹配指令的“createdAt”字段，可以将这些暴露给 GraphQL API。不要在这里发挥创造力，名字必须准确。

```
type Item {
  ...
  createdAt: DateTime! @createdAt
} 
```

现在可以像查询任何其他字段一样查询它。Prisma 根据该字段生成过滤和排序的方法。为了我的需要，我做到了。

```
items(orderBy: createdAt_DESC) {
    id
    name
    description
    createdAt
} 
```

这就是全部了！由于这个字段一直存在于表面之下，它*已经有了所有现有项目*的数据。这被证明是一个巨大的时间节省，是一个我将从现在开始经常使用的功能，与“updatedAt”系统字段一起使用。