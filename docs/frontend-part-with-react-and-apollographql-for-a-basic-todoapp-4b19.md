# 具有 react 和 apollographql 的前端部分，用于基本的待办事项应用程序

> 原文：<https://dev.to/danielpdev/frontend-part-with-react-and-apollographql-for-a-basic-todoapp-4b19>

# [后端到了](https://dev.to/danielpdev/backend-part-with-expressjs-graphql-and-mongodb-for-a-basic-todo-app-4go2)

## 这里是 [codesandbox](https://codesandbox.io/s/70o7o6n86) 上的现场版

# 目录

*   [目录](#table-of-contents)
    *   [什么是 graphql？](#what-is-graphql)
    *   [简介](#intro)
    *   [安装先决条件](#install-prerequisites)
    *   [删除样板代码](#remove-boilerplate-code)
    *   [代码](#code)
    *   [入口点(index.js)](#entry-point-indexjs)
    *   [查询](#queries)
    *   [组件](#components)
    *   [TodoList.js【第一季第 1 集】](#todolistjs)
    *   [TodoCreate.js](#todocreatejs)
    *   [TodoLike.js](#todolikejs)
    *   [结论](#conclusion)

## 什么是 GraphQL？

Simple，一种用于定义 API 的查询语言，它提供了完整且可理解的数据描述，并支持强大的开发工具。
更多关于 [GraphQL](https://graphql.org/learn/) 的信息。

## [T1】简介](#intro)

对于我们的前端，我们将使用 React 和 ApolloClient 来获取数据。不是所有的文件都会在这篇文章中提到，因为它们中的大部分不包含任何 graphql 相关的东西，但是你可以通过访问 [codesandbox](https://codesandbox.io/s/70o7o6n86) 上的实时版本来检查它们。

## 安装先决条件

导航到您的项目目录并复制粘贴以下命令:

```
mkdir todo-react-graphql && cd todo-react-graphql npm init react-app todo-react-apollo-app && cd todo-react-apollo-app && npm install apollo-boost apollo-cache-inmemory graphql 
```

## 清除样板代码

```
rm src/* 
```

## 代码

### 入口点(index.js)

在`src/`中创建一个名为`index.js`的文件，并粘贴以下代码

```
import "./styles.scss";

import { InMemoryCache } from "apollo-cache-inmemory";

import React from "react";
import ReactDOM from "react-dom";

import ApolloClient from "apollo-boost";
import { ApolloProvider } from "react-apollo";
import App from "./components/App";
const URI = "https://apollo-graphql-todo.glitch.me/graphql"; //replace with your own graphql URI

const cache = new InMemoryCache({
  dataIdFromObject: object => object.id || null
});

const client = new ApolloClient({
  cache,
  uri: URI
});

const Root = () => {
  return (
    <ApolloProvider client={client}>
      <App />
    </ApolloProvider>
  );
};

ReactDOM.render(<Root />, document.querySelector("#root")); 
```

我们正在创建一个 InMemoryCache 实例，并将它作为缓存传递给我们的 apollo 客户机。InMemoryCache 是 ApolloClient 2.0 的默认缓存实现。
更多关于[阿波罗缓存](https://www.apollographql.com/docs/react/advanced/caching.html)。
因为我们使用的是 MongoDB，所以我们可以利用全局惟一标识符，任何文档都被分配了这个标识符并存储在 _id 中。这将是存储在缓存中的每个对象的键。

```
const cache = new InMemoryCache({
  dataIdFromObject: object => object.id || null
}); 
```

这里我们实际上是将`URI`设置为我们的后端

```
const client = new ApolloClient({
  cache,
  uri: URI 
}); 
```

在 render 函数中，我们返回包装在 ApolloProvider 组件中的 App 组件，并将我们的客户端实例作为 prop 传递。

```
const Root = () => {
  return (
    <ApolloProvider client={client}>
      <App />
    </ApolloProvider>
  );
}; 
```

### 查询

```
mkdir queries && cd queries && touch index.js 
```

```
import gql from "graphql-tag";

const ADD_TODO = gql`
  mutation AddTodo($content: String!) {
    addTodo(content: $content) {
      id
      content
    }
  }
`;

const GET_TODOS = gql`
  {
    todos {
      id
      content
      likes
    }
  }
`;

const DELETE_TODO = gql`
  mutation DeleteTodo($ID: ID) {
    deleteTodo(id: $ID) {
      id
    }
  }
`;

const GET_TODO = gql`
  query Todo($id: ID!) {
    todo(id: $id) {
      id
      content
      likes
    }
  }
`;

const TODO_LIKE = gql`
  mutation TodoLike($id: ID) {
    likeTodo(id: $id) {
      id
      likes
    }
  }
`;

const UPDATE_TODO = gql`
  mutation UpdateTodo($id: ID!, $content: String!) {
    updateTodo(id: $id, content: $content) {
      id
    }
  }
`;

export { TODO_LIKE, GET_TODO, DELETE_TODO, GET_TODOS, ADD_TODO, UPDATE_TODO }; 
```

这里我们使用 graphql-tag 包来定义我们的查询和变异。Graphql-tag 用于生成一个语法树对象，我们将进一步将其用于查询和变异。与我们测试 graphql 后端时编写的内容非常相似。不同之处在于，任何查询和变异都必须用关键字名称包装起来，就像函数一样，你必须指定一个签名。

```
const GET_TODO = gql`
  query Todo($id: ID!) {
    todo(id: $id) {
      id
      content
      likes
    }
  }
`; 
```

我们说我们的名为`Todo`的查询接收到一个名为 id 的参数(前缀为$表示我们开始定义
参数),该参数将在我们的查询块中使用，并且必须是 ID 类型！表示该参数不可为空。`ID`是一种标量类型，表示唯一标识符，通常用作缓存的键。
同样的规则也适用于突变。围绕这一点，您可以找到将针对我们的后端运行的实际查询。
只是玩玩它，做一个`console.log(GET_TODO)`，看看`gql`生成的实际查询。

### 组件

```
cd .. && mkdir components && cd components 
```

## 你可以用. js

由于我们的组件中的大部分代码都是基本的 react，所以我不会全部讲述，我将只讲述 graphql 所在的部分。
我们要看的第一个组件是`TodoList`，这是主要组件，它的主要职责是加载并显示待办事项的
列表。

```
import React from "react";

import Loading from "./Loading";
import TodoItem from "./TodoItem";
import { Query } from "react-apollo";
import { Link } from "react-router-dom";
import { GET_TODOS } from "../queries";

const TodoList = props => (
  <Query query={GET_TODOS}>
    {({ loading, error, data }) => {
      if (loading) return <Loading />;
      if (error) return `Error! ${error.message}`;
      const { todos } = data;

      return (
        <React.Fragment>
          <div className="cards">
            {todos &&
              todos.map(todo => (
                <TodoItem
                  key={todo.id}
                  {...todo}
                  onUpdate={id => {
                    props.history.push(`/todo/${id}`);
                  }}
                />
              ))}
          </div>
          <Link to="/todos/new">
            <i
              className="fas fa-plus-circle fa-2x has-text-success"
              style={{
                float: "right"
              }}
            />
          </Link>
        </React.Fragment>
      );
    }}
  </Query> );

export default TodoList; 
```

首先，依赖于来自查询的响应的所有内容必须作为子元素放在一个`Query`组件中。
它收到的一个道具是需要在后端运行的实际查询，在我们的例子中，查询是:

```
{
    todos {
      id
      content
      likes
    }
} 
```

是我们抓取完成后使用的道具。这个组件被创建了两次。第一次是在查询开始时，第二次是在查询成功或失败后。如果出现网络问题或任何其他错误，我们将定义错误属性并包含错误消息。
如果成功，我们的数据道具将包含转换为 js 对象的实际 todos，并准备好供使用。

## TodoCreate.js

```
import { Mutation } from "react-apollo";
import React, { useState } from "react";
import useLoading from "./../hooks/useLoading";
import { ADD_TODO, GET_TODOS } from "../queries";

const TodoCreate = props => {
  const [setLoadingButton, setLoadingState] = useLoading({
    classList: ["is-loading", "no-border"]
  });
  const [content, setContent] = useState("");

  return (
    <Mutation
      mutation={ADD_TODO}
      update={(cache, { data: { addTodo } }) => {
        try {
          const { todos } = cache.readQuery({ query: GET_TODOS });
          cache.writeQuery({
            query: GET_TODOS,
            data: { todos: todos.concat([{ ...addTodo, likes: 0 }]) }
          });
        } catch (e) {
        } finally {
          setLoadingState(false);
        }
      }}
    >
      {addTodo => (
        <div className="todo_form">
          <h4 className="todo_form__title">Add Todo</h4>
          <form
            onSubmit={e => {
              e.preventDefault();
              setLoadingState(true);
              addTodo({ variables: { content } });
              setContent("");
            }}
          >
            <div className="field">
              <div className="control">
                <input
                  autoCorrect="false"
                  autoCapitalize="false"
                  className="input"
                  type="text"
                  onChange={e => setContent(e.target.value)}
                />
              </div>
            </div>
            <button
              className="button is-light"
              type="submit"
              ref={setLoadingButton}
            >
              Create Todo
            </button>
          </form>
        </div>
      )}
    </Mutation>
  );
};

export default TodoCreate; 
```

这里我们有一个突变组件，它采用我们之前定义的突变属性对象`ADD_TODO`。

该突变的子节点将接收用于触发突变请求的实际函数作为第一个参数。

```
addTodo({ variables: { content } }); 
```

当需要发出请求时，我们必须传递一个带有变量属性的对象，该属性将用于触发
请求。

```
 <Mutation
      mutation={ADD_TODO}
      update={...}
      /> 
```

在我们的突变完成后，我们调用回调函数，新的数据将为我们准备好。

```
update={(cache, { data: { addTodo } }) => {
    try {
      const { todos } = cache.readQuery({ query: GET_TODOS });
      cache.writeQuery({
        query: GET_TODOS,
        data: { todos: todos.concat([{ ...addTodo, likes: 0 }]) }
      });
    } catch (e) {
    } finally {
      setLoadingState(false);
    }
  }
} 
```

因为我们使用的是缓存系统，所以我们必须通过使用`writeQuery`函数来改变缓存，传递一个包含我们正在为其编写缓存的`query`的对象和包含新内容的`data`对象。

然而，我们也有一个名为`refetchQueries`的道具，它出现在突变组件上，但是使用这个道具将触发对后端的新请求，这不是任何人所希望的，因为我们将消耗更多的网络资源。

对于更新操作，一切都是一样的，发出请求并更新缓存。

更多关于[突变](https://www.apollographql.com/docs/react/essentials/mutations.html)的信息。

## TodoLike.js

对于 like 按钮，我们希望将请求模拟得更快，因为我们不希望实际上等到请求到达后端并返回成功或失败。这是通过使用乐观 UI 来完成的，乐观 UI 将让我们模拟突变的结果，甚至在我们收到来自服务器的响应之前就更新 UI。万一我们的突变失败了，我们不必什么都不做，因为阿波罗会替我们处理这些脏活。它看起来怎么样？

```
likeTodo({
  variables: { id },
  optimisticResponse: {
    __typename: "Mutation",
    likeTodo: {
      id,
      __typename: "Todo",
      likes: likes + 1
    }
  }
}); 
```

就是这样，现在当我们点击 like 按钮时，我们处理响应的更新函数会立即被调用，它的行为就像是即时的。如果失败，该操作将被恢复。

作为练习，您还可以为我们的 TodoCreate 组件实现乐观 UI。

## 结论

对于开发需要支持 GraphQL 的前端来说，这是一个很棒的工具。它可以很容易地加速开发过程，并提供伟大的现代工具，帮助您监控和分析您的 API。

我希望你喜欢这篇文章。