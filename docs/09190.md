# GraphQL 服务器入门

> 原文：<https://dev.to/restoreddev/graphql-server-primer-4ekd>

GraphQL 是目前 web 开发中最热门的话题之一。我和 Magento 一起做了很多工作，他们最近宣布了一个全新的 GraphQL API 用于前端开发。为了学习 GraphQL，[我使用 Next.js](https://andrewdavis.me/post/creating-a-magento-theme-with-nextjs-and-graphql/) 构建了一个实验性的 Magento 前端。在那个项目之后，我仍然不明白如何构建一个 GraphQL 服务器。因此，我决定使用 Node.js 构建一个示例 GraphQL 服务器。最初的 GraphQL 规范是用 JavaScript 编写的，因此在学习 GraphQL 时，它是一种很好的语言。如果你想查看整个项目，我创建了一个名为[的 GitHub 库。在这篇文章中，我想讨论一些关于 GraphQL 的概念，它们确实帮助我理解了它是如何工作的。](https://github.com/restoreddev/graphql-nodejs-example/)

## 一个 GraphQL 服务器只有一个端点

在 REST APIs 中，一个资源有多个 URL 是很常见的。您可能有几个端点来加载和创建数据，比如`/tasks`、`tasks/1`、`/tasks/create`。在 GraphQL 中，您的服务器只运行一个端点，通常在根`/`或`/graphql`处。当向 GraphQL 服务器提交查询时，可以在请求体中显式设置所需的资源，这样服务器就可以决定返回什么值。

## GraphQL 是关于类型的

在 GraphQL API 中，您使用类型语言定义您拥有的资源。GraphQL 支持[五种标量类型](https://graphql.org/learn/schema/#scalar-types)，可以用来组成更复杂的对象类型。这五种标量类型分别是:`Int`、`Float`、`String`、`Boolean`和`ID`。要创建资源，需要为它构建一个对象类型。我想模拟一个论坛，所以我创建了三个资源:`User`、`Thread`和`Comment`。在 GraphQL 类型中，这些资源看起来像这样:

```
type  User  {  id:  Int!  userName:  String!  firstName:  String  lastName:  String  }  type  Thread  {  id:  Int!  name:  String!  description:  String!  user:  User!  comments:  [Comment]  }  type  Comment  {  id:  Int!  description:  String!  user:  User!  } 
```

您可以看到，您可以使用关键字`type`后跟一个名称来创建一个对象类型。在花括号中，通过在属性名称后加上冒号和类型来定义对象的属性。属性后的感叹号`!`表示该值不能为空。

您也可以在其他自定义类型中使用自定义类型。`Thread`类型有一个 user 和 comments 属性，它引用了我创建的另外两个类型。类型名周围的方括号，如`[Comment]`表示该属性是一个数组。

写服务器的时候，把那些类型放在哪里？我将它们都放在一个名为`schema.graphql`的文件中，并使用 Apollo 服务器助手`gql`将该文件导入我的服务器。

## 请求由查询和变异类型处理

在 GraphQL 中，有两种类型的请求可以发送到 GraphQL 服务器:`query`和`mutation`。一个`query`用于检索数据，一个`mutation`用于对数据执行操作，比如创建或更新。在您的服务器模式中，您定义了一个查询对象类型和一个变异对象类型，就像这样:

```
type  Query  {  thread(id:  Int!):  Thread  threads:  [Thread]  }  type  Mutation  {  createThread(name:  String!,  description:  String!,  userId:  Int!):  Thread  createComment(userId:  Int!,  threadId:  Int!,  description:  String!):  Comment  } 
```

你可以在我的`Query`类型中看到，我定义了两种方法来获取一个线程。属性返回所有线程的数组，`thread(id: ID!)`返回单个线程。括号表示可以在查询中传递的参数。由于我将`id`标记为不可空的`Int`，要检索单个线程，您必须在 GraphQL 请求中传递一个线程的`id`。

在`Mutation`类型中，有两个属性用于创建线程和创建注释。每个操作都需要一组值来创建资源，并且每个操作都返回新创建的资源。

## 解析你的查询和突变

定义模式后，如何实现从数据源加载资源的逻辑呢？你用解析器！解析器类似于 REST API 中的控制器。对于每个`Query`和`Mutation`属性，您创建一个 JavaScript 函数，该函数接受参数并对资源执行操作以加载数据或更改数据。

我使用了 [Apollo Server](https://www.apollographql.com/docs/apollo-server/) 库来构建我的 GraphQL API。该库允许您编写模式、导入模式并传递一个解析器对象来处理所有请求。

我的阿波罗服务器设置如下:

```
const fs = require('fs');
const { ApolloServer, gql } = require('apollo-server');
const schema = fs.readFileSync(__dirname.concat('/schema.graphql'), 'utf8');
const typeDefs = gql(schema);
const resolvers = require('./resolvers');

const server = new ApolloServer({ typeDefs, resolvers });
server.listen().then(({ url }) => {
  console.log(`Server ready at ${url}`)
}); 
```

对于我的 Apollo 服务器实例，我只需要传入我的模式和解析器，它就会启动一个我可以查询的节点服务器。

我的解析器文件只导出了一个带有查询和变异属性的 JavaScript 对象，该对象包含对我的模式中定义的每个属性的函数的引用:

```
const threads = require('./threads');
const comments = require('./comments');

module.exports = {
  Query: {
    threads: threads.all,
    thread: threads.findOne,
  },
  Mutation: {
    createThread: threads.create,
    createComment: comments.create,
  }
}; 
```

`threads`和`comments`导入各自返回一个函数对象，这些函数可以传递给解析器对象。

那么解析器函数是什么样的呢？下面是一个查询解析器，它从数据库中返回所有的`Thread`类型:

```
exports.all = async function () {
  const threads = await db.Thread.query().eager('[comments.[user], user]');

  return threads;
}; 
```

该函数在数据库中查询解析`Thread`类型所需的数据，然后 Apollo Server 取出所需的值，并将其返回给请求所有线程的客户机。

很相似的一个突变:

```
exports.create = async function (parent, args) {
  const thread = await db.Thread.query().eager('user').insertAndFetch({
    userId,
    name,
    description,
  } = args);
  thread.comments = [];

  return thread;
}; 
```

解析函数接收的第二个参数是从请求中传递的所有参数。我使用这些参数在数据库中创建一个新线程，然后将数据返回给 Apollo 服务器，以便传递回客户机。

## 查询服务器

有许多方法可以测试 GraphQL API。我喜欢用[失眠](https://insomnia.rest/)。在开发模式下，Apollo Server 将返回您的模式，以便失眠症患者能够读取它，从而允许您自动完成对 API 的查询。

下面是一个示例查询，您可以用上面的模式发送到服务器:

```
query  getThreads  {  threads  {  id  name  description  user  {  id  firstName  lastName  userName  }  comments  {  id  description  user  {  id  userName  }  }  }  } 
```

在查询中，我请求查询对象的`threads`属性，并为每个线程传递我想要的属性。动态查询是 GraphQL 如此出色的原因，因为您可以要求 API 所能提供的或多或少的数据。下面的 json 表示 API 服务器返回给客户端的内容:

```
{  "data":  {  "threads":  [  {  "id":  1,  "name":  "Thread 1",  "description":  "This is the first thread",  "user":  {  "id":  1,  "firstName":  "Test",  "lastName":  "User 1",  "userName":  "testuser1"  },  "comments":  [  {  "id":  1,  "description":  "This is a comment on the first thread",  "user":  {  "id":  2,  "userName":  "testuser2"  }  },  {  "id":  3,  "description":  "Another comment",  "user":  {  "id":  1,  "userName":  "testuser1"  }  }  ]  },  {  "id":  2,  "name":  "Thread 2",  "description":  "This is the second thread",  "user":  {  "id":  2,  "firstName":  "Test",  "lastName":  "User 2",  "userName":  "testuser2"  },  "comments":  [  {  "id":  2,  "description":  "This is a comment on the second thread",  "user":  {  "id":  1,  "userName":  "testuser1"  }  }  ]  }  ]  }  } 
```

创建线程的变异查询如下:

```
mutation  createThread  {  createThread(  userId:  1,  name:  "A new thread",  description:  "This is a description"  )  {  id  name  description  user  {  id  firstName  lastName  userName  }  }  } 
```

我正在调用突变类型的`createThread`属性，并传入所需的参数。它返回给我它刚刚创建的资源:

```
{  "data":  {  "createThread":  {  "id":  7,  "name":  "A new thread",  "description":  "This is a description",  "user":  {  "id":  1,  "firstName":  "Test",  "lastName":  "User 1",  "userName":  "testuser1"  }  }  }  } 
```

## 一些通用的提示和窍门

以下是启动 GraphQL 服务器项目的一些通用技巧:

*   如果您使用数据库，请使用像 MongoDB 这样的 NoSQL 数据库或带有支持紧急加载的 ORM 的 SQL 数据库。GraphQL 类型通常使用嵌套对象，因此很难编写普通的 SQL 查询并为您的响应映射数据。我在 sqlite 中使用了 [Objection.js](https://vincit.github.io/objection.js/) ORM，这使得我的数据库代码更加简单。

*   GraphQL 自然会验证传递给 API 的任何参数的数据类型，但它只验证类型。默认情况下，字符串类型可以为空或任意长度。我使用 Objection.js 的验证特性来防止在突变中使用空字符串。

*   `ID`标量类型将 ID 值转换为字符串。这对于某些数据库来说非常有用，但是在我的例子中，我使用的是带有数字主键的 sqlite，所以我将 ID 值保留为一个`Int`。

## 结论

我对构建 GraphQL API 的速度感到惊讶，尤其是在 Apollo Server 和 Objection.js 等库的帮助下。我真的很喜欢能够围绕类型定义我的 API，这成为可用资源的自然文档。不必设置 URL 路由或类型验证也节省了大量时间。GraphQL 对于构建 API 客户端的好处已经被广泛吹捧，但是我认为对于服务器来说也有一些真正的优势。

我希望这篇文章能帮助您更好地理解 GraphQL 服务器。如果你对这篇文章有任何问题或想法，请发表评论！