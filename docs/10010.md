# 在无服务器的世界中做出反应

> 原文：<https://dev.to/basal/react-in-a-serverless-world-2m3d>

### [“客户端-服务器与无服务器全栈 app 架构对比”](#top)

> 本文的目标读者主要是前端开发人员。本文假设读者熟悉 [React](https://reactjs.org/) 和一些基本的 [GraphQL](https://graphql.org/) 。此外，本文中的客户端应用程序使用了最近推出的 React [钩子](https://reactjs.org/docs/hooks-intro.html)。后端技术将被详细解释，因此没有必要详细理解诸如 [Apollo 服务器](https://www.apollographql.com/docs/apollo-server/)、 [MongoDB](https://www.mongodb.com/) 、[mongose](https://mongoosejs.com/)、部署、 [AWS](https://aws.amazon.com/) 、 [Amplify](https://aws-amplify.github.io/) 和 [AppSync](https://aws.amazon.com/appsync/) 等主题。完整的回购可与应用程序的例子。

本文旨在说明构建全栈应用程序的客户机-服务器和无服务器方法之间的区别，React 用于 UI。作为起点，我们将在 React 中构建一个简单的客户端应用程序，用于客户端-服务器(从现在开始将称为“serverful ”,从加州大学伯克利分校的这份有启发性的技术报告中获得启发)和无服务器方法。现在， [TodoMVC 项目](http://todomvc.com/)是一个众所周知的项目，它使用一个 Todo 应用程序来比较 web 框架，所以我们将使用一个基于它的 Todo 应用程序。具体来说，我们将从 Redux 存储库中选择 [Todo MVC 应用程序](https://github.com/reduxjs/redux/tree/master/examples/todomvc)，使用它们的 CSS 样式，删除一些功能以简化本文，并使用 React 钩子将基于类的组件切换为功能组件，以进一步简化代码。

* * *

## [起点:客户端 App](#starting-point-client-side-app)

你可以在下面的 [CodeSandBox](https://codesandbox.io/s/3r6jjoljpp) 中看到这样做的客户端应用程序:

这个 Todo 应用程序是一个 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 应用程序，用户可以:

*   通过在文本输入区输入待办事项并按回车键来添加待办事项
*   通过双击列表中的待办事项来更新待办事项
*   将鼠标悬停在待办事项上并点击`×`图标，即可删除待办事项
*   点击待办事项左侧的圆形图标，将待办事项标记为`completed`

出于这个简单示例应用程序的目的，一些示例 todo 被添加到一个`data`文件夹中，作为 todo 对象的数组。应用程序状态数据保存在内存中，不会在浏览器刷新过程中保持不变。

现在我们有了一个简单的客户端应用程序起点，基本上有两种不同的方法来为用户添加数据持久性:

*   **serverful(客户端-服务器)架构**，开发者选择并部署后端服务器和持久数据源(数据库)
*   **无服务器架构**，后端服务器和数据源实施外包给 [BaaS](https://en.wikipedia.org/wiki/Mobile_backend_as_a_service) 云提供商

我们将创建两个基于纯客户端应用的全栈解决方案——每种架构方法一个——详细解释每种方法的流程，以便我们可以评估每种方法的利弊。此外，为了简单起见，我们选择只对客户机和服务器使用用 JavaScript 编写的包。

* * *

## [方法一:服务器架构](#approach-a-serverful-architecture)

> 在 Github repos 中可以找到这个例子中服务器[和客户端](https://github.com/bowdentom/todo-server-app)[的完整 repos。](https://github.com/bowdentom/todo-client-app)

在我们的示例中，我们将使用 GraphQL API，尽管简单的 REST API 对于简单的 Todo 应用程序来说已经足够了。其原因是，当额外的特性被增量地添加到项目中时，GraphQL 比 REST 有许多优势，因此它是一个“有抱负的”选择。

对于我们的后端，我们将使用 [Apollo Server (v2)](https://www.apollographql.com/docs/apollo-server/) ，这是一个社区维护的开源 GraphQL 服务器，它可以与许多 [Node.js](https://nodejs.org/en/) HTTP 服务器框架一起工作。我们将把它作为一个[独立](https://github.com/apollographql/apollo-server#installation-standalone)包来使用，而不把它与另一个 web 框架集成在一起。我们稍后会将服务器部署到 Heroku。

对于我们的持久数据源，我们将使用 [MongoDB](https://www.mongodb.com/) ，一个非 SQL 数据库。为了简单起见，我们将使用 Mongoose，它是 MongoDB 的 ORM，而不是使用 MondoDB 的原生 NodeJS 驱动程序。我们将把 MongoDB 数据库部署到 [MongoDB Atlas](https://cloud.mongodb.com) 中。

为了在前端处理 GraphQL 请求，我们将使用 [Apollo 客户端](https://www.apollographql.com/docs/react/essentials/get-started.html)。由于我们使用 React 钩子，我们将*而不是*使用`react-apollo` React 组件，这些组件使用*渲染道具模式*，原因我们将在本文[的](https://www.exodevhub.com/react-hooks-making-it-easier-to-compose-reuse-and-share-react-code)中解释。我们也将把客户端应用程序部署到 Heroku，作为服务器应用程序的一个独立应用程序。

我们先来看看后端代码。

### [第一步(服务器):创建 GraphQL 服务器并连接数据库](#step-1-create-graphql-server)

server 应用程序的基本级别 [`index.js`](https://github.com/bowdentom/todo-server-app/blob/master/src/index.js) 文件执行以下操作:

1.  设置 MongoDB 数据库连接(通过 Mongoose)
2.  获取已分析的 GraphQL 架构类型定义和解析程序
3.  创建并运行 Apollo GraphQL 服务器

```
const { ApolloServer, gql } = require('apollo-server')
const fs = require('fs')
const mongoose = require('mongoose')

// Database connection
mongoose.connect(process.env.MONGODB_URL, { useNewUrlParser: true })
const database = mongoose.connection
database.on('error', console.error.bind(console, 'connection error:'))
database.once('open', () => console.log('We are connected to MongoDB'))

// GraphQL schema types and resolvers
const typeDefs = gql(
  fs.readFileSync(`${__dirname}/graphql/schema.graphql`, { encoding: 'utf-8' })
)
const resolvers = require('./graphql/resolvers')

// Apollo GraphQL server
const server = new ApolloServer({ typeDefs, resolvers })
server
  .listen({ port: process.env.PORT || 4000 })
  .then(({ url }) => console.log(`GraphQL server ready on ${url}`)) 
```

### [第二步(服务器):创建 GraphQL 模式](#step-2-create-graphql-schema)

GraphQL 模式描述了连接到它的客户机可用的功能。GraphQL 模式的核心构建块是类型定义。类型提供了广泛的功能:

1.  定义数据的形状和使用的数据类型
2.  显示不同类型之间的关系
3.  定义客户端可以执行哪些数据获取(查询)和数据操作(变异)操作
4.  为客户端用户提供文档(通过[自检](https://graphql.org/learn/introspection/))

模式中有两种类型是特殊的:`Query`和`Mutation`类型。这些是特殊的，因为它们定义了每个 GraphQL 查询的入口点。GraphQL 模式描述了如何从模式入口点开始，通过将数据从一个点(或“节点”)遍历到另一个点，从数据图中的每个点获取数据。我们的模式有一个`Query`入口点:`listTodos`，一个产生数据源中所有 todos 的数组的操作。有四个`Mutation`字段是入口点——`createTodo`、`deleteTodo`、`updateTodo`和`toggleCompletion`——它们描述了客户端可以进行的所有数据操作。

输入类型描述了当客户机想要改变数据时所需的形状和数据类型。在我们的模式中，我们定义了三种输入类型:`CreateTodoInput`、`UpdateTodoInput`和`DeleteTodoInput`。

标量类型是简单类型，如`String`、`Boolean`和`ID`。用户定义的类型由标量类型组成，比如我们的`Todo`类型，它指定了一个 todo 对象，需要一个`ID`类型的`id`、一些`String`类型的`text`和一个`Boolean`类型的`completed`标志。类型周围的方括号表示一个数组，所以`[Todo]`是一个 todos 的数组。类型后的感叹号`!`表示非空。

这里是我们的整个模式，在 [`src/graphql/schema.graphql`](https://github.com/bowdentom/todo-server-app/blob/master/src/graphql/schema.graphql) 文件中:

```
type Todo {
  id: ID!
  text: String!
  completed: Boolean!
}

input CreateTodoInput {
  id: ID
  text: String!
  completed: Boolean!
}

input UpdateTodoInput {
  id: ID!
  text: String
  completed: Boolean
}

input DeleteTodoInput {
  id: ID
}

type Query {
  listTodos: [Todo]
}

type Mutation {
  createTodo(input: CreateTodoInput!): Todo
  deleteTodo(input: DeleteTodoInput!): Todo
  updateTodo(input: UpdateTodoInput!): Todo
  toggleCompletion(input: UpdateTodoInput!): Todo
} 
```

### [第三步(服务器):创建 GraphQL 解析器](#step-3-create-graphql-resolvers)

让我们看看我们的解决方案，在 [`src/graphql/resolvers.js`](https://github.com/bowdentom/todo-server-app/blob/master/src/graphql/resolvers.js) 文件中，非常简单:

```
const db = require('../dataSource/db')

const Query = {
  listTodos: () => db.list(),
}

const Mutation = {
  createTodo: (_, args) => db.create(args.input),
  deleteTodo: (_, args) => db.remove(args.input),
  updateTodo: (_, args) => db.update(args.input),
  toggleCompletion: (_, args) => db.toggle(args.input),
}

module.exports = { Query, Mutation } 
```

您可以将 GraphQL 查询中的每个字段视为返回下一个类型的前一个类型的函数或方法。例如，如 GraqhQL 模式中所述，根`Mutation`类型中的`createTodo`字段是一个接受输入对象并返回`Todo`的函数。每种类型上的每个字段都由一个名为*解析器*的函数支持，这是我们明确指定的。当一个字段被执行时，相应的解析器被调用以产生下一个值。在我们的示例中，`createTodo`解析器通过调用我们的数据源交互模块`db`中的函数`create`(由 Mongoose ORM 管理，稍后我们将更详细地描述)返回一个类型为`Todo`的值(由我们的 GraphQL 模式指定)。

每个解析器函数接收四个参数(前一个参数`obj`，提供给字段的参数`args`，上下文信息`context`，以及特定于字段的信息`info`)。你可以在这里找到更多细节。我们通过变异解析器将一个`input`对象通过`args`函数参数传递给 Mongoose ORM，它将返回一个新的`Todo`类型的 todo 对象。

如果一个字段产生一个标量值，如字符串或数字，则执行完成。但是，如果一个字段产生一个对象值，那么查询将包含适用于该对象的另一个字段选择。这一直持续到达到标量值。GraphQL 查询总是以标量值结束。

在我们的例子中，返回的`Todo`是一个对象值，它只包含标量字段(`id`、`text`和`completed`)。解决这些问题是[琐碎的](https://graphql.org/learn/execution/#trivial-resolvers)，Apollo 服务器让我们省略了这么简单的解析器。它假设如果没有为字段提供解析器，那么应该读取并返回同名的属性。

### [第四步(服务器):与数据源交互(第一部分，Mongoose 模型)](#step-4-interact-with-data-source-part-1)

让我们从代码开始，在 [`src/dataSource/model.js`](https://github.com/bowdentom/todo-server-app/blob/master/src/dataSource/model.js) 文件中找到:

```
const mongoose = require('mongoose')

const todoSchema = new mongoose.Schema({
  id: {
    type: String,
    required: true,
  },
  text: {
    type: String,
    required: true,
    trim: true,
    minlength: 1,
  },
  completed: {
    type: Boolean,
    required: true,
    default: false,
  },
})

const Todo = mongoose.model('Todo', todoSchema)

module.exports = { Todo } 
```

MongoDB 数据库由`collections`组成，类似于 JavaScript 数组，保存我们的数据`documents`，类似于 JavaScript 对象。Mongoose 定义了[模式](https://mongoosejs.com/docs/guide.html)(不要与 GraphQL 模式混淆)，每个模式都映射到一个 MongoDB 集合，并定义该集合中文档的形状。

我们的 Mongoose 模式简单地描述了我们的 Todo 文档的形状，它有`id`、`text`和`completed`键，以及它们的类型和附加属性(比如`required`、`default`和`minLength`)。

为了使用 mongose 模式，我们将它转换成一个我们可以使用的 mongose[模型](https://mongoosejs.com/docs/models.html):`const Todo = mongoose.model('Todo', todoSchema)`。这些`Todo`模型的实例是文档，映射到 MongoDB 文档。

### [第五步(服务器):与数据源交互(第二部分，Mongoose CRUD 操作)](#step-5-interact-with-data-source-part-2)

在我们的 MongoDB 数据库中创建、读取、更新和删除文档的操作用 [`src/dataSource/db.js`](https://github.com/bowdentom/todo-server-app/blob/master/src/dataSource/db.js) :
编码

```
const mongoose = require('mongoose')

const { Todo } = require('./model')

const list = async () => {
  try {
    const todos = await Todo.find()
    if (!todos) return []
    return todos
  } catch (err) {
    console.log(`Error in list todos: ${err}`)
  }
}

const create = async input => {
  const newTodo = new Todo({
    id: input.id,
    text: input.text,
    completed: input.completed,
  })
  try {
    await newTodo.save()
    return newTodo
  } catch (err) {
    console.log(`Error in create todo with text ${input.text}: ${err}`)
  }
}

const remove = async input => {
  try {
    if (!input.id) return
    const removedTodo = await Todo.findOneAndDelete({ id: input.id })
    return removedTodo
  } catch (err) {
    console.log(`Error in remove todo with id ${input.id}: ${err}`)
  }
}

const update = async input => {
  try {
    const updatedTodo = await Todo.findOneAndUpdate(
      { id: input.id },
      { text: input.text, completed: input.completed },
      { new: true }
    )
    return updatedTodo
  } catch (err) {
    console.log(
      `Error in update todo with id ${input.id} and text ${input.text}: ${err}`
    )
  }
}

const toggle = async input => {
  try {
    // 1\. Find todo that needs to be toggled in database
    const toggleTodo = await Todo.findOne({ id: input.id })
    // 2\. Update the completed flag of that todo in the database
    const updatedTodo = await Todo.findOneAndUpdate(
      { id: toggleTodo.id },
      { text: toggleTodo.text, completed: !toggleTodo.completed },
      { new: true }
    )
    return updatedTodo
  } catch (err) {
    console.log(`Error in toggle with id ${input.id}: ${err}`)
  }
}

module.exports = { list, create, remove, update, toggle } 
```

所有的数据库操作都是异步的，所以我们使用 [`async/await`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) 语法来处理[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。

这个文件描述了五个异步函数:`list`、`create`、`remove`、`update`和`toggle`，它们使用了几个 Mongoose 的 CRUD 助手工具。让我们描述一下每个功能是如何工作的:

1.  `list`:为了获取集合中的所有文档，我们使用了 [`Model.find()`](https://mongoosejs.com/docs/api.html#model_Model.find) 方法。

2.  `create`:需要将一个输入对象传递给一个新创建的文档，然后用 [`Document.prototype.save()`](https://mongoosejs.com/docs/api.html#document_Document-save) 方法保存到数据库中。

3.  `remove`:从输入对象中取`id`字段，与 [`Model.findOneAndDelete()`](https://mongoosejs.com/docs/api.html#model_Model.findOneAndDelete) 方法一起使用。

4.  `update`:与 [`Model.findOneAndUpdate()`](https://mongoosejs.com/docs/api.html#model_Model.findOneAndUpdate) 法配合使用。该方法根据第一个参数中的条件查找匹配的文档，根据第二个参数更新文档，如果在可选的第三个参数中添加了`{new: true}`，则返回修改后的文档(而不是原始文档，这是默认行为)。

5.  `toggle`:也更新数据库中的文档。首先，我们需要找到要为其切换`completed`布尔值的 todo。为此，我们使用了 [`Model.findOne()`](https://mongoosejs.com/docs/api.html#model_Model.findOne) 的方法。然后我们更新它。

> 至此，我们已经完成了 serverful 应用程序服务器端的所有代码。你可以在 Github 上的 [`todo-server-app`](https://github.com/bowdentom/todo-server-app) 中找到该项目。

### [第六步(客户端):创建阿波罗客户端](#step-6-create-apollo-client)

现在我们已经有了 GraphQL 服务器应用程序，我们可以在 GraphQL 客户端应用程序上工作了。为了从客户机发出 GraphQL 请求，我们将使用 Apollo 客户机。开始使用 Apollo 客户端最简单的方法是使用 [Apollo Boost](https://github.com/apollographql/apollo-client/tree/master/packages/apollo-boost) 包。

让我们从之前在 [CodeSandBox](https://codesandbox.io/s/3r6jjoljpp) 中展示的客户端专用 React 应用程序开始。代码可以从我们准备的 Github repo 中克隆出来。

#### 准备:克隆`todo-clientonly` App

在您终端的命令行中:

```
git clone https://github.com/bowdentom/todo-clientonly.git todo-client-app
cd todo-client-app 
```

[`App`](https://github.com/bowdentom/todo-clientonly/blob/master/src/components/App.js) 组件是我们将进行大部分代码修改以适应我们的 GraphQL 客户端应用程序的地方。

```
import React, { useState } from 'react'
import { generate } from 'shortid'

import TodoItem from './TodoItem'
import TodoTextInput from './TodoTextInput'
import { initialTodos } from '../data'

const App = () => {
  const [todos, setTodos] = useState(initialTodos)

  const addTodo = todoText => {
    const addedTodo = {
      id: generate(),
      completed: false,
      text: todoText,
    }
    const updateTodos = [...todos, addedTodo]
    setTodos(updateTodos)
  }

  const removeTodo = todoId => {
    const updateTodos = todos.filter(todo => todo.id !== todoId)
    setTodos(updateTodos)
  }

  const editTodo = (todoId, todoText) => {
    const updateTodos = [...todos]
    const index = updateTodos.findIndex(todo => todo.id === todoId)
    const editedTodo = updateTodos[index]
    editedTodo.text = todoText
    setTodos(updateTodos)
  }

  const completeTodo = todoId => {
    const updateTodos = [...todos]
    const index = updateTodos.findIndex(todo => todo.id === todoId)
    const editedTodo = updateTodos[index]
    editedTodo.completed = !editedTodo.completed
    setTodos(updateTodos)
  }

  return (
    <div>
      <header className="header">
        <h1>todos</h1>
        <TodoTextInput
          newTodo
          onSave={text => {
            if (text.length !== 0) addTodo(text)
          }}
        />
      </header>
      <section className="main">
        <ul className="todo-list">
          {todos.map(todo => (
            <TodoItem
              key={todo.id}
              todo={todo}
              completeTodo={completeTodo}
              editTodo={editTodo}
              removeTodo={removeTodo}
            />
          ))}
        </ul>
      </section>
    </div>
  )
}

export default App 
```

我们现在将通过 GraphQL 请求查询来获取它们，而不是硬编码的`initialTodos`。我们将这个查询称为`ListTodos`。你会在 Github 上已经完成的 [`todo-client-app`](https://github.com/bowdentom/todo-client-app) 文件中找到 [`src/graphql/queries`](https://github.com/bowdentom/todo-client-app/blob/master/src/graphql/queries.js) :

```
import gql from 'graphql-tag'

export const LIST_TODOS = gql`
  query ListTodos {
    listTodos {
      id
      text
      completed
    }
  }
` 
```

`ListTodos`查询字符串被包装在一个`gql`模板标签中，以将其解析成 GraphQL [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) (来自 [`graphql-tag`](https://github.com/apollographql/graphql-tag) 包)。

现在，我们也需要突变的 AST。你可以在文件 [`src/graphql/mutations`](https://github.com/bowdentom/todo-client-app/blob/master/src/graphql/mutations.js) :
中找到它们

```
import gql from 'graphql-tag'

export const CREATE_TODO = gql`
  mutation CreateTodo($input: CreateTodoInput!) {
    createTodo(input: $input) {
      id
      text
      completed
    }
  }
`

export const DELETE_TODO = gql`
  mutation DeleteTodo($input: DeleteTodoInput!) {
    deleteTodo(input: $input) {
      id
      text
      completed
    }
  }
`

export const UPDATE_TODO = gql`
  mutation UpdateTodo($input: UpdateTodoInput!) {
    updateTodo(input: $input) {
      id
      text
      completed
    }
  }
` 
```

现在我们已经有了这些 GraphQL ASTs，我们可以返回到我们的`App`组件，设置 Apollo 客户端，添加一个`fetchTodos`函数以便在组件挂载时运行`ListTodos`查询，并在现有的`addTodo`、`removeTodo`、`editTodo`和`completeTodo` :
处理程序中实现`CreateTodo`、`DeleteTodo`和`UpdateTodo`突变

```
import React, { useState, useEffect } from 'react'
import { generate } from 'shortid'
import { ApolloClient, HttpLink, InMemoryCache } from 'apollo-boost'

import TodoItem from './TodoItem'
import TodoTextInput from './TodoTextInput'
import { LIST_TODOS } from '../graphql/queries'
import { CREATE_TODO, DELETE_TODO, UPDATE_TODO } from '../graphql/mutations'

const apiUrl =
  process.env.NODE_ENV === 'development'
    ? 'http://localhost:4000/'
    : process.env.REACT_APP_API_URL

const client = new ApolloClient({
  link: new HttpLink({ uri: apiUrl }),
  cache: new InMemoryCache(),
})

const App = () => {
  const [todos, setTodos] = useState([])

  const fetchTodos = async () => {
    // Get a fresh list of all todos from the server
    try {
      const response = await client.query({
        query: LIST_TODOS,
        fetchPolicy: 'no-cache',
      })
      const fetchedTodos = response.data.listTodos
      setTodos(fetchedTodos)
    } catch (err) {
      console.log(`Error in fetchTodos API call: ${err}`)
    }
  }

  // Fetch todos on mount
  useEffect(() => {
    fetchTodos()
  }, [])

  const addTodo = async todoText => {
    // Update UI immediately
    const addedTodo = {
      id: generate(),
      text: todoText,
      completed: false,
    }
    const updatedTodos = [...todos, addedTodo]
    setTodos(updatedTodos)
    // Send operation to the API
    try {
      const createTodoInput = {
        id: addedTodo.id,
        text: addedTodo.text,
        completed: addedTodo.completed,
      }
      await client.mutate({
        mutation: CREATE_TODO,
        variables: { input: createTodoInput },
      })
    } catch (err) {
      console.log(`Error in addTodo API call with text ${todoText}: ${err}`)
    }
  }

  const removeTodo = async todoId => {
    // Update UI immediately
    const updatedTodos = todos.filter(todo => todo.id !== todoId)
    setTodos(updatedTodos)
    // Send operation to the API
    try {
      const deleteTodoInput = {
        id: todoId,
      }
      await client.mutate({
        mutation: DELETE_TODO,
        variables: { input: deleteTodoInput },
      })
    } catch (err) {
      console.log(`Error in removeTodo API call with id ${todoId}: ${err}`)
    }
  }

  const editTodo = async (todoId, todoText) => {
    // Update UI immediately
    const updateTodos = [...todos]
    const index = updateTodos.findIndex(todo => todo.id === todoId)
    const editedTodo = updateTodos[index]
    editedTodo.text = todoText
    setTodos(updateTodos)
    // Send operation to the API
    try {
      const updateTodoInput = {
        id: todoId,
        text: todoText,
        completed: editedTodo.completed,
      }
      await client.mutate({
        mutation: UPDATE_TODO,
        variables: { input: updateTodoInput },
      })
    } catch (err) {
      console.log(
        `Error in editTodo API call with id ${todoId} and text ${todoText}: ${err}`
      )
    }
  }

  const completeTodo = async todoId => {
    // Update UI immediately
    const updateTodos = [...todos]
    const index = updateTodos.findIndex(todo => todo.id === todoId)
    const editedTodo = updateTodos[index]
    editedTodo.completed = !editedTodo.completed
    setTodos(updateTodos)
    // Send operation to the API
    try {
      const updateTodoInput = {
        id: todoId,
        text: editedTodo.text,
        completed: editedTodo.completed,
      }
      await client.mutate({
        mutation: UPDATE_TODO,
        variables: { input: updateTodoInput },
      })
    } catch (err) {
      console.log(`Error in completeTodo API call with id ${todoId}: ${err}`)
    }
  }

  return (
    <div>
      <header className="header">
        <h1>todos</h1>
        <TodoTextInput
          newTodo
          onSave={text => {
            if (text.length !== 0) addTodo(text)
          }}
        />
      </header>
      <section className="main">
        <ul className="todo-list">
          {todos.map(todo => (
            <TodoItem
              key={todo.id}
              todo={todo}
              completeTodo={completeTodo}
              editTodo={editTodo}
              removeTodo={removeTodo}
            />
          ))}
        </ul>
      </section>
    </div>
  )
}

export default App 
```

注意，我们遵循在处理程序中立即更新 UI，然后向服务器发出异步 GraphQL 请求的模式。

> 至此，我们已经完成了 serverful 应用程序客户端的所有代码。你可以在 Github 上的 [`todo-client-app`](https://github.com/bowdentom/todo-client-app) 中找到该项目。

我们现在可以继续部署数据库、服务器和客户机了。

### [第七步:部署数据库](#step-7-deploy-database)

我们将使用[云图](https://www.mongodb.com/cloud/atlas)作为我们的 MongoDB 数据库的云托管服务。我们将使用他们的[免费层](https://docs.mongodb.com/manual/tutorial/atlas-free-tier-setup/)。

*   *创建一个 Atlas 用户帐户*。点击`Start free`按钮，创建一个账户。
*   *创建新集群*。首先，选择一个云提供商&地区。比如`AWS N. Virginia (us-east-1)`。然后，选择集群层。选择`M0`。指定一个集群名称。例如:`todo-db`。然后点击底部的`Create Cluster`按钮。创建群集需要几分钟时间。
*   *添加数据库用户*。在`Security`选项卡中，点击`MongoDB Users`选项卡，然后点击`Add New User`按钮。将出现一个`Add New User`对话框。在`User Privileges`下，选择`Atlas admin`。为将要部署到 Atlas 的数据库添加用户名和密码。完成后，点击`Add User`按钮。
*   *将客户端与数据库的连接列入白名单*。在`Security`选项卡中，点击`IP Whitelist`选项卡，然后点击`Add IP Address`按钮。将出现一个`Add Whitelist Entry`对话框。点击`Add Current IP Address`按钮，然后点击`Confirm`，等待 Atlas 更新防火墙。
*   *获取 URI 连接字符串*。在集群的沙盒面板内的`Overview`选项卡中，单击`Connect`按钮。一个`Connect to <YOUR CLUSTER NAME>`对话框将会出现。选择`Connect Your Application`。然后选择`Short SRV connection string`。在`Copy the SRV address field`中，复制字符串。大概会是这样的:`mongodb+srv://<Username>:<PASSWORD>@<Cluster>.mongodb.net/todo-db?retryWrites=true`。在下一步中，我们将使用此 URI 连接字符串从 server 应用程序连接到集群。

### [第八步:部署服务器 app](#step-8-deploy-server)

我们将使用 [Heroku](https://www.heroku.com) 为[免费](https://www.heroku.com/free)部署我们的服务器。

*   *创建一个 Heroku 用户账号*。[在 Heroku 上注册一个免费用户账户](https://signup.heroku.com/login)。
*   下载并安装 Heroku CLI 。按照[这些说明](https://devcenter.heroku.com/articles/heroku-cli)下载并安装 Heroku CLI。

现在我们已经有了从命令行与 Heroku 交互的必要工具，我们将部署我们的 NodeJS 服务器。

*   *从命令行*登录 Heroku。从终端的命令行使用命令`heroku login`登录 Heroku。
*   *在 Heroku* 上创建服务器 app。在命令行中键入`heroku create <YOUR_SERVER_APP_NAME>`，其中`<YOUR_SERVER_APP_NAME>`是 Heroku 上您的服务器应用程序的唯一名称。
*   *为服务器 app* 设置配置。使用云图集群连接 URI，我们称之为上一步中的`<YOUR_CONNECTION_URI>`，键入`heroku config:set MONGODB_URL=<YOUR_CONNECTION_URI> --app <YOUR_SERVER_APP_NAME>`。
*   *部署到 Heroku* 。假设您正在从 Git `master`分支进行部署，使用这个命令推送到 Heroku，在那里它将被构建和部署:`git push heroku master`。

如果成功，您的服务器应用程序 API 将可在 URL: `https://<YOUR_SERVER_APP_NAME>.herokuapp.com/`上访问。在下一步中，您将需要此 URL。

### 第九步:[部署客户端 app](#step-9-deploy-client)

现在我们已经部署了数据库和服务器应用程序，我们可以部署我们的客户端应用程序，也是使用 Heroku。

*   *设置环境*。在客户端 app 的`.env`文件中，设置 Apollo 客户端将使用的服务器 API 端点:`REACT_APP_API_URL=https://<YOUR_SERVER_APP_NAME>.herokuapp.com/`。
*   *在 Heroku* 上创建客户端 app。在命令行中键入`heroku create <YOUR_CLIENT_APP_NAME>`，其中`<YOUR_CLIENT_APP_NAME>`是您的客户端应用程序在 Heroku 上的唯一名称。
*   *部署到 Heroku* 。假设您正在从 Git `master`分支进行部署，使用这个命令推送到 Heroku，在那里它将被构建和部署:`git push heroku master`。

如果成功，您的客户端 Todo 应用程序将可在 URL: `https://<YOUR_CLIENT_APP_NAME>.herokuapp.com/`上访问。您的应用程序现已准备就绪！

* * *

## [方法 B:无服务器架构](#approach-b-serverless-architecture)

因为我们现在讨论的是“无服务器”方法，所以我们不会制作服务器，甚至不必担心手动连接数据库。我们需要做的就是编写客户端代码，让 AWS 来完成剩下的繁重工作。

让我们从 [`todo-clientonly`](https://github.com/bowdentom/todo-clientonly) 应用程序开始，就像我们对方法 a 的客户端所做的一样

#### 准备:克隆`todo-clientonly` App

在您终端的命令行中:

```
git clone https://github.com/bowdentom/todo-clientonly.git todo-serverless
cd todo-serverless 
```

### [第一步:安装 AWS Amplify CLI](#serverless-step-1-install-amplify-cli)

在命令行中，在您的机器上进行全局安装:

```
npm install -g @aws-amplify/cli 
```

### [第二步:配置 Amplify CLI](#serverless-step-2-configure-amplify-cli)

```
amplify configure 
```

则需要以下配置步骤:

*   *登录*。这应该会在您的浏览器中打开 AWS 管理控制台。登录您的 AWS 帐户，然后返回到您的命令行。点击`Enter`继续。
*   *指定地区*。使用箭头键指定 AWS 区域。比如`us-east-1`。
*   *指定一个 IAM 用户*。指定新 IAM 用户的用户名。比如:`todo-serverless-cli-user`。
*   *添加 IAM 用户*。AWS 管理控制台中的`Add User` stepper 应该会在您的浏览器中打开。在您的浏览器中，IAM 用户有一些预先配置的设置，我们可以通过单击`Next: Permissions`、`Next: Tags`、`Next: Review`，最后单击`Create User`来接受这些设置。一旦创建了 IAM 用户，我们就会得到一个`Access key ID`和一个`Secret access key`。请确保将这些文件复制到安全的位置，因为我们接下来会用到它们。现在您可以返回到命令行了。点击`Enter`继续。
*   *输入用户访问密钥*。输入之前获得的新创建用户的访问密钥 id。
*   *输入用户秘密访问密钥*。输入之前获得的新创建用户的秘密访问密钥。
*   *指定一个配置文件名*。指定配置文件名。比如:`todo-serverless-cli-user-profile`。现在 CLI 已经配置好了，我们准备开始初始化新的 AWS Amplify 项目。

### [第三步:初始化放大项目](#serverless-step-3-initialize-amplify)

在命令行中，从您在准备过程中克隆的`todo-serverless` app 文件夹的根目录:

```
amplify init 
```

然后完成以下步骤，为您的项目配置 Amplify:

*   *指定项目名称*。输入项目的名称。例如，通过按下`Enter`选择默认名称`todo-serverless`。
*   *指定环境名*。输入环境的名称。比如:`dev`。
*   *选择默认编辑器*。使用箭头键选择您的默认编辑器。比如:`Visual Studio Code`。
*   *选择*App 的编程语言。选择您正在构建的应用程序的类型。选择:`javascript`。
*   *选择框架*。“你用的是什么 javascript 框架？”选择:`react`。
*   *指定源目录路径*。“源目录路径:”输入:`src`，应该是默认的。
*   *指定分发目录路径*。“分发目录路径:”输入:`build`，默认为。
*   *指定构建命令*。" Build 命令:" Enter: `npm run-script build`，应该是默认的。
*   *指定开始命令*。“开始命令:”回车:`npm run-script start`，应该是默认的。
*   *使用 AWS 配置文件*？"您想使用 AWS 配置文件吗？"输入:`Y`。
*   *选择 AWS 配置文件*。"请选择您想要使用的配置文件"。选择:`todo-serverless-cli-user-profile`。

CLI 将开始在云中初始化项目。这需要几秒钟的时间。

如果成功，您应该会看到:

> 您的项目已成功初始化并连接到云！

您可能已经注意到，Amplify CLI 在根级别向您的`todo-serverless`项目添加了一个文件夹`amplify`，并更新了您的`.gitignore`文件。

### [第四步:使用 Amplify 添加 API](#serverless-step-4-amplify-add-api)

现在我们可以开始为我们的应用程序创建 GraphQL API 了。AWS 提供了一个名为`AppSync`的托管 GraphQL 后端服务，我们将使用它。在命令行:

```
amplify add api 
```

然后完成以下步骤来添加 AppSync:

*   *选择 GraphQL API* 。“请从下列服务中选择一项”。选择:`GraphQL`。
*   *指定 API 名称*。提供一个 API 名称。比如:`TodoServerlessAppSyncApi`。
*   *选择 API 授权类型*。为 API 选择授权类型。选择:`API key`。对于我们的简单示例应用程序，我们不会添加授权和身份验证。如果我们想要登录用户，我们将选择`Amazon Cognito User Pool`选项。
*   *现有的 GraphQL 模式*？"你有带注释的 GraphQL 模式吗？"选择:`N`。
*   *引导模式创建*？"您想要引导式架构创建吗？"选择:`Y`。
*   *数据关系的复杂性*。“什么最能描述您的项目:”选择:`Single object with fields (e.g., “Todo” with ID, name, description)`。
*   *现在编辑模式*？"您想现在编辑架构吗？"选择:`Y`。
*   *编辑放大生成的模式*。您的代码编辑器应该在`todo-serverless/amplify/backend/api/TodoServerlessAppSyncApi/schema.graphql`打开 Amplify 生成的`schema.graphql`文件。

您应该会看到以下由 Amplify 生成的模式代码:

```
type Todo @model {
  id: ID!
  name: String!
  description: String
} 
```

请更新`Todo`类型，如下所示:

```
type Todo @model {
  id: ID!
  text: String!
  completed: Boolean!
} 
```

保存您的更改，并返回到命令行。

*   *继续 GraphQL 自动生成*。点击`Enter`继续。

如果一切顺利，您应该会看到以下消息:

> GraphQL 架构编译成功。
> 在`<...>/todo-serverless/amplify/backend/api/TodoServerlessAppSyncApi/schema.graphql`编辑您的模式或将`.graphql`文件放在`<...>/todo-serverless/amplify/backend/api/TodoServerlessAppSyncApi/schema`的目录中
> 本地成功添加资源`TodoServerlessAppSyncApi`。

Amplify 已经添加了本地文件夹`amplify/backend/api/`,其中包含您修改的模式。现在，您可以将这些更改(以及您的配置选择)提交给 AWS，以便在云中为您的后端创建资源。

### [第五步:推送 API 更新到 AWS](#serverless-step-5-amplify-push)

在命令行中，为了构建您所有的本地后端资源并在云中进行配置:

```
amplify push 
```

然后完成以下步骤将 API 推送到 AWS:

*   *继续*。“您确定要继续吗？”选择`Y`。
*   *为新的 GraphQL API 生成代码*？"您想为新创建的 GraphQL API 生成代码吗？"选择`Y`。
*   *选择代码生成语言目标*。"选择代码生成语言目标."比如:`javascript`。
*   *指定 GraphQL* 。"输入 graphql 查询、变异和订阅的文件名模式."输入`src/graphql/**/*.js`，这应该是默认的。
*   *生成所有 GraphQL 操作*？"你想生成/更新所有可能的 GraphQL 操作-查询，变异和订阅."选择`Y`。
*   *指定最大语句深度*。"输入最大语句深度[如果模式嵌套很深，则增加默认值]."输入`2`，这应该是默认的。

您现在应该看到以下消息:
“更新云中的资源。这可能需要几分钟……”

如果成功，您应该会看到以下消息:

> ✔成功生成 GraphQL 操作并在`src/graphql`
> 保存✔所有资源更新在云端

Amplify 已经将文件夹`src/graphql`添加到您的项目中。在里面，你会看到`queries`、`mutations`和`subscriptions`的文件夹，以及一个`schema.json`文件。

如果您查看`queries`和`mutations`文件夹，您会看到与我们在本文的`Approach A: Serverful Architecture`部分中手动编写的 GraphQL 查询相似的查询，当创建服务器端应用程序时。

### [第六步:在无服务器(客户端专用)App 中配置 Amplify](#serverless-step-6-configure-amplify-on-client)

现在我们已经在`AWS AppSync`中设置了“无服务器”资源，我们可以在客户端应用`todo-serverless`中添加 GraphQL 请求代码。

基础级`index.js`组件是我们将开始的地方。您可能已经注意到，在设置 AWS AppSync 时，Amplify 在前面的步骤中自动添加了一个配置文件`todo-serverless/src/aws-exports.js`。我们将在我们的客户端专用应用程序中配置 Amplify 时使用该文件。

更新`index.js`文件，使其看起来像这样:

```
import React from 'react'
import ReactDOM from 'react-dom'
import Amplify from 'aws-amplify'

import App from './components/App'
import 'todomvc-app-css/index.css'
import aws_exports from './aws-exports'

Amplify.configure(aws_exports)

ReactDOM.render(<App />, document.getElementById('root')) 
```

我们需要将`aws-amplify`安装到我们的项目中，这样我们就可以导入`Amplify`。在你的命令行中，请选择`yarn add aws-amplify`或`npm install aws-amplify`，将它添加到我们的项目中。

### [步骤 7:用 GraphQL API 请求更新无服务器(仅客户端)应用](#serverless-step-7-update-client-with-graphql-api)

让我们转到`src/components`文件夹中的`App`组件文件。更新这个文件，使它看起来像这样:

```
import React, { useState, useEffect } from 'react'
import { generate } from 'shortid'
import { API, graphqlOperation } from 'aws-amplify'

import TodoItem from './TodoItem'
import TodoTextInput from './TodoTextInput'
import { listTodos } from '../graphql/queries'
import { createTodo, deleteTodo, updateTodo } from '../graphql/mutations'

const App = () => {
  const [todos, setTodos] = useState([])

  const fetchTodos = async () => {
    try {
      const response = await API.graphql(graphqlOperation(listTodos))
      // Note: todos are under listTodos.items in this schema...
      const fetchedTodos = response.data.listTodos.items
      setTodos(fetchedTodos)
    } catch (err) {
      console.log('Error in fetchTodos:', err)
    }
  }

  // Fetch todos on mount
  useEffect(() => {
    fetchTodos()
  }, [])

  const addTodo = async todoText => {
    // Update UI immediately
    const addedTodo = {
      id: generate(),
      text: todoText,
      completed: false,
    }
    const updatedTodos = [...todos, addedTodo]
    setTodos(updatedTodos)
    // Send operation to the API
    try {
      const createTodoInput = {
        id: addedTodo.id,
        text: addedTodo.text,
        completed: addedTodo.completed,
      }
      await API.graphql(
        graphqlOperation(createTodo, { input: createTodoInput })
      )
    } catch (err) {
      console.log(`Error in addTodo API call with text ${todoText}: ${err}`)
    }
  }

  const removeTodo = async todoId => {
    // Update UI immediately
    const updatedTodos = todos.filter(todo => todo.id !== todoId)
    setTodos(updatedTodos)
    // Send operation to the API
    try {
      const deleteTodoInput = {
        id: todoId,
      }
      await API.graphql(
        graphqlOperation(deleteTodo, { input: deleteTodoInput })
      )
    } catch (err) {
      console.log(`Error in removeTodo API call with id ${todoId}: ${err}`)
    }
  }

  const editTodo = async (todoId, todoText) => {
    // Update UI immediately
    const updateTodos = [...todos]
    const index = updateTodos.findIndex(todo => todo.id === todoId)
    const editedTodo = updateTodos[index]
    editedTodo.text = todoText
    setTodos(updateTodos)
    // Send operation to the API
    try {
      const updateTodoInput = {
        id: todoId,
        text: todoText,
        completed: editedTodo.completed,
      }
      await API.graphql(
        graphqlOperation(updateTodo, { input: updateTodoInput })
      )
    } catch (err) {
      console.log(
        `Error in editTodo API call with id ${todoId} and text ${todoText}: ${err}`
      )
    }
  }

  const completeTodo = async todoId => {
    // Update UI immediately
    const updateTodos = [...todos]
    const index = updateTodos.findIndex(todo => todo.id === todoId)
    const editedTodo = updateTodos[index]
    editedTodo.completed = !editedTodo.completed
    setTodos(updateTodos)
    // Send operation to the API
    try {
      const updateTodoInput = {
        id: todoId,
        text: editedTodo.text,
        completed: editedTodo.completed,
      }
      await API.graphql(
        graphqlOperation(updateTodo, { input: updateTodoInput })
      )
    } catch (err) {
      console.log(`Error in completeTodo API call with id ${todoId}: ${err}`)
    }
  }

  return (
    <div>
      <header className="header">
        <h1>todos</h1>
        <TodoTextInput
          newTodo
          onSave={text => {
            if (text.length !== 0) addTodo(text)
          }}
        />
      </header>
      <section className="main">
        <ul className="todo-list">
          {todos.map(todo => (
            <TodoItem
              key={todo.id}
              todo={todo}
              completeTodo={completeTodo}
              editTodo={editTodo}
              removeTodo={removeTodo}
            />
          ))}
        </ul>
      </section>
    </div>
  )
}

export default App 
```

现在可以删除带有硬编码 todos 的`src/data`目录。

您会注意到 API 调用与我们在`todo-client-app`中写的是多么相似。这就是我们为这个应用程序所要做的一切。

你现在可以在本地测试你的应用，在云中与 AWS AppSync 的 DynamoDB 交互。从您的命令行，或者`yarn start`或者`npm start`来启动浏览器中`localhost:3000`上的开发服务器。在应用程序中添加一些 todo，然后检查添加的 todo 是否出现在 AWS 管理控制台的 DynamoDB 中。

### [第八步:在 S3 托管无服务器(客户端专用)应用](#serverless-step-8-amplify-add-s3-hosting)

我们可以通过 HTTP 为 S3 选择`DEV`，或者通过 CloudFront 发行版为 S3 和 HTTPS 选择`PROD`。出于本文的目的，我们将使用 S3 的 HTTP。

从命令行，我们可以为我们的应用添加 S3 托管:

```
amplify add hosting 
```

然后完成以下步骤来托管应用程序:

*   *选择环境设置*。选择环境设置:“选择:`DEV (S3 only with HTTP)`。
*   *指定托管桶名称*。“托管桶名称”:选择默认名称。
*   *指定网站的`index`文件*。“网站索引文档”:输入`index.html`，默认为。
*   *指定网站的`error`文件*。“网站错误文档”:输入`index.html`，默认为。

我们现在可以在命令行中使用以下命令部署到 S3:

```
amplify publish 
```

当系统提示“您确定要继续吗？”时，点击`Y`。

然后，您将看到一条消息:“正在更新云中的资源。这可能需要几分钟……”

如果一切顺利，您将收到消息:

> ✔上传文件成功。您的应用已成功发布。http://-dev . S3-website-us-east-1 . Amazon AWS . com

您的浏览器应该会自动打开一个指向 url `http://<your hosting bucket name>-dev.s3-website-us-east-1.amazonaws.com`的标签。

您的应用程序现已在网上发布，供您分享！测试一下，刷新浏览器，看看你所有的待办事项！祝贺您创建了一个无服务器应用程序！

* * *

## [比较有服务器和无服务器方法(A 与 B)](#comparing-serverful-and-serverless-approaches)

阅读完这篇详细的文章后，您将能够体会到 *serverful* 方法和 *serverless* 方法之间的差异，前者您必须制作两个应用程序——客户端和服务器——除了数据库之外，还要分别部署这两个应用程序，后者您只需要使用 Amplify 制作和部署一个客户端应用程序，并对 AWS 进行一些非常简单的配置。

当编写一个服务器级 GraphQL 应用程序的后端 API 时，您需要创建:

1.  GraphQL 服务器
2.  GraphQL 模式
3.  GraphQL 解析器
4.  数据源 CRUD 函数

然后，您需要部署服务器和数据库。这些都是重要的任务，会引入潜在的错误并需要持续的维护。即使是这个简单的 todo 应用程序，也需要花费大量的时间和精力来完成这些任务。

手动创建后端 API 涉及大量代码和复杂性，您可以将其传递给托管的 GraphQL BaaS，如 AWS AppSync。这将使你能够更快地迭代你的项目，这将更加[精益](http://theleanstartup.com/)和[敏捷](https://agilemanifesto.org/)。

加州大学伯克利分校题为“[简化的云编程:关于
无服务器计算](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2019/EECS-2019-3.pdf)的伯克利观点”的技术报告的作者预测:

> 无服务器计算将成为云时代的默认计算模式，在很大程度上取代有服务器计算，从而结束客户端-服务器时代。

我们认为，初创公司应该努力成为“[指数型组织](https://exponentialorgs.com/)”，利用云计算资产和技术，而无服务器的 web 应用制作方法是朝着正确方向迈出的一步。

无服务器是制作网络应用的未来。欢迎来到未来！