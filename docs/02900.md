# 使用 GraphQL 为前端供电

> 原文：<https://dev.to/dealeron/power-to-the-front-end-with-graphql-2210>

<figcaption>Photo of a sparkler held above a hand by [Mervyn Chan](https://unsplash.com/@mervynckw) on [Unsplash](https://unsplash.com)</figcaption>

我一直觉得开发中的很多东西都是逆向设计的。我们很容易陷入数据背后的东西，而忘记数据不是最终目标，为用户提供某种功能才是最终目标。我曾多次访问一个银行网站，看到一个看起来像是直接从后端的 SQL 表中提取出来的表，上面的信息没有一个符合我实际需要的。

当我第一次读到 Rest APIs 时，正是这种设计让我对它们相当犹豫。不要误解我的意思，开发良好的 Rest APIs 所带来的标准化和一致性是一件很棒的事情。然而，整个概念感觉就像是开发人员公开现有数据的一种方式，而不一定是用户想要的数据。

一个常见的问题经常出现在 Rest APIs 的实现中，甚至是类似 Rest 的 API:一个页面经常需要执行几个 API 调用来获得它所需要的一切，很多时候会导致检索到不必要的信息。假设您需要一个人的姓名，那么您还可以获得他们的出生日期、头发颜色、鞋码以及学生资源中的任何其他信息。你最终会*过度查询。*更进一步，为了获得嵌套信息，你经常需要进行后续的 API 调用。

防止这种情况的简单答案可能是远离照本宣科的 Rest APIs，开始开发用于特定目的的 API(前面提到的 Rest 式 API)。例如，假设您有一个 API，专门返回学生的姓名、他们就读的学校、他们父母的姓名以及他们最喜欢的口袋妖怪的名字。现在，您可能会有许多 API 引用同一个资源，并且总的来说需要维护更多的代码。我肯定有人喜欢每天对 API 结果模型进行细微的调整，并重写 SQL 查询以适应新模型的形状，但这根本不是我喜欢的。

所以在编写了这些 Rest 式的 API 几个月后，我开始想，“如果我们能让这些 Rest 式的 API 更加动态……指定需要返回的属性会怎么样”。毫不奇怪，我不是第一个有这种想法的人；事实证明，脸书在这方面远远领先于我。

[GraphQL](https://graphql.org/) 只是一种查询语言，不是数据库。就像“SQL”是一种查询语言，与实际的实现没有任何关系。您可以在。NET(我们目前利用 GraphQL.NET 的 T2)、Java、Javascript，任何你能想到的东西。 [Neo4J](https://neo4j.com/) 是一个图形数据库，除了它的 [Cypher](https://neo4j.com/developer/cypher/) API 之外，它甚至还附带了一个 GraphQL API。

您只需将一个查询示例发布到 GraphQL 端点:

```
{  student(id:9682)  {  name  dateOfBirth  school  {  name  address  }  parents  {  name  dateOfBirth  }  favoritePokemon  {  id  generation  name  }  }  } 
```

在某种程度上，开发人员定义了每种[图形类型](https://graphql.org/learn/schema/)(人物、学校、口袋妖怪)的形状，以及如何遍历它们(`person -parents-\> person[]`)，但是根据您的 GraphQL 应用程序的充实程度，他们很久以前就为一些完全不同的页面定义了这些图形类型，您不需要要求他们做任何事情来通过一个 API 调用获得这种形状的信息。

突然之间，前端开发人员可以专注于开发满足用户需求的应用程序，而不必担心如何存储/构建数据。查询 API 是围绕前端的需求构建的，而不是后端的设计。

就像 Rest APIs 一样，这些图形类型可以从任何数据源提取，而不仅仅是 SQL。我在内存源(例如应用程序配置)和第三方 API 调用(例如 Salesforce APIs)中实现了 SQL、弹性搜索、Blob 存储、Redis、Neo4J。你能想到的任何返回数据的东西都可以放在 GraphQL 抽象层的后面。如果你设计正确，前端永远不需要关心。你很快就会忘记“我们无法同时获得学生和口袋妖怪数据，学生在 SQL 中，口袋妖怪存储在 Tim 的 raspberry pi 上托管的 CSV 中”这样的担忧。

不过回到正题，**动力到前端**。

<figure>[![](img/d79e58b42b756da887c122a8dfbcab41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dFeHrCip--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yj7wgdbj1lc6jtiipv2p.jpeg) 

<figcaption>一个人在云前的山顶上举起拳头的照片[伊恩·斯陶费尔](https://unsplash.com/@ianstauffer)在 [Unsplash](https://unsplash.com)</figcaption>

</figure>

GraphQL 比乍看上去更加健壮。它提供了许多工具，使前端开发人员能够在他们需要的时候准确地得到他们想要的东西。

先关， [**参数化**](https://graphql.org/graphql-js/passing-arguments/) 。通过查询字符串传递参数相当令人沮丧。确保所有东西都是 URL 编码的，并在背面完全验证是相当复杂和耗时的。GraphQL 已经预装了所有这些东西，并且根据具体实现，它会自动验证您传入的任何变量。

```
query($studentId:Int!)  {  student(id:$studentId)  {  name  }  } 
```

你只需在查询中传递一组变量，比如:

```
{  studentId:  9682  } 
```

它将自动期待一个整数作为学生 id，因为它有感叹号，所以它甚至会在处理查询之前进行验证，以确保 student id 不为空。

其次， [**自省**](https://graphql.org/learn/introspection/) **。**任何遵循 GraphQL 标准的应用程序都将能够探索可用的图形类型、它们具有的属性、它们与其他图形类型的关系以及它们可能具有的任何描述/元数据。这主要用于像 [GraphiQL](https://electronjs.org/apps/graphiql) 这样的工具中的自动完成，这些工具用于构建/测试查询，但是我发现它也可以用于构建页面，这些页面可以基于节点上可用的属性来构建自己。

```
{  __type(name:"Pokemon")  {  name  fields  {  name  type  {  name  kind  }  }  }  } 
```

[**指令**](https://graphql.org/learn/queries/#directives) 让部分查询有条件。当您在页面上显示动态信息时，这通常很好。

```
query($caresAboutPokemon:Boolean!,  $studentId:Int!){  student(id:$studentId)  {  name  favoritePokemon  @include(if:  $caresAboutPokemon)  {  name  }  }  } 
```

您仍然需要传入`caresAboutPokemon`，但是它提供了对请求不同数据的大量控制，而不必进行笨拙的字符串构建。

GraphQL 自带的其他工具有很多[(通常)都是预先打包好的，不需要开发人员做任何额外的工作，我强烈推荐你去看看。](https://graphql.org/learn/queries/)

总而言之，GraphQL 已经被证明是一个非常强大的工具，它提供了大量的通用性，使得构建和修改网页变得非常简单。我真的希望看到这种让技术对前端开发者更加友好的趋势继续下去。

* * *