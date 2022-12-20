# noobs 反应钩

> 原文：<https://dev.to/k_penguin_sato/react-hooks-for-noobs-3lao>

## 简介

`Hooks`是 React16.8 中引入的新特性。
我将尝试按照以下顺序解释它们是什么。

*   [什么是钩子？](#what-are-hooks)
*   [挂钩规则](#rules-of-hooks)
*   [状态挂钩](#state-hook)
*   [效果挂钩](#effect-hook)
*   [自定义挂钩](#custom-hooks)

# 什么是钩子？

根据 React 官方文档，

> 钩子让你不用写类就可以使用状态和其他 React 特性。

没错。就是这样！。
多亏了`hooks`，现在你可以在功能组件中使用一些 React 特性，比如`state`！！

我将在本帖中介绍以下 3 个钩子。

*   (1) **状态挂钩**:允许在功能组件中使用`state`和`setState`。
*   (2) **效果钩子**:让你在功能组件中执行取数等副作用。
*   (3) **定制钩子**:构建定制钩子可以让你将组件逻辑提取到可重用的函数中。

# 规则的钩子

为了安全使用钩子，你必须遵守两条基本规则。

**(1)只调用顶层的钩子！！**
不要在循环、条件或嵌套函数中调用钩子。

**(2)只从 React 函数调用钩子！！**
不要从常规的 JavaScript 函数中调用钩子。

# 状态挂钩

通过使用一个叫做`useState`的钩子，你可以在功能组件中使用`state`和`setState`。

让我们通过比较使用`useState`的`functional component`和等价的`class component`来看看如何使用`useState`。

### 等效类组件

它做以下两件事。

*   (1)定义`count`状态及其初始状态`0`。
*   (2)每次调用 setState 时，在`count`上加 1。

```
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  render() {
    return (
      <div>
        <p>You clicked{this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click Me
        </button>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 功能组件与状态挂钩

现在是使用`useState`创建一个功能组件的时候了，它和上面的类组件做同样的事情。

`useState`的基本语法是这样的！

```
const [state, setState] = useState(initialState); 
```

Enter fullscreen mode Exit fullscreen mode

这就像你一起定义了`state`、`setState`和`initialState`。

如果您想定义与上面的类组件相同的状态，它应该是这样的。

```
import React, { useState } from  'react';

function Counter() {
  const [count, setCount] = useState(0)
} 
```

Enter fullscreen mode Exit fullscreen mode

注意上面代码中的 3 件事！

*   (1) `count`相当于类组件中的`this.state={count:0}`。
*   (2) `setCount`相当于类组件中的`setState`部分。
*   (3) `0`是`count`的初始状态。

当你写剩下的代码时，请记住这两点。

*   (1)可以使用`count`目录！(不用做`this.count`。)
*   (2)可以使用`setCount`更新状态。

```
import React, { useState } from  'react';

function Counter() {
  const [count, setCount] = useState(0)

  return(
    <div>
      // (1) You can use count directory!
      <p>You clicked {count} times</p>

      // (2) You can update the state by using setCount.
      <button onClick={() => setCount(count + 1)}> Click me</button> 
    </div>
  )
}

export default Counter; 
```

Enter fullscreen mode Exit fullscreen mode

作为旁注，您可以像下面的代码一样定义多个`states`。

```
import React, { useState } from  'react';

function Counter() {
  const [count, setCount] = useState(0)
  const [name, setName] = useState('')

  return(
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}> Click me</button>
      <p>My name is {name}.</p>
      <button onClick={() => setName('テスト太郎')}>Show my name</button>
    </div>
  )
}

export default Counter; 
```

Enter fullscreen mode Exit fullscreen mode

# 效果挂钩

你可以使用一个叫做`useEffect`的钩子在功能组件中执行副作用！

让我们通过比较使用`useEffect`的`functional component`和等价的`class component`来看看如何使用`useEffec`。

### 示例类组件

在类组件中，我们在`componentDidMount` `componentDidUpdate`中执行取数据、更改 DOM 等副作用。

这里，它在组件安装后在控制台中输出`It did mount`,在更新发生后输出`It did get updated`。

```
import React from  'react';

class Effect extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  componentDidMount() {
    console.log('It did mount.')
  }

  componentDidUpdate() {
    console.log('It did get updated.')
  }

  render() {
    return (
      <div>
        <h1>You clicked {this.state.count} times</h1>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 使用 useEffect 的示例功能组件

`useEffect`钩就像是`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`的组合。
它在**之后运行，每次渲染**包括第一次渲染。当你用`hooks`构建 react 应用时，这是你产生副作用的地方。

```
import React, { useState, useEffect } from 'react'

function Effect() {
  const [count, setCount] = useState(0)

  useEffect(() => {
    console.log('It got rendered')
  })

  return(
    <div>
      <h1>You clicked {count} times</h1>
      <button onClick={() => setCount(count + 1)}> Click me</button>
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

# 自定义挂钩

正如我上面提到的，构建您的定制钩子可以让您将组件逻辑提取到可重用的函数中。

假设有如下两个组件。

*   (1)一个名为`Status`的组件，如果它接收到`id = 1`，则返回`Logged in`。
*   (2)一个名为`Message`的组件，如果它接收到`id = 1`，则返回`Welocme Back`。

```
export default function Status(props){
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  const handleStateChange = (id) => {
    if(id === 1){
      setIsLoggedIn(true)
    }
    else{
      setIsLoggedIn(false)
    }
  }

  useEffect(() => {
    handleStateChange(props.user.id)
  })

 const status = isLoggedIn ? 'Logged in' : 'Sign up'

  return (
    <>
      <h1>Status: {status}</h1>
    </>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

```
export default function Message(props){
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  const handleStateChange = (id) => {
    if(id === 1){
      setIsLoggedIn(true)
    }
    else{
      setIsLoggedIn(false)
    }
  }

  useEffect(() => {
    handleStateChange(props.user.id)
  })

 const message = isLoggedIn ? 'Welcome Back' : 'Who are you??'

  return (
    <>
      <h1>Message: {message}</h1>
    </>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能已经注意到了，这是非常多余的。
您可以构建一个`custom hook`来将两个组件中存在的相同逻辑提取到一个可重用的函数中。

※以`use`开头命名您的自定义挂钩非常重要。
在本例中，我将我的定制钩子命名为`useLogIn`。

```
import { useState, useEffect } from 'react';

export default function useLogIn(userId){
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  // The login you want to reuse.
  const handleStateChange = (id) => {
    if(id === 1){
      setIsLoggedIn(true)
    }
    else{
      setIsLoggedIn(false)
    }
  }

  // Perform side effects in useEffect.
  useEffect(() => {
    handleStateChange(userId)
  })

  return isLoggedIn;
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`useLogIn`，我们可以简化`Status`和`Message`组件。

```
import React from 'react';
import useLogIn from './useLogIn';

export default function Status(props){
  const status = useLogIn(props.user.id) ? 'Logged in' : 'Sign up'
  return (
    <>
      <h1>Status: {status}</h1>
    </>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

```
import React from 'react';
import useLogIn from './useLogIn';

export default function Message(props){
  const message = useLogIn(props.user.id) ? 'Welcome Back' : 'Who are you??'
  return (
    <>
      <h1>Message: {message}</h1>
    </>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

这个`custom hooks`可以用于其他很酷的东西，看看关于[建造你自己的钩子](https://reactjs.org/docs/hooks-custom.html)的官方文件。

# 资源

*   [引入挂钩–反应](https://reactjs.org/docs/hooks-intro.html)
*   阿米莉亚·瓦滕伯格的《反应钩子中的思考》
*   [React 挂钩指南-教程和示例| KendoReact](https://www.telerik.com/kendo-react-ui/react-hooks-guide)