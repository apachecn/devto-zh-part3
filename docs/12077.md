# 反应上下文 API

> 原文：<https://dev.to/magnificode/react-context-api-g5h>

[https://reactjs.org/docs/context.html](https://reactjs.org/docs/context.html)

从 React 16.3.0 开始，我们可以访问 React 上下文 API。传统上，正如 Kent C. Dodds [解释的那样](https://dev.to/kentcdodds/prop-drilling-38-temp-slug-4820416)，当涉及到将道具传递给一个应用程序的多个继承者时，我们经历了一点困惑和头痛。他创造了一个术语“正确钻井”，很好地描述了这个问题。

React Context API 旨在通过相当直接的`Provider`到`Consumer`关系来解决 prop 钻井问题。这使得在不一定是彼此直接后代的组件之间传递数据变得更加容易。

## 上下文

为了建立`Provider`到`Consumer`的关系，我们必须首先建立一个新的上下文。这个上下文作为一种边界，将上下文中的特定数据传递给其子组件。

```
const MyContext = React.createContext(); 
```

Enter fullscreen mode Exit fullscreen mode

## 提供者组件

一旦我们定义了一个上下文，我们就可以创建我们的提供者，它是我们将实际用来包装我们的应用程序(或我们的应用程序的一部分)并传递数据的组件。provider 组件只是一个相当简单的高阶组件，它包含您希望传递给应用程序各个级别的状态。简单地说，这就是你的数据所在的地方。

```
class MyProvider extends Component {
  state = {
    name: 'Dominic',
    age: 28,
  };
  render() {
    return (
      <MyContext.Provider value={{
        state: this.state
      }}>
        {this.props.children}
      </MyContext.Provider>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`MyContext.Provider`组件中的 value 属性将数据传递给子组件。所以在这种情况下，我们把状态作为一个对象传递下去。这使我们能够访问提供者状态。

## 消费者

在我们的提供者的任何子组件中，我们都需要编写一个消费者来获取数据。取代传统的`props`，数据通过`render props`
传递

```
class Company extends Component {
  render() {
    return(
      <div className="company">
        <MyContext.Consumer>
          {(context) => (
            //Fragment added here since you can only return one child
            <>
              <p>Welcome to {context.state.name}</p>
              <p>We are {context.state.age} years old!</p>
            </>
          )}
        </MyContext.Consumer>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 更新状态

除了通过`value`属性从提供者传递状态之外，您还可以传递函数。就像使用 Redux 一样，我们传递的这些方法就是我们的“动作”。

```
class MyProvider extends Component {
  state = {
    name: 'Dominic',
    age: 28,
  };
  render() {
    return (
      <MyContext.Provider value={{
        state: this.state,
        addYear: () => this.setState({
          age: this.state.age + 1
        })
      }}>
        {this.props.children}
      </MyContext.Provider>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在消费者中，我们现在可以使用这种方法。

```
class Company extends Component {
  render() {
    return(
      <div className="company">
        <MyContext.Consumer>
          {(context) => (
            <>
              <p>Welcome to {context.state.name}</p>
              <p>We are {context.state.age} years old!</p>
              <button onClick={context.addYear}>Add Year</button>
            </>
          )}
        </MyContext.Consumer>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

万岁！这样，我们完整的`App.js`文件应该是这样的:

```
import React, { Component } from 'react';

// Create new context
const MyContext = React.createContext();

// Create the Provider that will pass down state and methods to the rest of the application.
class MyProvider extends Component {
  state = {
    name: 'Dominic',
    age: 28,
  };
  render() {
    return (
      <MyContext.Provider value={{
        state: this.state,
        addYear: () => this.setState({
          age: this.state.age + 1
        })
      }}>
        {this.props.children}
      </MyContext.Provider>
    )
  }
}

// Create the consumer that will consume the data provided by the Provider.
class Company extends Component {
  render() {
    return(
      <div className="company">
        <MyContext.Consumer>
          {(context) => (
            //Fragment added here since you can only return one child
            <>
              <p>Welcome to {context.state.name}</p>
              <p>We are {context.state.age} years old!</p>
              <button onClick={context.addYear}>Add Year</button>
            </>
          )}
        </MyContext.Consumer>
      </div>
    )
  }
}

// We'll nest our Consumer inside another component just to show that we don't need to pass props to each component.
const Companies = () => (
  <div>
    <Company />
  </div>
)

class App extends Component {
  render() {
    return (
      // Ensure the provider wraps all the components you want to share data between.
      <MyProvider>
        <div className="App">
          <Companies />
        </div>
      </MyProvider>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

干得好！您已经启动并运行了 React 上下文 API。这种方法是尝试使用的很好的第一步，然后再使用像 Redux 这样更复杂的方法。没有第三方库，没有令人困惑(在我的头脑中仍然令人困惑)的动作和 reducers。只是漂亮、干净的 React API。