# 一个基本的 Todo 应用程序的 ExpressJS，GraphQL 和 MongoDB 的后端部分

> 原文：<https://dev.to/danielpdev/backend-part-with-expressjs-graphql-and-mongodb-for-a-basic-todo-app-4go2>

## 这里是关于[小故障](http://glitch.com/edit/#!/join/e4cb8e25-6fda-4f77-8e2f-9ffaa9d9ccb7)的现场版本。(改之前请先混音)

# [前端部分](https://dev.to/danielpdev/frontend-part-with-react-and-apollographql-for-a-basic-todoapp-4b19)

# 目录

*   [目录](#table-of-contents)
    *   [什么是 graphql？](#what-is-graphql)
    *   [简介](#intro)
    *   [安装先决条件](#install-prerequisites)
    *   [GraphQL 类型](#graphql-types)
    *   [TodoType](#todotype)
    *   [RootQueryType](#rootquerytype)
    *   [突变类型](#mutationtype)
    *   [涂胶代码](#glueing-code)
    *   [启动 Express GraphQL 服务器](#starting-express-graphql-server)
    *   [测试查询和突变](#testing-queries-and-mutations)
    *   [结论](#conclusion)

## 什么是 graphql？

一种用于定义 API 的查询语言，它提供完整且可理解的数据描述，并支持强大的开发工具。
更多关于 [Graphql](https://graphql.org/learn/) 的信息。

## [T1】简介](#intro)

这是一个使用 ExpressJS 和 GraphQL 的基本 TodoApp 的后端部分。我们的后端将使用 express-graphql 结合 mongoose，对于服务器，我们将使用 ExpressJS。
在[小故障](https://apollo-graphql-todo.glitch.me/graphql)上访问现场版本。

## 安装先决条件

导航到您的项目目录并复制粘贴以下命令:

```
mkdir todo-express-graphql && cd todo-express-graphql npm install cors express express-graphql graphql mongoose 
```

## GraphQL 类型

```
cd todo-express-graphql && mkdir schema && cd schema && touch todo_type.js 
```

## TodoType

```
const mongoose = require('mongoose'); 
const graphql = require('graphql'); //package used to build our graphql schema
const {
  GraphQLObjectType,
  GraphQLID,
  GraphQLInt,
  GraphQLString
} = graphql; //necessary types for defining our schema

const TodoType = new GraphQLObjectType({
  name:  'TodoType',
  fields: () => ({
    id: { type: GraphQLID },
    likes: { type: GraphQLInt },
    content: { type: GraphQLString },
  })
}); 

module.exports = TodoType; 
```

当我们为我们的 GraphQL 模式定义一个类型时，我们需要创建一个`GraphQLObjectType`的实例，并传递一个包含我们的类型所需字段的对象。

`name`是`GraphQLObjectType`上唯一的必填字段。
我们将在本帖后面介绍的一些最常用的属性是`fields`，需要定义该类型解析的属性和`resolve`函数。
请参考关于 [GraphQLObjectType](https://graphql.org/graphql-js/type/#graphqlobjecttype) 的 graphql 官方文档

### RootQueryType

```
const mongoose = require('mongoose');
const graphql = require('graphql');
const { 
  GraphQLObjectType,
  GraphQLList,
  GraphQLID,
  GraphQLNonNull
} = graphql;
const Todo = mongoose.model('todo');
const TodoType = require('./todo_type');

const RootQueryType = new GraphQLObjectType({
  name: 'RootQueryType',
  fields: () => ({
    todos: {
      type: new GraphQLList(TodoType),
      resolve() {
        return Todo.find({});
      }
    },
    todo: {
      type: TodoType,
      args: { id: { type: new GraphQLNonNull(GraphQLID) } },
      resolve(parentValue, { id }) {
        return Todo.findById(id);
      }
    }
  })
});

module.exports = RootQueryType; 
```

`RootQueryType`拥有消费我们的 Todo 资源所需的所有根端点。这里我们使用`GraphQLList(TodoType)`将`todos`端点定义为一个包含一系列`TodoType`文档的响应。接下来是我们的`todo`端点，用于从数据库中检索
单个 todo。使用

`GraphQLNonNull`是因为我们需要确保作为查询参数接收的`id`不是未定义的。

`resolve(parentValue, { id })`resolve 函数接收的第一个参数是 parentValue 或 root，它是从其他类型传递下来的值。该参数启用了 GraphQL 查询的嵌套性质。
第二个参数是带有实际查询参数的对象。

express-graph QL 的好处在于它总是期望从解析器函数返回一个承诺，使用 mongoose 将会非常顺利地集成
。
有关 ApolloGraphQL 文档中[解析器](https://www.apollographql.com/docs/graphql-tools/resolvers.html#Resolver-function-signature)的更多信息。

### 突变型

突变通常用于改变我们数据库中的数据，您可以看到它们与我们的`RootQueryType`非常相似，只是现在我们基于查询参数来改变数据。

```
 const graphql = require('graphql');
const { GraphQLObjectType, GraphQLString, GraphQLID } = graphql;
const mongoose = require('mongoose');
const Todo = mongoose.model('todo');
const TodoType = require('./todo_type');

const mutation = new GraphQLObjectType({
  name: 'MutationType',
  fields: {
    addTodo: {
      type: TodoType,
      args: {
        content: { type: GraphQLString }
      },
      resolve(parentValue, { content }) {
        return (new Todo({ content })).save()
      }
    },
    likeTodo: {
      type: TodoType,
      args: { id: { type: GraphQLID } },
      resolve(parentValue, { id }) {
        return Todo.like(id);
      }
    },
    deleteTodo: {
      type: TodoType,
      args: { id: { type: GraphQLID } },
      resolve(parentValue, { id }) {
        return Todo.remove({ _id: id });
      }
    },
    updateTodo: {
      type: TodoType,
      args: { id: { type: GraphQLID }, content: { type: GraphQLString }  },
      resolve(parentValue, { id, content }) {
        return Todo.update({ _id: id }, { content });
      }
    },
  }
});

module.exports = mutation; 
```

### 涂胶码

```
const graphql = require('graphql');
const { GraphQLSchema } = graphql;

const query = require('./root_query_type');
const mutation = require('./mutations');

module.exports = new GraphQLSchema({
  query,
  mutation
}); 
```

大多数情况下，当你写模式文件时，你必须传递一个带有两个键的对象:`query`和`mutation`。非常简单明了，只需导入所需的突变和查询
，并将其作为对象传递给`GraphQLSchema`。

更多关于 [GraphQLSchema](https://graphql.org/graphql-js/type/#graphqlschema)

## 启动 Express GraphQL 服务器

```
 const express = require('express');
const expressGraphQL = require('express-graphql');
const mongoose = require('mongoose');

const todoModel = require('./models/todo');
const bodyParser = require('body-parser');
const schema = require('./schema');
const cors = require('cors')
const app = express();
app.use(cors());

const MONGO_URI = 'your mLab link';
if (!MONGO_URI) {
  throw new Error('You must provide a MongoLab URI');
}

mongoose.Promise = global.Promise;
mongoose.connect(MONGO_URI);
mongoose.connection
    .once('open', () => console.log('Connected to MongoLab instance.'))
    .on('error', error => console.log('Error connecting to MongoLab:', error));  

app.use(bodyParser.json());
app.use('/graphql', expressGraphQL({
  schema, //pass the schema to our middleware 
  graphiql: true //enable graphiql interface so we can test our queries and mutations before starting to use it.
}));

app.get('/', (req, res) => {
  res.redirect('/graphql');
});

app.listen(4000, () => {
  console.log('Listening at 4000');
}); 
```

## 测试查询和突变

当你必须建立一个查询，而你不知道该如何写它时
，那么`graphiql`将帮助 https://apollo-graphql-todo.glitch.me/graphql。GraphQL 的强大功能之一是即时文档。在我们定义了将在我们的`GraphQLSchema`中使用的类型之后，我们已经准备好了文档。只要进入[https://apollo-graphql-todo.glitch.me/graphql](https://apollo-graphql-todo.glitch.me/graphql)，在右上角你就可以找到`Docs`。

## 在 graphiql 中编写查询:

```
query{
  todos{
    id
    likes
    content
  }
} 
```

这个查询将在我们的`RootQueryType`和`todos`
字段上运行，并将被解析为一个`TodoTypes`列表。`TodoType`包含
和`id`、`likes`、`content`作为属性，因为我们有一个列表，所以我们将得到一个类似如下的响应:

```
{
  "data": {
    "todos": [
      {
        "id": "5c5c21184c9edc006857c11b",
        "likes": 17,
        "content": ""
      },
      {
        "id": "5c5c26e84c9edc006857c124",
        "likes": 4,
        "content": "asd"
      },
      {
        "id": "5c5c29b296f75b0068f3b9db",
        "likes": 0,
        "content": "asdad"
      },
      {
        "id": "5c5c29c296f75b0068f3b9dc",
        "likes": 0,
        "content": "eq123"
      }
    ]
  }
} 
```

作为一个练习，试着修改添加、修改和删除一个待办事项。

## 结论

Express-graphql 是开发需要支持 graphql 的后端的一个很好的工具，现在我们已经看到它可以多么容易地与 MongoDB 集成。我们现在有了一个小例子来说明如何实现一些基本的查询和变异。

我希望你喜欢这篇文章。