# 了解 GraphQL 突变

> 原文：<https://dev.to/eveporcello/understanding-graphql-mutations-52nl>

*本文最初发布于[moonhighway.com](https://moonhighway.com/understanding-graphql-mutations)。*

没有查询语言 QL，就不能拼写 GraphQL。但是不要让术语`query`暗示 GraphQL 仅仅是关于获取数据的。GraphQL 远不止这些。

要用 GraphQL 改变数据，我们可以发送一个突变。可以把 GraphQL 变体想象成一个函数，它可以执行定制的创建、更新和/或删除操作，只需要一点技巧和灵活性。

在本文中，我们将仔细研究 GraphQL 的变异:如何在模式中设计它们，以及如何使用 GraphQL 查询语言执行它们。

突变应该代表应用程序中的动词。它们应该由用户能够使用您的服务器执行的事情组成。设计 GraphQL API 时，列出用户可以对应用程序执行的所有操作。这些很可能是你的突变。

`Mutation`是根对象类型，就像`Query`一样。突变有名字。它们可以有返回对象类型或标量的选择集。我们在模式中的`Mutation`类型内定义了 GraphQL API 上所有可用的变异:

```
type  Mutation  {  # mutations go here  } 
```

在模式中的`Mutation`类型中，我们通过名称给出了变异，并定义了应该从变异中返回什么:

```
type  Mutation  {  deleteAllSongs:  Boolean!  } 
```

`deleteAllSongs`是突变的名称。它将返回一个布尔值来描述变异是否成功。我们想要做的动词是删除数据集中的所有歌曲。这种变异是个坏消息。

为了运行这个变异，我们将使用 GraphQL 查询语言发送以下变异:

```
mutation  Chaos  {  deleteAllSongs  } 
```

我们应该会收到以下响应:

```
{  "data":  {  "deleteAllSongs":  true  }  } 
```

有了那个突变，我们所有的歌都没了。虽然我们可能对烧毁我们所有的数据感觉不太好，但我们应该在这样一个事实中找到安慰，即我们现在知道如何向 GraphQL API 发送一个变异，一个返回布尔值的变异。

## 向一个突变发送自变量

让我们考虑另一种突变，但不是摧毁什么，而是创造什么。我们将从模式开始:

```
type  Mutation  {  addSong(  title:  String!  numberOne:  Boolean  performerName:  String!  ):  Song!  } 
```

变异的名字是`addSong`，有三个参数:一个不可为空的字符串代表`title`，一个可为空的布尔值代表歌曲是否是`numberOne`的主打歌，一个不可为空的字符串代表`performerName`。我们可以假设突变将这首新歌添加到数据库中。注意，我们从这个突变中返回了`Song`类型。`Song`在模式中定义如下:

```
type  Song  {  id:  ID!  title:  String!  numberOne:  Boolean  performerName:  String!  } 
```

这意味着当我们发送突变时，`Song`对象将被返回，从而使我们能够访问`Song`上的所有字段。

```
mutation  CreateSong  {  addSong(  title:  "Electric Avenue"  numberOne:  false  performerName:  "Eddy Grant"  )  {  title  numberOne  performerName  }  } 
```

以上可以用来创作新歌。因为这个突变返回`Song`并且不可空，所以我们需要在突变后添加一个选择集。换句话说，参数列表后面是另一个字段列表周围的一组花括号。这里我们为刚刚创建的歌曲选择了`title`和`numberOne`字段。

```
{  "data":  {  "title":  "Electric Avenue",  "numberOne":  false,  "performerName":  "Eddy Grant"  }  } 
```

## 将自变量作为变量发送

到目前为止，我们已经在查询文本中直接发送了变异参数。以这种方式从应用程序中收集数据可能很困难。或者，您可以使用输入变量。变量代替了查询中的静态值，因此我们可以传递动态值。

让我们考虑一下我们的 addSong 突变。我们不处理字符串，而是使用 GraphQL 中的变量名，变量名前面总是有一个`$`字符:

```
mutation  createSong($title:  String!,  $numberOne:  Boolean,  $by:  String!)  {  addSong(title:  $title,  numberOne:  $numberOne,  performerName:  $by)  {  title  numberOne  performerName  }  } 
```

静态值由一个`$variable`代替。然后，我们声明`$variable`可以被变异接受。从那里，我们将每个`$variable`名称与参数名称进行映射。在 GraphQL 或 GraphQL Playground 中，左下角有一个查询变量的窗口。这是我们将输入数据作为 JSON 对象发送的地方。确保使用正确的变量名作为 JSON 键:

```
{  "title":  "No Scrubs",  "numberOne":  true,  "by":  "TLC"  } 
```

变量在发送参数数据时非常有用。这不仅将使我们的变异在 GraphQL 操场测试中更有组织，而且允许动态输入将在以后连接客户端接口时非常有帮助。

## 从变异中返回自定义对象

到目前为止，我们已经从一个变异中返回了一个`Boolean`和一个`Song`对象。可能会有这样的情况，由于一个突变，您想要访问更多的字段。或许是时间戳？还是一些关于突变是否成功的数据？您可以构造一个自定义的响应对象类型来传递这些字段。我们将从返回模式中的`AddSongResponse`对象开始:

```
type  Mutation  {  addSong(  title:  String!  numberOne:  Boolean  performerName:  String!  ):  AddSongResponse!  } 
```

然后我们将创建`AddSongResponse`对象:

```
type  AddSongResponse  {  song:  Song!  success:  Boolean!  timestamp:  String!  } 
```

通过创建这种类型，我们可以封装歌曲和一些关于变异执行的元数据字段，并从变异中返回它们。通过这种增强，查询有所改变:

```
mutation  createSong($title:  String!,  $numberOne:  Boolean,  $by:  String!)  {  addSong(title:  $title,  numberOne:  $numberOne,  performerName:  $by)  {  song  {  title  numberOne  }  success  timestamp  }  } 
```

`Song`对象字段现在嵌套在`song`字段下。`song`、`success`、`timestamp`现在在同一级别。创建这些自定义的返回对象可以让我们更深入地了解变化，而不仅仅是返回一个简单的对象类型。

突变从模式开始，规划出哪些突变是一个重要的过程。记住，这些变异是灵活的，可以返回任何东西:标量值，如布尔值或字符串，核心类型，如`Song`，或者定制的变异响应对象。

*关于如何设置支持突变的 GraphQL 服务器的更多信息，请查看 egghead.io 上的播放列表:[使用 GraphQL 和 Apollo](https://egghead.io/playlists/create-fullstack-applications-with-graphql-and-apollo-794dc9c7) 创建 Fullstack 应用程序 T3。*