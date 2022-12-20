# React Hook (useState)与 React 上下文 API

> 原文：<https://dev.to/devdammak/react-hook-usestate-with-react-context-api-3pco>

在 React 应用程序中管理状态是很棘手的，尤其是当你想要从组件 1 到(x)组件共享数据时，你将不得不从组件 1、2 到 7 传递数据，这基本上是不必要的。像 Redux、Flux、MobX e.tc 这样的包有助于管理状态，但这类包的问题是它们在 react 库的外部，有些人觉得完全理解、使用和集成 react 有点麻烦。因此 react 团队发布了 Redux 的替代版本，Redux 是 React 16.x 中的上下文 API。

# 什么是 React 上下文 API

> 它是一种特性/方法，提供了一种通过组件树传递数据的方式，而不必在每一层手动传递属性。

假设我是一名送货员，我想通过楼梯将一个包裹送到某栋大楼的 7 楼。为了让我到达 7 楼，我必须把包裹从 1 楼带到 2 楼到 3 楼..这有点麻烦，对吗？为什么我不能把自己从 1 楼跳跃/传送到 7 楼，反之亦然，而不会让自己从一栋建筑的 1 楼跳到最后一层(7 楼)。这就是通过 props 将数据从父组件传递到嵌套子组件的典型方式。但是有了 React Context，您就不必经历所有这些，您需要做的只是直接跳到第 7 层，然后跳回来，而无需进行任何额外/不必要的旅行。

## 简单指南

```
const ThemeContext = React.createContext('light');

class App extends React.Component {
  render() {
     return (
        <ThemeContext.Provider value="dark">
        <Toolbar />
       </ThemeContext.Provider>
     );
 }
} 
```

# 术语理解

### 反应. createContext

```
const MyContext = React.createContext(defaultValue); 
```

它将允许你创建一个上下文对象，它接受一个缺省值。当 React 呈现订阅该上下文对象的组件时，它将从树中它上面最近的匹配提供者读取当前上下文值。

### 上下文。供应者

```
<MyContext.Provider value={/* some value */}> 
```

创建上下文对象后，您将可以访问上下文 API 方法，如提供者、消费者等。提供者允许消费组件订阅上下文更改，还可以设置/存储值/数据。

### 上下文。消费者

```
<MyContext.Consumer> 
```

{value => /*根据上下文值*/}
渲染一些东西

顾名思义，它允许您使用来自上下文提供者的值集/存储。

## 什么时候应该使用 React 上下文 API

Context API 只在你需要从一个嵌套的组件中访问你的数据时使用，当你意识到你的组件的嵌套层次超过 3 层时，试着使用 Context API 来避免通过 props 传递不必要的数据。

## React Hook(使用状态)

索菲·阿尔珀特(Sophie Alpert)在 React Conf 2018 上表示，对于人类和机器来说，理解这些课程都非常令人困惑和讨厌。React 团队开发 React Hook 来管理无状态/功能组件的状态，并处理类组件支持的每个组件生命周期。例如，组件安装、组件更新、组件卸载等等

## 简单指南

```
import React, { useState } from 'react';
 const hookExample =()=>{
const[state, setState] =useState(0);
 return (
   <div>
    <p>You clicked {count} times </p>
    <button onClick={()=> setState()}>Click me </button>
  </div>
} 
```

> const [state，setState]= use state(0)；

我使用数组析构从 useState 中提取 state 和 setState。我将初始值/状态设置为零(0)。useState 可以接受任何默认值字符串、数字、布尔值、数组、对象。

返回一个包含两个元素的数组:当前状态和设置状态。(State 和 setState)只是变量表示，你可以用任何名字来表示你的 state 和 setState。

## 通过道具传递数据

```
import React, { Fragment } from "react";
import ReactDOM from "react-dom";

import "./styles.css";

//Building Component
const Building = () => {
  const data = {
    companyName: "DHL Delivery Package",
    recipientName: "Mr Adekoya Damola Felix",
    package: "MacBook Pro retina Display (20Kg)"
  };
  return <Floor1 data={data} />; };

//Floor1 Component
const Floor1 = ({ data }) => {
  return <Floor2 data={data} />; };

//Floor2 Component
const Floor2 = ({ data }) => {
  return <Floor3 data={data} />; };

//Floor3 Component
const Floor3 = ({ data }) => {
  return <Floor7 data={data} />; };

//Floor 7 Component
const Floor7 = ({ data }) => {
  return (
    <Fragment>
      <h3>Welcome to Floor 7</h3>
      <p>
        <strong>Company Name: </strong>
        {data.companyName}
      </p>
      <p>
        <strong>Recipient Name: </strong>
        {data.recipientName}
      </p>
      <p>
        <strong>Package: </strong>
        {data.package}
      </p>
    </Fragment>
  );
};
function App() {
  return (
    <div className="App">
      <h1>Simple Context API Tutorial using Hook(useState)</h1>
      <Building />
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

从上面的源代码可以看出，react 中没有上下文 API 或 redux 是传递数据的正常方式。为了让组件 7 接收数据(包)，包必须从 1 楼一直传递到 2 楼…7 楼。

## 用钩子和上下文 API 管理状态

```
import React from "react";

