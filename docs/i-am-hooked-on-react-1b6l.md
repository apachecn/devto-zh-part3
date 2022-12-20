# 我迷上了 React

> 原文：<https://dev.to/brightdevs/i-am-hooked-on-react-1b6l>

React 引入了一个新特性，允许你使用状态和其他 React 特性，而无需编写类，即将到来的[钩子](https://reactjs.org/docs/hooks-overview.html)，React 16.8.0 中的一个新提议，将会让你大吃一惊，使我们的无状态函数组件比以前做得更多！

# 初始工作

用钩子设置学习 React 环境的最简单方法是运行:

```
npx create-react-app my-app
cd my-app
npm start 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经初始化了 React 应用程序。
请确保使用 React-Hooks 时安装了正确的版本(我将使用的版本是`16.8.0`),因为今天它是 React 的最新版本。

如果你想知道你正在使用哪个版本，请使用命令:

`npm info react`

如果您想更新 react 和 react-dom，请使用命令:

`npm i react@next react-dom@next`

# 反应钩

在这篇文章中，我们将关注 React 中的 3 个基本钩子:

```
useState
useEffect
useContext 
```

Enter fullscreen mode Exit fullscreen mode

React 介绍的就更多了，但是下面的特性更复杂，要单独描述:

```
useReducer
useCallback
useMemo
useRef
useImperativeMethods
useLayoutEffect 
```

Enter fullscreen mode Exit fullscreen mode

### 使用状态

现在，您可以向功能组件添加状态。我们不需要为此单独写一个类。让我们创建一个简单的无状态计数器组件，并添加第一个钩子。

```
export const Counter = () => {
   return (
       <div>
           <p>Counter value: </p>
           <button onClick={}>Increase</button>
           <button onClick={}>Decrease</button>
       </div>
   )
} 
```

Enter fullscreen mode Exit fullscreen mode

这是简单的柜台。如果用户按下按钮`onClick`事件，我们的计数器将增加和减少值。

它的工作方式是，你可以传递初始状态作为第一个参数，在我的例子中，它等于 0。

```
const [count, setCount] = useState(0); 
```

Enter fullscreen mode Exit fullscreen mode

`useState`钩子返回一个数组。数组的第一项是当前状态的值，此时它将为 0。数组的第二项是更新状态的函数。

现在，我们可以通过状态和函数的当前值来更新状态:

```
export const Counter = () => {
   const [count, setCount] = useState(0);
   return (
       <div>
           <p>Counter value: {count}</p>
           <button onClick={() => {setCount(count + 1)}}>Increase counter</button>
           <button onClick={() => {setCount(count -1)}}>Decrease counter</button>
       </div>
   )
} 
```

Enter fullscreen mode Exit fullscreen mode

### 使用效果

钩子能够避免函数组件的副作用。和大家熟知的`componentDidMount`和`componentDidUpdate`差不多。

因此，我们简单地将它导入并添加到我们的计数器中，并传递一个匿名函数作为第一个参数:

```
const [count, setCount] = useState(0);
const [checked, changeCheckbox] = useState(true)
useEffect(() => {
   console.log('hello from useEffect')
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当我们改变 checkbox 的当前值时，文本`hello from useEffect`将被渲染(所以每当函数刷新对 DOM 的更改时，包括第一次渲染)。

`useEffect`的真正强大之处在于我们可以传递第二个可选参数，这是一个数组。然后，我们可以指定只在改变计数值的情况下才调用这个效果。

```
useEffect(() => {
   console.log('hello from useEffect')
}, [count]) 
```

Enter fullscreen mode Exit fullscreen mode

现在，`useEffect` **将仅在计数状态将改变**的情况下被调用。很酷，对吧？

### 使用上下文

想象一下，问题是来自索引文件的用户名和姓氏作为道具传递给组件。

我们将创建另外两个功能组件:`Header`和`LoginInfo`。组件将只呈现从`Dashboard`作为`prop`传递的值。

`Dashboard`实际上并不使用它，只是将它传递给`Header`,后者使用来自用户状态的值，并将它传递给`LoginInfo`,后者也呈现用户的名字。
这种方法很好，但是我们必须让用户通过一堆组件(在我们的例子中`Dashboard`并不关心它)。

让它看起来更好的一种方法是使用`createContext`，创建一个新的上下文，并返回当前的上下文值，该值由给定上下文的最近的上下文提供者给出。
所以让我们创建并导出`Context`对象:

```
import React from 'react'
const Context = React.createContext()
export default Context 
```

Enter fullscreen mode Exit fullscreen mode

在我们的 index 应用程序中，我们导入上下文并用`<Context.Provider>`包装整个主页面组件，并将来自 state 的用户值作为道具传递。现在，我们可以从 state 访问所有的
上下文消费者的值，我们不需要将它作为道具通过组件传递。

```
import React, { Component } from 'react';
import './App.css';
import { Dashboard } from "./Dashboard";
import Context from './Context'

class App extends Component {
   state = {
       user: 'John Doe'
   }
   render() {
       const {user} = this.state
       return (
           <Context.Provider value={user}>
               <Dashboard />
           </Context.Provider>
       );
   }
}
export default App; 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们使用`useContext` Hook 并包装我们的上下文，其中的值是从提供者(我们的索引)传递的值。让我们给用户变量赋值。

```
import React, { useContext } from 'react'
import LoginInfo from './LoginInfo'
import Context from './Context'

const Header = () => {
    const user = useContext(Context)
    return (
        <div>
            <h1>Welcome {user}</h1>
            <LoginInfo />
        </div>

    )
}

export default Header 
```

Enter fullscreen mode Exit fullscreen mode

`LoginInfo`的情况也是如此。我们通过使用`useContext`钩子声明了一个值`user`，这个值是从提供者(我们的索引)传递来的。

```
import React, { useContext } from 'react'
import Context from './Context'

const LoginInfo = () => {
    const user = useContext(Context)
    return (
        <h6>Logged as {user}</h6>
    )
}

export default LoginInfo 
```

Enter fullscreen mode Exit fullscreen mode

在`LoginInfo`和`Header`中，现在我们有了道具用户作为值，所以我们可以从根本不使用它的`Dashboard`中移除不必要的道具。

# 反应钩子限制

虽然看起来很好，但是了解钩子真的很好:

*   挂钩只能从 React 函数组件调用，
*   钩子应该只在顶层调用。不要在循环、条件或嵌套函数中调用它们。通过遵循这条规则，您可以确保每次组件呈现时都以相同的顺序调用钩子。这使得 React 能够正确地保存多个`useState`和`useEffect`调用之间的钩子状态。(如果你好奇更多，这里的是[的好解释)。](https://reactjs.org/docs/hooks-rules.html#explanation)

# 类基础组件将不再被支持？

我认为钩子和类库组件仍然有用。文档中说**“没有从 React 中移除类的计划”**，他们也绝对不建议把所有东西都重写到钩子中。它是独立的，你应该决定是使用钩子还是类。

钩子是 React 的一大特色。我个人是在稳定版发布后使用。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Patryk Huzarski，软件工程师@光明发明
[电子邮件](//patryk.huzarski@brightinventions.pl)