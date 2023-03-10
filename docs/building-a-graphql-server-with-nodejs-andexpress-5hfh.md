# 用 Node.js 和 Express 构建 GraphQL 服务器

> 原文：<https://dev.to/azure/building-a-graphql-server-with-nodejs-andexpress-5hfh>

* * *

# 用 Node.js 和 Express 构建 GraphQL 服务器

[![](img/4631bed853e31b6a847729be2e688da7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yYE5urCW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Aa4sGHbJDDttlJI3xWg-28w.jpeg)

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

本文是 GraphQL 系列文章的一部分:

*   使用 Node.js 和 Express 构建 GraphQL 服务器，**我们到了**
*   [使用 Node.js 和 Apollo 框架构建 GraphQL 服务器](https://dev.to/azure/creating-a-graphql-server-withapollo-jjj)
*   [使用 React 使用 Apollo GraphQL 服务器](https://dev.to/softchris/consuming-an-apollo-graphql-server-usingreact-3kk4)

> GraphQL 使我们能够构建一个客户机可以以任何方式查询的服务器。我说的当然是内容协商，即在任何深度上准确地向服务器请求他们想要的字段或资源的能力。

在上一篇文章[构建您的第一个 GraphQL 服务器](https://medium.com/@noringc/building-your-first-graphql-server-d5c4f88f5e82)中，我介绍了构成 GraphQL 服务器的不同工件和类型。在本文中，我们将更多地关注如何构建一个由客户端可以使用的 GraphQL 支持的服务。我们还将引入一个名为 GraphiQL 的操场环境，它为我们提供了一个可以测试查询的 UI。

在本文中，我们将涵盖:

*   **为什么选择 Graphql** ，Graphql 是一项很好的新技术，让我们试着解释一下为什么它是相关的，为什么它会使构建 API 变得有趣和简单
*   **构建模块**，让我们来看看构建 Graphql 服务器端所需的构建模块
*   我们将使用 Node.js、Express 和一个名为 express-graphql 的库来构建一个服务器
*   **查询**，我们将介绍查询服务器的不同方式，如普通查询、参数化查询以及如何通过突变改变数据

## 为什么图 QL

选择 GraphQL 而不是 REST 有很多原因:

*   **您需要的数据**，与所有使用内容协商的技术一样，您可以只查询您需要的数据，这意味着您可以准确地获得您需要的列，并使返回的答案最少。尤其是在当今移动优先和 3G/4G 连接的世界，将数据响应保持在最小规模是一件非常重要的事情。
*   **一个端点**，一旦你想从一个端点得到特定的数据，你就查询那个特定的端点。如果您需要的数据是需要从多个端点拼在一起的东西，该怎么办？在这一点上，您执行一系列调用或构建一个新的端点。无论您选择哪种方法，您都需要花时间管理和了解您的端点。GraphQL 在这里大放异彩，因为它只是一个端点。
*   **序列化**，当你调用一个 REST 的时候得到你得到的响应 JSON。然而，您可能需要对数据做一些额外的*处理*，比如重命名列，以更好地适应您的应用程序。使用 GraphQL，您可以在查询本身中指定这一点
*   **深入研究**，通常情况下，对 REST 来说，像命令这样的具体事情很容易。如果您想获取订单上的订单项目，甚至是客户购买的产品，该怎么办？最有可能的是，为了避免额外的往返，您必须进行几次调用或进行特定的报告查询。使用 GraphQL，您可以在图形中查询您需要的深度，并显示您需要的任何深度的数据。当然，以一种高效的方式完成这项工作是 GraphQL 面临的最大挑战之一，这并不总是一帆风顺的。GraphQL 不是银弹，但它让生活变得简单多了

## 积木

GraphQL 服务器由以下部分组成:

*   模式定义了我们的实体，也定义了我们可以查询或调用的变异
*   **解析器**，解析器函数与第三方 API 或我们的数据库对话，并最终将数据返回给我们的用户

## 安装依赖项

让我们从安装我们需要的依赖项开始。我们需要以下内容:

*   **express** ，创建我们的 web 服务器
*   **graphql** ，安装 graphql，这是我们的核心库，使我们能够利用 graphql
*   这个库使我们能够将 graphql 和 express 绑定在一起

## Express + graphql(仅限)

让我们从只安装`graphql`和`express`开始，来理解它给了我们什么:

```
npm install express graphql 
```

Enter fullscreen mode Exit fullscreen mode

此后，让我们创建一个`express` HTTP 服务器，就像这样:

```
// schema.mjs

import {
  graphql,
  GraphQLSchema,
  GraphQLObjectType,
  GraphQLString,
  GraphQLList
} from "graphql";
let humanType = new GraphQLObjectType({
  name: "Human",
  fields: () => ({
    id: { type: GraphQLString },
    description: { type: GraphQLString },
    name: { type: GraphQLString }
  })
});
import people from "./data/people";
let schema = new GraphQLSchema({
  query: new GraphQLObjectType({
  name: "RootQueryType",
  fields: {
    hello: {
      type: GraphQLString,
      resolve() {
        return "world";
      }
    },
    person: {
      type: humanType,
      resolve() {
        return people[0];
      }
    },
    people: {
      type: new GraphQLList(humanType),
      resolve() {
        return people;
      }
    }
  }
})
});

export { graphql };
export default schema; 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常简单的模式，它将`hello`、`person`和`people`声明为可查询的关键字，并且还创建了`humanType`作为自定义类型。

对文件结尾`.mjs`的简短注释。我们在这里所做的是利用对`ESM/EcmaScript`模块的实验支持。它们目前在 NodeJS 中的实现方式迫使我们使用`.mjs`作为文件结尾。

接下来是应用程序本身，这只是一个基本的快速应用程序，看起来像这样:

```
 // app.mjs
import express from "express";
const app = express();
const port = 3000;
import schema, { graphql } from "./schema";

app.get("/", (req, res) => {
  let query = `{ hello, person { name }, people { name, description } }`;
  graphql(schema, query).then(result => {
    res.json(result);
  });
});
app.listen(port, () => console.log(`Example app listening on port port!`)); 
```

Enter fullscreen mode Exit fullscreen mode

上面我们通过调用:
来声明默认路由

```
app.get("/", (req, res) => {
}); 
```

Enter fullscreen mode Exit fullscreen mode

然后我们通过调用参数`schema`和`query`来添加`graphql`部分，就像这样:

```
graphql(schema, query).then(result => {
  res.json(result);
}); 
```

Enter fullscreen mode Exit fullscreen mode

正如我们在上面看到的，调用`graphql`意味着我们得到一个承诺，在`then()`回调时，我们能够看到查询的结果。总之，我们可以看到`graphql`和`express`如何互动。

最后，要运行它，我们需要在我们的`package.json`文件中指定一个`start`命令，调用 ESM 模块的实验支持。它需要看起来像这样:

```
// excerpt from package.json
"start": "node — experimental-modules app.mjs" 
```

Enter fullscreen mode Exit fullscreen mode

### 添加 express-graphql

我们刚刚展示了如何使用`express`和`graphql`并创建一个 REST api，但是我们可以通过添加`express-graphql`来做得更好，所以让我们把它作为下一件事:

```
npm install express-graphql 
```

Enter fullscreen mode Exit fullscreen mode

让我们先为自己做点别的事情，也就是说，使用`buildSchema()`方法并以这种方式建立一个模式，就像这样:

```
var { buildSchema } = require("graphql");
var schema = buildSchema(`
  type Product {
    name: String,
    id: Int
  },
  type Query {
    hello: String,
    products: [Product]
  }
`); 
```

Enter fullscreen mode Exit fullscreen mode

上面我们可以看到，我们定义了自定义类型`Product`，我们还定义了查询为`hello`和`products`。

我们还需要一些解析器函数，我们将在下面定义:

```
const getProducts = () => {
  return Promise.resolve([{
    title: 'Movie'
  }]);
}  

var root = {
  hello: () => {
    return "Hello world!";
  },
  products: () => {
    return getProducts();
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以稍微清理一下我们的代码，这样我们启动服务器的代码看起来就像这样:

```
var graphqlHTTP = require("express-graphql");
app.use(
  '/graphql',
  graphqlHTTP({
    schema: schema,
    rootValue: root,
    graphiql: true
  })
); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，我们实际上不需要定义任何路由，而是把它留给 graphql。我们可以看到`graphqlHTTP()`是我们从`express-graphql`得到的一个函数

现在我们已经把所有的部分都准备好了。

### **图 ql**

当我们调用我们的`graphqlHTTP()`函数时，我们为它提供了一个配置对象，该对象设置了以下属性:

*   **模式**，我们的 GraphQL 模式
*   **根值**，我们的解析器函数
*   **graphiql** ，一个声明是否使用`graphiql`的布尔值，我们需要它，所以我们在这里传递`true`

下一步是通过导航到`http://localhost:4000/graphql`来尝试`graphiql`，瞧，这就是你应该看到的:

[![](img/c5367d8cea176d9a78434f7bc4119f32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pUXTolOS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AX8nQBrmfPu4nmbzPjUNGhA.png)

好极了，一个可视化界面，现在呢？

好了，现在您可以开始创建 Graphql 查询了。要知道要查询什么，请看一下您在模式中定义的内容。

我们希望能够查询`hello`和`products`，因为我们已经在模式中设置了它们。让我们开始吧:

[![](img/3a3aa4e56e2e4a3524e41e7e05509940.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fZOFM4Hn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AX_D6Hbp9JO71SHea0M03fQ.png)

好了，点击`play`图标，你应该会看到上面的内容。正如你所看到的，这是一个非常有用的工具来调试你的查询，但它也可以用来调试你的突变。

### 参数化查询

让我们尝试用 graphiql 中的参数编写一个查询:

[![](img/7aeb590c603d1f567972c6071175958a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KJy6AXkC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AeD5LVw96Aq9kmzaser0yMg.png)

上面我们可以看到如何使用关键字`query`来定义我们的查询。此后，我们给它一个名字`Query`，后跟一个括号。在括号内，我们有用`$`字符表示的输入参数。在这种情况下，我们称我们的参数为`id`，这意味着它的全名是`$id`。让我们看看我们得到了什么:

```
query Query($id: Int!) {
  // our actual query
} 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候编写实际的查询了，接下来让我们开始:

```
product(id: $id) {
  name
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们使用了查询构造中的`$id`，完整的结果如下所示:

```
query Query($id: Int!) {
  product(id: $id) {
    name
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:在屏幕的底部，我们有一个叫做`query variables`的区域，让我们定义想要用作输入的变量。因此，当我们将某个东西定义为`$name_of_variable`时，它会查看`query variables`部分

**召唤变异**

要调用突变，我们需要关键字`mutation`。接下来让我们创建突变调用:

```
mutation MyMutation {
  addProduct(name: "product", description: "description of a product") 
} 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:如果我们返回一个复杂的对象，我们需要选择我们想要的列，就像这样

```
mutation MyMutation {
  addProduct(name: "product", description: "description of a product"){ 
    col1, 
    col2 
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

为了构建一个 API，我们使用了 NPM 库`express`、`graphql`。然而，通过添加`express-graphql`,我们获得了一个名为`graphiql`的可视化环境，使我们能够提出查询和运行突变，这样我们就可以验证我们的 API 是否如预期的那样工作

如果你想看看现有的回购协议，请看这里[演示回购协议](https://github.com/softchris/graphql-graphiql)

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris