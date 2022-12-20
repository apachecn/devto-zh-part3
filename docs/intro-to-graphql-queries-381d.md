# GraphQL 查询简介

> 原文：<https://dev.to/magnificode/intro-to-graphql-queries-381d>

最近我一直试图更好地掌握 GraphQL。我接触 GraphQL 的大部分时间都是以 React 应用的形式。这些应用程序往往都是在后端设置好并包装好的。这样，我就可以专注于在前端使用这些数据。

为了更多地了解 GraphQL 后端是如何设置的，我一直在参加 Wes Bos 课程[full stack Advanced
React&graph QL](https://advancedreact.com/)。

我想在这门课中集中讨论一个更狭窄的话题。GraphQL 后端的设置，以及如何从前端交互和更改数据。

我不会去检查这些技术的设置，因为这是一个完全独立的职位。相反，我将关注如何在一个工作应用中编写和使用查询。

作为一名前端工程师，我没有太多机会接触设置 GraphQL 后端的工作。让我们开始建立我们的数据结构。

## 使用模式

* * *

首先，假设我们想用一组`people`填充数据库。每个人都有一个`id`、一个`name`和一个`age`作为开始。

GraphQL 是框架不可知的。我们下面所解释的也将转化为任何技术的组合。我们将使用所谓的“GraphQL 模式语言”来解释下面的例子。

为了通知 GraphQL 我们希望我们的数据如何结构化，我们必须使用**对象类型**构建模式。这个初始模式是这样创建的:

```
type  Person  {  id:  ID!  name:  String!  age:  Int  } 
```

Enter fullscreen mode Exit fullscreen mode

这里我们告诉 GraphQL 以下内容:我们的数据库将由许多类型为`Person`的对象组成。数据库中的每个`Person`都有一个`id`字段，该字段需要一个`ID` ( *注意:这里的 ID 值是 GraphQL 知道的唯一标识符*)。我们还通过使用`!`通知 GraphQL 这个字段是必需的。

接下来，我们解释说每个人都有一个期望必填的`String`的`name`字段。

最后，`age`字段可以具有与之相关联的`Int`(整数)值，但是该字段不是必需的。

这里有额外的标识符和术语可以扩展，但这让我们对如何使用 GraphQL 模式语言有了基本的了解。关于模式的更多深入信息可以在 [GraphQL 文档](https://graphql.org/learn/schema/)中找到。但是现在，让我们继续。

## 查询数据

* * *

在低层次上，有两种与 GraphQL API 交互的基本方法。**查询**和**突变**。

**查询**就是这样。一种使用 GraphQL API 查询数据库的方法。查询本身很简单，会返回一组字段供您在前端进行交互。例如，如果我们想在数据库中查询一个特定的人，我们可以通过编写一个简单的查询来完成，并向该查询传递一个参数。

在此之前，我们需要告诉 GraphQL 我们的查询会得到什么。
在我们的模式中，除了定义多个 GraphQL 服务的`type`(到目前为止`Person`是我们定义的唯一服务)，我们还可以定义特定的查询，并构建如何处理这些查询。例如，如果我们要查询一个人，我们需要告诉 GraphQL 返回什么。

```
type  Query  {  person:  Person  } 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们告诉 GraphQL，当我们对一个人运行查询时，它应该返回一个 JSON 对象，这个对象包含我们的类型，`Person`。现在，当我们加载一个 GraphQL playground 并查询一个特定的人时，我们应该返回预期的类型。

GraphQL 的另一个强大特性是能够向查询添加参数。这允许我们在数据库中搜索特定的人，只要我们知道存储在数据库中的关于这个人的识别信息。以下面的查询为例:

```
query  {  person(id:  1)  {  name  }  } 
```

Enter fullscreen mode Exit fullscreen mode

我们向 GraphQL 查询一个人，他的`id`是`1`。然后，我们告诉 GraphQL，我们只想返回那个人的`name`，其他什么都不要。这是 GraphQL 的另一个吸引人之处。无论数据库中存在多少信息，我们都可以很清楚地知道返回给我们的数据。这创建了一些超级高效的查询，只返回我们需要的信息。

我们上面的查询将返回以下内容:

```
{  "data":  {  "person":  {  "name":  "Alan Watts"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

所有返回的数据都包装在`data`对象中。对于任何 GraphQL 查询都是如此。最棒的是，我们可以使用参数来查询我们构建的`Person`类型的任何一个字段！

### 查询所有的一种类型

如果我们知道我们要查询的人的具体信息，我们上面所做的就非常有用。只要我们不介意返回一个单数`Person`，那么我们就处于良好的状态，但是如果我们想要查询数据库中所有的`People`呢？

类似于我们如何定义单一的`person`查询，我们也需要定义一个`people`查询。将会这样执行:

```
type  Query  {  person:  Person  people:  [Person]!  } 
```

Enter fullscreen mode Exit fullscreen mode

除了我们的`person`查询之外，我们还添加了一个`people`查询，我们期望它返回多个`People`类型的数组！我添加了一个`!`向 GraphQL 表示，如果我们查询多个人，它*必须*返回数组。它可以是空数组，但数组本身是必需的。

太棒了。现在，我们只需简单地编写:
就可以查询我们数据库中的所有人

```
query  {  people  {  id  name  }  } 
```

Enter fullscreen mode Exit fullscreen mode

哪个会返回:

```
"data":  {  "people":  [  {  "id":  1  "name":  "Alan Watts"  },  {  "id":  2  "name":  "Stanisław Szukalski"  },  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

这是在 GraphQL 中处理和构建查询的高层次视图，但至少应该是让您开始使用它的良好开端。GraphQL 非常强大，使用起来非常有趣。不过，在我看来，最大的难点是启动和运行 GraphQL，因为应用程序的后端部分需要连接到前端。另外，GraphQL *不是*打算用来做的事情之一是在数据被传递到数据库之前修改数据。单独使用 GraphQL 无法完成任何逻辑或数据修改。

在我在本文开头提到的 Wes Bos 课程中，他使用 React、Apollo、GraphQL 和 GraphQL Yoga 设置了他的应用程序。Apollo 是应用程序前端和后端之间的连接，允许您在应用程序前端运行查询，并返回您可以使用的数据。GraphQL Yoga 允许您在数据被传递到后端的 GraphQL 之前对其进行逻辑处理。所有这些技术一起工作很好，但是需要一点设置。

为了我自己的理智(如果有用的话，也可能是为了你的理智)，我已经建立了一个[非常简单的应用程序框架](https://github.com/magnificode/react-apollo-prisma-yoga-boilerplate),它包含了这些技术，以及更多的技术，并且已经准备好了。这是 Wes Bos 在他的课程中所经历的设置步骤的结果，我强烈建议购买并深入研究，因为他在解释这些技术及其具体优势方面做得非常出色。当然，了解如何从头开始设置应用程序总是非常有益的。