# 使用 React 使用 Apollo GraphQL 服务器

> 原文：<https://dev.to/azure/consuming-an-apollo-graphql-server-usingreact-3kk4>

[![](img/7d94084bd95fb0da7e04140dc17eef15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ntpvo7HT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Ah-_NGiGhMwMHOPYU5Y8iFA.jpeg)

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

本文是 Graphql 系列文章的一部分

*   [使用 Node.js 和 Express 构建 GraphQL 服务器](https://dev.to/softchris/building-a-graphql-server-with-nodejs-andexpress-5hfh)
*   [使用 Node.js 和 Apollo 框架构建 GraphQL 服务器](https://dev.to/azure/creating-a-graphql-server-withapollo-jjj)
*   使用 React 使用 Apollo GraphQL 服务器，**我们在这里**

> 这是对我们如何从前端与 Apollo GraphQL 服务器交互的介绍。如果你不熟悉如何创建 Apollo 服务器，先看看这篇文章[创建 Apollo 服务器](https://dev.to/azure/creating-a-graphql-server-withapollo-jjj)

在本文中，我们将介绍:

*   **设置**，我们需要指定服务器的 URL 并实例化一个客户端
*   **查询**，有查询组件可以用来查询数据
*   **突变**，我们可以使用突变组件执行突变
*   **轮询/显式** **获取**，Apollo 在轮询、显式和获取数据方面提供了一些不错的功能

## **设置**

为了用 GraphQL 建立一个 React 应用程序，我们需要库`apollo-boost`和`react-apollo`。`apollo-boost`为我们提供了`ApolloClient`，我们需要实例化给定的`URL`。`react-apollo`给了我们一个*高阶*提供者`ApolloProvider`，我们需要用它来包装我们的应用程序。首先进行必要的安装:

```
yarn add react-apollo apollo-boost graphql 
```

一旦我们安装好了所有的东西，我们就可以开始设置了。转到`index.js`并键入以下内容:

```
import React, { Component } from "react";
import ApolloClient from "apollo-boost";
import { ApolloProvider } from "react-apollo";
import Local from “./components/Local”;

const localGraphQL = "http://localhost:4000/graphql";

const client = new ApolloClient({
  uri: localGraphQL
});
class App extends Component {
  render() {
    return (
      <ApolloProvider client={client}>
        <div>
          <h2>My first Apollo app </h2>
        </div>
     </ApolloProvider>
    );
  }
}
export default App; 
```

上面我们首先实例化了`ApolloClient`，并在这个过程中为它提供了一个`url`，即我们的 GraphQL 服务器的位置。

其次，我们用我们的`ApolloProvider`包装了整个应用程序，并且我们还用我们的`ApolloClient`实例设置了它的`client`属性。

现在我们已经准备好与 GraphQL 服务器进行交互了。

## **查询**

为了能够查询 Apollo 服务器，我们需要做三件事:

*   **写**我们的`gql`查询
*   **利用`react-apollo`库给我们的查询组件**
*   **渲染**响应

要编写我们的`gql`查询，我们需要导入`graphql-tag`，然后编写 GraphQL 查询，就像这样:

```
const getRates = gql`
{
  rates(currency: “USD”) {
    currency
    rate
  }
}`; 
```

此后，我们需要从`react-apollo`导入`Query`组件，并作为输入属性提供我们刚刚定义的查询，比如:

```
const Data = () => (
  <Query query={getRates} >
  // render the query results
  </Query> ); 
```

在我们的`Query`组件的第一个子组件中，我们正在调用一个将对象作为参数的函数。该对象具有以下属性:

*   **加载**，只要我们的查询还没有解决这是`true`
*   **错误**，如果我们从查询中返回一个错误
*   **数据**，我们查询的数据结果

现在我们已经了解了这些属性以及如何使用它们，让我们把所有的东西放在一起:

```
import React from "react";
import { Query } from "react-apollo";
import gql from "graphql-tag";
const getRates = gql`
{
  products(type: "DVD") {
    name
    price
  }
}`;
const Data = () => (
  <Query query={getRates} >
  {({ loading, error, data }) => {
  if (loading) return <p>Loading…</p>;
  if (error) return <p>Error :(</p>;
  return data.products.map(({ name, price }) => (
    <div key={name}>
      <p>{`${name}: ${price}`}</p>
    </div>
   ));
  }}
</Query>
);
export default Data; 
```

我们现在已经了解了如何从 GraphQL 服务器读取数据并呈现给用户。

## **投票**

您不仅希望获取数据，有时还希望定期获取数据，而不需要显式导航到某个页面或按特定按钮来触发 GET 请求。例如，我们在聊天应用中使用它来获得实时感。我们当然是在讨论轮询，以我们指定的固定间隔获取数据。我们学习使用的`Query`组件内置了轮询，我们需要做的就是将一个`pollInterval`属性设置为我们想要的获取间隔的毫秒数。让我们看看那会是什么样子:

```
import React from "react";
import { Query } from "react-apollo";
import gql from "graphql-tag";
const GET_DATA = gql`
{
  products {
    name
    id
  }
}
`;
const DataPull = () => (
  <Query query={GET_DATA} pollInterval={500}>
  {(loading, error, data, startPolling, stopPolling) => {
  if (loading) return null;
  if (error) return `Error!: ${error}`;
    return (
     <React.Fragment>
      {data.products.map(p => <div>{p.name}</div>)}
      <button onClick={()=> startPolling()}>Start polling</button>
      <button onClick={() => stopPolling()}>Stop polling</button>
    </React.Fragment>;
    )
}}
</Query>
);
export default DataPull; 
```

上面我们已经介绍了以下新概念:

*   **轮询间隔**，这是一个以毫秒为单位的轮询间隔，你可以看到我们将其设置为`500`，例如半秒
*   **开始轮询**，这是一个功能，如果我们之前已经停止轮询，我们可以在其中重新开始轮询
*   **停止轮询**，这是一个允许我们随时停止轮询的功能

### **重取**

有时我们最终会遇到这样的情况，我们希望显式地获取数据，以确保我们看到的是最新的数据。这样做的原因是对用户操作做出反应，而不是轮询。让我们看看如何使用这个`refetch`功能:

```
import React from "react";
import { Query } from "react-apollo";
import gql from "graphql-tag";
const GET_DATA = gql`
{
  products {
    name
    id
  }
}
`;
const Refetch = () => (
  <Query query={GET_DATA}>
  {(loading, error, data, refetch) => {
  if (loading) return null;
  if (error) return `Error!: ${error}`;
  return (
    <React.Fragment>
      <div>
        {data.prop}
        <button onClick={() => refetch()}>Fetch</button>
      </div>
   </React.Fragment>
  )
}}
</Query>
);
export default Refetch; 
```

上面我们看到，我们已经添加了另一个参数`refetch`到我们的`Query`子函数中，比如:

```
{(loading, error, data, refetch) => {
}} 
```

这个`refetch`参数是一个我们可以调用的函数，因此我们可以在我们的标记中将它绑定到一个按钮上，就像这样:

```
<button onClick={() => refetch()}>Fetch</button> 
```

## **突变**

当我们对 GraphQL 服务器进行变异时，我们需要做以下事情:

*   **调用**正确的突变

*   **使用**来自`react-apollo`的变异组件

以上听起来不太像，也确实不是。让我们从第一件事开始，我们的突变查询:

我们将使用来自`graphql-tag`库的`gql`助手来创建我们的变异查询。此后，我们使用关键字`mutation`，随后给突变命名并指定其输入参数`$person`。此时我们有以下查询:

```
const ADD_PERSON = gql`
mutation AddPerson($person: Person!) {
}
`; 
```

现在我们准备调用我们在 GraphQL 服务器中定义的实际突变`addPerson`。您的变异查询现在应该是这样的:

```
const ADD_PERSON = gql`
  mutation AddPerson($person: Person!) {
    addPerson(person: $person) {
      id
    }
  }
`; 
```

接下来是通过使用我们的 React 组件`Mutation`来使用变异查询。该组件需要两件东西:

*   **填充**的`mutation`属性，
*   **定义**组件`Mutation`的子组件，我们将需要为其提供一个函数，作为第一个参数，它包含将触发突变发生的`mutation`函数，作为第二个参数，它将接受一个具有属性`data`、`error`和`loading`的对象

让我们从使用`Mutation`组件的第一点开始，并设置它的`mutation`属性，就像这样:

```
import React from "react";
import { Mutation } from "react-apollo";
import gql from "graphql-tag";
const ADD_PERSON = gql`
  mutation AddPerson($person: Person!) {
    addPerson(person: $person) {
      id
    }
  }
`;
<Mutation mutation={ADD_PERSON}>
</Mutation> 
```

上面我们已经使用了我们的`Mutation`组件，并用我们的变异查询`ADD_PERSON`设置了`mutation`属性。接下来是定义`Mutation`组件的子组件。正如我们已经说过的，孩子是这样一个函数:

```
(addPerson, { data, loading, error }) => (
// JSX
) 
```

上面的函数预计将返回 JSX。我们需要定义一段 JSX，它允许我们使用以下内容:

*   **addPerson()** ，该函数将执行变异查询。
*   **加载**，这个布尔值将告诉我们我们的突变是否正在进行，使用这个值来决定是否使用微调器
*   **数据**，这是你的突变查询完成后返回的数据

现在我们已经了解了函数参数的用途，让我们来定义我们的 JSX。当我们想要收集数据时，定义一个表单是很习惯的，所以让我们这样做:

```
<form onSubmit={e => {
  e.preventDefault();
  addPerson({ variables: { person: { name: input.value } } });
  input.value = “”;
}} >
  <input ref={node => { input = node; }} />
  <button type=”submit”>Add Person</button>
  {loading &&
  <div>adding person…</div>
  }
  { data &&
  <div>response data</div>
  }
  { error &&
  <div>Error adding person…</div>
  }
</form> 
```

正如你在上面看到的，我们有一个表单，一个输入框和一个可以点击的按钮。我们将`addPerson()`方法挂接到表单的`onSubmit()`上。请注意，我们还解决了如何为我们的变异查询获取数据的问题。我们给`addPerson()`方法一个具有属性`variables`的对象，其中我们将一个对象赋给属性`person`。那个`person`属性与我们的变异查询中存在的输入参数相同。

其他字段`data`、`loading`和`error`用作条件 JSX，我们选择显示它们是否为真。

就是这样，这就是用一些参数调用突变并显示响应的全部内容，无论是实际数据还是错误。

下面是完整的代码。

```
import React from "react";
import { Mutation } from "react-apollo";
import gql from "graphql-tag";
const ADD_PERSON = gql`
  mutation AddPerson($person: Person!) {
    addPerson(person: $person) {
      id
    }
  }
`;
const DataInput = () => {
  let input;
  return (
    <Mutation mutation={ADD_PERSON}>
    {(addPerson, { data, loading, error }) => (
    <div>
      <form onSubmit={e => {
        e.preventDefault();
        addPerson({ variables: { person: { name: input.value } } });
        input.value = “”;
    }} >
      <input ref={node => { input = node; }} />
      <button type=”submit”>Add Person</button>
      {loading &&
      <div>adding person…</div>
      }
      { data &&
      <div>response data</div>
      }
      { error &&
      <div>Error adding person…</div>
      }
    </form>
  </div>
)}
</Mutation>)
}
export default DataInput; 
```

## **总结**

我们已经研究了与后端数据交互的不同方式。

*   **获取数据**，如果我们使用`Query`组件，我们可以通过用`gql`问题填充其`query`属性来获取数据

*   **轮询数据**，如果我们在`Query`组件上设置属性`pollInterval`，我们可以轮询我们的 GraphQL 后端。

*   **显式数据获取**，通过使用额外的参数`refetch`，我们可以在需要时显式获取数据

*   **触发突变**，我们了解到我们可以使用`Mutation`组件进行突变