export default React.createContext(); 
```

在上面的代码片段中，我创建了一个可重用的上下文，它可以跨应用程序中的其他组件使用。

```
import React, { useState } from "react";
import PackageContext from "./context";

const provider = props => {
  const [state, setState] = useState({
    companyName: "DHL Delivery Package",
    recipientName: "Mr Adekoya Damola Felix",
    package: "MacBook Pro retina Display (20Kg)",
    deliveryStatus: "Delivery In Progress..."

  });
  return (
   <PackageContext.Provider
      value={{
        data: state,
        updateDeliveryStatus: () => {
          setState({ ...state, deliveryStatus: "Delivered" });
        }
      }}
    >
      {props.children}
    </PackageContext.Provider>
  );
};

export default provider; 
```

我导入前面创建的上下文组件，我们将使用它来设置我们的提供者值并消费该值。

> const [state，setState]= use state({
> company name:" DHL Delivery Package "，
> recipient name:" Mr Adekoya Damola Felix "，
> Package:" MacBook Pro retina Display(20k g)"
> })；

我设置了初始状态，这是要交付的包(数据)，您可以将提供者组件视为中心存储，也可以将包装我们的主要组件的更高级组件视为中心存储。

> return(
> {道具.儿童}
> 
> )；

我返回了我之前创建的上下文组件，分配一个值作为保存状态数据(包细节)的 props】

```
import React, { Fragment } from "react";
import ReactDOM from "react-dom";
import Provider from "./provider";
import Context from "./context";
import "./styles.css";

const Building = () => {
  return <Floor1 />;
};

//Floor1 Component
const Floor1 = () => {
  return <Floor2 />;
};

//Floor2 Component
const Floor2 = () => {
  return <Floor3 />;
};

//Floor3 Component
const Floor3 = () => {
  return <Floor7 />;
};

//Floor 7 Component
const Floor7 = () => {
  return (
    <Context.Consumer>
      {context => (
        <Fragment>
          <h3>Welcome to Floor 7</h3>
          <p>
            <strong>Company Name: </strong>
            {context.data.companyName}
          </p>
          <p>
            <strong>Recipient Name: </strong>
          {context.data.recipientName}
          </p>
          <p>
            <strong>Package: </strong>
            {context.data.package}
          </p>
    <p>
            <strong>Delivery Status: </strong>
            {context.data.deliveryStatus}
          </p>
          <button onClick={context.updateDeliveryStatus}>
            Update Delivery Status
          </button>
        </Fragment>
      )}
    </Context.Consumer>
  );
};

function App() {
  return (
    <div className="App">
      <h1>Hello CodeSandbox</h1>
      <h2>Start editing to see some magic happen!</h2>
      <Provider>
        <Building />
      </Provider>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

让我们直接进入最后一个组件(第 7 层)，我们将在那里使用我们的数据..我包装了所有的标签，允许我连接到我的商店并直接访问我的数据。

注意:在任何组件中，您想要使用或消费您已经保存在提供程序中的数据。您所要做的就是导入您的上下文组件并使用消费者属性，或者您可以从上下文组件中析构消费者。例如

> 从'导入{消费者}。/上下文组件'

从您的应用程序组件中，您需要将主组件(建筑组件)包装为父组件。建筑物的每个子/子组件都可以访问提供者数据，更像 redux 中的提供者。

## 如何从子组件或消费者组件更新我的状态

从组件 7 更新驻留在组件 1 中的状态可能看起来很奇怪，但是实际上可以从任何消费者组件更新状态。

> {
> data: state，
> updateDeliveryStatus:()=>
> {
> setState({...state，delivery status:" Delivered " })；
> }
> } { T7】

在提供者值数据对象中，您需要将想要在对象中执行的操作设置为一个函数，该函数稍后可以在使用者组件中触发。

## 结论

在这篇文章中，我希望我能够影响并解释上下文 api 和 react hook usestate 是如何工作的。Context API 基本上是您的中心存储，您可以在其中存储您想要在 react 应用程序中全局访问的数据。钩子允许你在一个功能组件中管理你的状态。

我在 codesandbox 上创建了一个简单的演示，演示如何使用 react hook useState 的上下文 API。

[https://codesandbox.io/embed/m3v26y05y](https://codesandbox.io/embed/m3v26y05y)

最初发布于[https://code burst . io/beginner-guide-to-react-context-API-with-hook-use state-4 df 7 c 3 e 08 ad](https://codeburst.io/beginner-guide-to-react-context-api-with-hook-usestate-4df7c3e08ad)