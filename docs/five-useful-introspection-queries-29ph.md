# 五个有用的自省查询

> 原文：<https://dev.to/moontahoe/five-useful-introspection-queries-29ph>

我们都喜欢类型系统，或者至少，我们大多数人都同意类型系统通常是好的。他们可以帮助我们消除 bug。它们提供了一个正式的结构，使得整合工具和自动化成为可能。它们为我们提供了一种交流代码结构的方式。所以如果类型系统很好，那么你应该爱上 GraphQL。GraphQL 是您的 API 的类型系统。

在 GraphQL 中，所有类型都在一个**模式**中定义。每个 GraphQL API 都有一个。您可以使用 GraphQL 中的“Docs”选项卡或 GraphQL Playground 中的“schema”标签来浏览模式。GraphQL 和 GraphQL playground 都通过查询端点来获取模式本身的详细信息，从而构建模式文档浏览器。这些查询称为自省查询，您可以自己将它们发送给 GraphQL APIs。

本文探讨了一些常见的自省查询，从帮助理解 GraphQL API 到为 GraphQL 生态系统构建工具和自动化，您都可以使用这些查询。让我们找点乐子！加载 GraphQL 或 GraphQL Playground 并插入您最喜欢的 GraphQL 端点。您将向该端点询问有关其自身的问题。

如果你没有最喜欢的 GraphQL API，你可以使用[的 Snowtooth API](https://snowtooth.moonhighway.com) 。这是一个假滑雪场的真正的 GraphQL API。你也可以使用[星球大战 API，或者 SWAPI](https://graphql.org/swapi-graphql) 来练习发送自省查询。或者从这个[公共 GraphQL 端点列表](https://github.com/APIs-guru/graphql-apis)中选择一个端点。
T9】

## 查询模式中所有可用的类型

我发送给 GraphQL API 的第一个自省查询是一个请求该 API 模式中所有可用类型的查询:

```
 query  allSchemaTypes  {  __schema  {  types  {  name  kind  description  }  }  } 
```

使用`__schema`字段，我们可以要求 GraphQL API 提供模式中定义的所有类型的列表。这个特定的查询将返回每种类型的`name`和`description`。我们还要求每种类型的`kind`。种类是指用来定义类型的 GraphQL 构造函数:`SCALAR`、`OBJECT`、`INTERFACE`、`UNION`、`ENUM`、`INPUT_OBJECT`、`LIST`、`NON_NULL`。该查询提供了特定 GraphQL 端点上可用类型的概述。

如果`description`字段返回`null`，这意味着模式没有提供类型描述。为了充分利用类型系统，在起草模式时正式描述每个类型总是一个好主意。

## 所有可用的查询

关于 API，我们需要知道的第一件事是我们可以向它发送哪些查询？下面的自省查询将列出所有可以发送到 api 的根查询:

```
query  availableQueries  {  __schema  {  queryType  {  fields  {  name  description  }  }  }  } 
```

`__schema`字段允许我们请求`queryType`，它返回查询类型`OBJECT`，其中包含我们可以发送到该端点的根查询。列出这个`OBJECT`的`fields`将提供那些可用根查询的列表。这里我们要求每个字段的`name`和`description`。您还可以通过查询`mutationType`找到所有的根突变，或者通过查询`subscriptionType`找到所有的根订阅。

## 关于个人类型的详细信息

一旦你收到了一个类型列表，你可以发送一个自省查询，询问任何一个类型的细节:

```
# Replace <TYPE> with the name of an actual type  query  liftType  {  __type(name:  "<TYPE>")  {  fields  {  name  description  }  }  } 
```

在上面的例子中，我们必须为我们想要查询的类型提供一个名称。如果您使用的是 [Snowtooth](https://snowtooth.moonhighway.com) ，您可以查询“升降机”或“拖车”类型。如果你正在使用 [SWAPI](https://graphql.org/swapi-graphql) ，你可以要求“电影”或“物种”类型。

该查询返回特定类型的详细信息。在这种情况下，我们需要特定类型中定义的每个字段的`name`和`description`。

## 枚举器值列表

GraphQL 中的枚举器很棒。它们允许我们将字段的可能值限制在一个特定的列表中。当您想知道这些值是什么时，您可以发送下面的自省查询:

```
# Replace <ENUM TYPE> with the name of an actual enumeration type  query  EnumerationValues  {  __type(name:  "<ENUM TYPE>")  {  kind  name  description  enumValues  {  name  description  }  }  } 
```

上面的查询要求枚举类型的`kind`、`name`和`description`以及一个`enumValues`列表。这将提供枚举类型中定义的每个选项的`name`和`description`。

我们只能查询`ENUM`类型上的枚举值。这意味着类型的`kind`必须是一个`ENUM`。如果您使用的是 snowtooth，您可以查询“LiftStatus”。如果您使用的 API 没有在模式中定义任何枚举，您可以始终使用“__TypeKind”。这是一个用于自省的`ENUM`类型。它将返回所有可用类型的列表。

## 与一个接口或联合相关的所有类型

如果您的 GraphQL API 定义了任何联合或接口，您可以找出哪些类型与这些联合相关联或者由这些接口实现:

```
# Replace <INTERFACE OR UNION TYPE> with the name of an actual interface or union type  query  UnionInterfaceTypes  {  __type(name:  "<INTERFACE OR UNION TYPE>")  {  kind  name  description  possibleTypes  {  name  kind  description  }  }  } 
```

上面的查询将告诉您什么类型与一个联合或接口相关联。为了使这个查询工作，您必须在定义联合或接口类型的 GraphQL API 上使用它。不幸的是，Snowtooth API 没有定义这些类型。然而，SWAPI 确实有一个名为“Node”的接口，它允许您在那里测试这个查询。

这个有用的查询将为您指出实现接口类型或与联合类型相关联的所有对象。如果您在 SWAPI 中查询“节点”接口，您将看到“节点”接口的`kind`、`name`和`description`，以及在`possibleTypes`字段下由该接口实现的对象列表。在 SWAPI 中，这包括所有包含`id`的主要对象类型。

* * *

当我们想更好地理解一个模式时，自省查询很有用。我们可以在代码中使用这些查询来围绕 GraphQL APIs 本身构建应用程序。一个很好的例子就是 GraphQL 或 GraphQL Playground。这两个工具都提供了一个使用自省查询的浏览器，允许您浏览模式。当你添加一个类型系统到你的 API 中时，有无限的可能性。