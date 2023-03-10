# 实用的 React 钩子:如何重构你的应用程序来使用钩子

> 原文：<https://dev.to/bnevilleoneill/practical-react-hooks-how-to-refactor-your-app-to-use-hooks-1mj4>

[![](img/228bdf6967a95add3f637a032819da7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nXu0oDnM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A-re5tZymal4yNn9J0O0K7w.png)

相当多的人对增加钩子来作出反应感到兴奋——我恰好是这些人中的一员！

一旦你通过了关于如何开始使用钩子的教程,你的下一个问题就变得显而易见了:你如何重构你现有的应用程序或者组件来使用钩子？你可能会面临哪些挑战？

### 简介

这篇文章的目标很简单，但是它的方法可能是独特的。对于这个主题，大多数作者可能会使用现有的应用程序，并向您展示重构它以使用钩子的步骤。这很好，但远非完美。

为什么？每个应用程序都是不同的，都有自己特定的使用案例和场景。

我不会向您展示如何重构单个应用程序来使用 React Hooks，而是向您介绍一些适用于各种应用程序类型的一般性挑战。别担心，我将从基本的关注点开始，然后转到更高级的用例。

### 为什么重构要用 React 钩子？

我不打算解释为什么你应该考虑重构你的组件来使用钩子。如果你正在寻找一些像样的论据，官方文件有一些。

### 先决条件

本文假设您对 React Hooks 的工作原理有所了解。如果你需要钩子如何工作的提示，看看这个有用的介绍。

一旦我们解决了这个问题，您就可以开始应对您在使用 React 钩子重构应用程序时所面临的挑战(和解决方案)。

[![](img/18c623c22aecb029a54b76acf9d59712.png)](https://logrocket.com/signup/)

### 大家面临的第一个问题:如何将类组件转换成函数组件

当您开始使用 React 钩子重构应用程序时，您将面临的第一个问题恰好是其他挑战的根源。

挑战很简单:如何在不破坏任何功能的情况下，将类组件重构为功能组件？

好吧，让我们看看你会遇到的一些最常见的情况，从最简单的开始。

#### 1。没有状态或生命周期方法的类组件

[![](img/f4072b7a67df4a43bd2434307f45353a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ocV21Vz---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AJQoBxHguCpt6JIrzC3O-Bg.gif)

<figcaption>3 秒重构类组件——等待动画😊</figcaption>

**N.B.** ，这张 GIF 对于更高级的读者来说可能足以看出这种从类到函数组件的重构的不同之处。为了便于理解，也为了其他需要更多上下文的人，我将解释并写出代码。

这是最基本的情况:一个非常愚蠢的类组件。它只是渲染一些 JSX。

```
// before
import React, {Component} from 'react';

class App extends Component {
  handleClick = () => {
    console.log("helloooooo")
  }

  render() {
    return <div> 
      Hello World 
      <button onClick={this.handleClick}>
        Click me! 
      </button>
    </div>  
  }
}

export default App 
```

重构这个组件非常简单。给你:

```
// after 
import React from 'react'

function App() {
  const handleClick = () => {
    console.log("helloooooo")
  }

  return <div> 
      Hello World 
      <button onClick={handleClick}> Click me! </button>
    </div>  
}

export default App 
```

这里有什么不同？

*   没有`class`关键字；替换为 JavaScript 函数
*   功能组件中没有`this`；替换为函数范围内的 JavaScript 值

就这些——没什么大不了的。我们继续吧。

#### 2。具有属性、一些默认属性值和属性类型声明的类组件

[![Refactoring the class component in 3 secs 💪](img/5ab3a017df747c83c5372cd8a026c5e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2f3J4YXD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AyPtj5MVeJfmn5n89kMunaA.gif)

<figcaption>3 秒重构类组件。</figcaption>

这是另一个没有太多开销的简单例子。考虑下面的类组件:

```
// before
class App extends Component {
  static propTypes = {
    name: PropTypes.string
  }
  static defaultProps = {
    name: "Hooks"
  }

  handleClick = () => {
    console.log("helloooooo")
  }

  render() {
    return <div> 
      Hello {this.props.name} 
      <button onClick={this.handleClick}> Click me! </button>
    </div>  
  }
} 
```

经过重构，我们得到了这个:

```
function App({name = "Hooks"}) {
  const handleClick = () => {
    console.log("helloooooo")
  }

  return <div> 
      Hello {name} 
      <button onClick={handleClick}>Click me! </button>
    </div>
}

App.propTypes = {
  name: PropTypes.number
} 
```

作为一个功能组件，这个组件看起来简单多了。props 变成函数参数，默认 props 通过 ES6 默认参数语法处理，静态 propTypes 替换成 App.propTypes，就这样！

#### 3。具有状态的类组件(单个或少数多个键)

当您拥有一个带有实际状态对象的类组件时，这个场景会变得更加有趣。你的很多类组件都属于这个类别，或者这个类别的一个稍微复杂一点的版本。

考虑下面的类组件:

```
class App extends Component {
  state = {
    age: 19
  }

  handleClick = () => {
    this.setState((prevState) => ({age: prevState.age + 1}))
  }

  render() {
    return <div> 
      Today I am {this.state.age} Years of Age 
      <div> 
        <button onClick={this.handleClick}>Get older! </button>
      </div>
    </div>
  }
} 
```

该组件仅跟踪状态对象中的单个属性。很简单！

[![The magic of useState in 3 secs ✌️](img/1b0203021854066d38cb954da7774e0c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--al1SxKWs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AE7XwHTCp2i9DJuJxWM9R8g.gif)

<figcaption>3 秒内使用状态的魔法。</figcaption>

我们可以对此进行重构，使用 useState 钩子，如下所示:

```
function App() {
  const [age, setAge] = useState(19);
  const handleClick = () => setAge(age + 1)

  return <div> 
      Today I am {age} Years of Age 
      <div> 
        <button onClick={handleClick}>Get older! </button>
      </div>
   </div>
} 
```

那看起来简单多了！

[![](img/864aa4ffdcb12657f6e588a4c8575be3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FDPdcQZC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2Av-UwlMqt7iW9BE1jzMYmYw.gif) 

<figcaption>重构成功了。耶！</figcaption>

如果该组件有更多的状态对象属性，您可以使用多个 useState 调用。那完全没问题，如下图:

```
function App() {
  const [age, setAge] = useState(19);
  const [status, setStatus] = useState('married')
  const [siblings, setSiblings] = useState(10)

  const handleClick = () => setAge(age + 1)

  return <div> 
      Today I am {age} Years of Age 
      <div> 
        <button onClick={handleClick}>Get older! </button>
      </div>
   </div>
} 
```

这是最基本的问题，但是如果你需要更多的例子，你可以在[这本有用的指南](https://scotch.io/tutorials/5-ways-to-convert-react-class-components-to-functional-components-w-react-hooks)中找到。

### 为增量挂钩的采用做出权衡

虽然重新编写应用程序/组件来使用钩子听起来很棒，但这是有代价的——首先是时间和人力。

如果您碰巧正在处理一个大型代码库，那么您可能需要在 Hooks 采用的早期阶段做出一些权衡。下面描述了一个这样的场景。

考虑以下组件:

```
const API_URL = "https://api.myjson.com/bins/19enqe";

class App extends Component {
  state = {
    data: null,
    error: null,
    loaded: false,
    fetching: false,
  }

  async componentDidMount() {
    const response = await fetch(API_URL)
    const { data, status } = {
      data: await response.json(),
      status: response.status
    }

    // error? 
    if (status !== 200) {
      return this.setState({
        data,
        error: true,
        loaded: true,
        fetching: false,
      })
    }

    // no error 
    this.setState({
      data,
      error: null,
      loaded: true,
      fetching: false,
    })
  }

  render() {
    const { error, data } = this.state;

    return error ? <div> Sorry, and error occured :( </div> :
      <pre>{JSON.stringify(data, null, '  ')}</pre>
  }
} 
```

该组件在装载时向远程服务器发出获取一些数据的请求，然后根据结果设置状态。

我不希望你把注意力放在异步逻辑上，所以你应该注意的是:setState 调用。

```
class App extends Component {
 ... 

  async componentDidMount() {
    ...

    if (status !== 200) {
      return **this.setState** ({
        data,
        error: true,
        loaded: true,
        fetching: false,
      })
    }

**this.setState** ({
      data,
      error: null,
      loaded: true,
      fetching: false,
    })
  }

  render() {
    ...
  }
} 
```

这里的 setState 调用接收一个具有四个属性的对象。这只是一个例子，但这里的一般情况是，**你有一个组件，它用许多对象属性**进行 **setState 调用。**

现在，使用 React 钩子，您可能会将每个对象值拆分成单独的 useState 调用。您可以使用带有 useState 的对象，但是这些属性是不相关的，在这里使用 object 可能会使以后将它分解成独立的定制钩子更加困难。

下面是重构的样子:

```
... 
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [loaded, setLoading] = useState(false);
  const [fetching, setFetching] = useState(false);
... 
```

等等——还没完呢！

this.setState 调用也必须修改成这样:

```
// no more this.setState calls - use updater functions. 
 setData(data);
  setError(null);
  setLoading(true);
  fetching(false); 
```

是的，这是可行的。但是，如果在组件中有许多 setState 调用，那么您将多次编写这些调用，或者将它们分组到另一个自定义钩子中。

现在，如果您希望在代码库中逐渐采用钩子，在保持稍微相似的 setState 签名的同时减少代码更改，会怎么样呢？这可能吗？

在这种情况下，你必须做出取舍。如果您正在开发后期代码库，这很容易发生！在这里，我们将介绍 useReducer 钩子。

useReducer 具有以下签名:

```
const [state, dispatch] = useReducer(reducer) 
```

reducer 是一个接受状态和动作并返回新状态的函数。

```
const [state, dispatch] = useReducer((state, action) => newState) 
```

然后，从 reducer 返回的新状态由组件通过状态变量使用。

如果您以前使用过 redux，那么您知道您的动作必须是具有某种类型属性的对象。然而，useReducer 的情况并非如此。相反，reducer 函数接受状态和一些动作，然后**返回一个新的状态对象**。

我们可以利用这一点，进行不那么痛苦的重构，如下所示:

```
... 
function AppHooks() {
  ... 
 const [state, setState] = useReducer((state, newState) => (
{...state, ...newState}
  ));

 setState({
    data,
    error: null,
    loaded: true,
    fetching: false,
  })
} 
```

上面是怎么回事？

您可以看到，我们没有在组件的任何地方更改大量的 this.setState 调用，而是选择了一种更简单的增量方法，这种方法不涉及大量的代码更改。

而不是 this.setState({data，error: null，loaded: null，fetching: false})，只是删除 this。，setState 调用仍然可以工作，但是有钩子！

让这一切成为可能的是:

```
const [state, setState] = useReducer((state, newState) => (
{ ...state, ...newState }
)); 
```

当您试图更新状态时，传递给 setState(通常称为 dispatch)的任何内容都会作为第二个参数传递给 reducer。我们称之为新状态。

现在，我们不用做一些花哨的 switch 语句(如 Redux 中)，而是返回一个新的 state 对象，用传入的新值覆盖以前的状态——类似于 setState 的工作方式，即通过更新状态属性而不是替换整个对象。

有了这个解决方案，就可以更容易地在您的代码库中采用增量式钩子——不需要大量的代码更改，并且具有类似的 setState 签名。

以下是完整的重构代码，代码改动较少:

[![Complete refactor with very little code change 👍](img/46ee277822e346b87d73b19805c7ec79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dwVLWVD_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A9NJD97N2LJWL0WHMsI9ydw.gif) 

<figcaption>完成重构，代码改动很少👍</figcaption>

```
function AppHooks() {
  const initialState = {
    data: null,
    error: null,
    loaded: false,
    fetching: false,
  }
  const reducer = (state, newState) => ({ ...state, ...newState })
  const [state, setState] = useReducer(reducer, initialState);

  async function fetchData() {
    const response = await fetch(API_URL);
    const { data, status } = {
      data: await response.json(),
      status: response.status
    }

    // error? 
    if (status !== 200) {
      return setState({
        data,
        error: true,
        loaded: true,
        fetching: false,
      })
    }

    // no error 
    setState({
      data,
      error: null,
      loaded: true,
      fetching: false,
    })
  }

  useEffect(() => {
    fetchData()
  }, [])

  const { error, data } = state
  return error ?  Sorry, and error occured :(  :
    <pre>{JSON.stringify(data, null, '  ')}</pre>
} 
```

### 简化生命周期方法

您将面临的另一个常见挑战是重构组件的 componentDidMount、componentWillUnmount 和 componentDidUpdate 生命周期方法中的逻辑。

useEffect 钩子是提取这个逻辑的最佳地方。默认情况下，useEffect 中的效果函数将在每次渲染后运行。如果你熟悉钩子，这是常识。

```
import { useEffect } from 'react'
useEffect(() => {
   // your logic goes here
   // optional: return a function for canceling subscriptions 
   return () = {}
}) 
```

那么这里可能有什么新东西呢？

useEffect 挂钩的一个有趣特性是可以传入的第二个参数:依赖数组。

考虑一个空的依赖数组的例子，如下所示:

```
import { useEffect } from 'react'
useEffect(() => {

}, []) // 👈 array argument 
```

在这里传递一个空数组将使 effect 函数仅在组件挂载时运行，并在组件卸载时清除。这非常适合于在组件挂载时希望跟踪或获取一些数据的情况。

下面是一个向依赖数组传递值的例子:

```
import { useEffect } from 'react'
useEffect(() => {

}, [name]) // 👈 array argument with a value 
```

这里的含义是，当组件挂载时，效果函数将被调用，同样，每当 name 变量的值发生变化时，效果函数也会被调用。

### 比较 useEffect 对象值

useEffect 挂钩接受一个函数参数，该参数可能会产生一些副作用。

```
useEffects(doSomething) 
```

然而，useEffect 钩子还接受第二个参数:函数中的效果所依赖的值的数组。例如:

```
useEffects(doSomething, [name]) 
```

在上面的代码中，doSomething 函数将只在 name 值更改时运行。这是一个非常有用的功能，因为您可能不希望效果在每次渲染后运行，这是默认行为。

然而，这引起了另一个问题。为了使 useEffects 仅在 name 已更改时调用 doSomething 函数，它将以前的 name 值与其当前值进行比较，例如 prevName === name。

这对原始的 JavaScript 值类型非常有用。

但是如果名字是一个对象呢？JavaScript 中的对象是通过引用来比较的！从技术上讲，如果 name 是一个对象，那么它在每次渲染时总是不同的，所以检查 prevName === name 将总是为 **false** 。

这意味着，doSomething 函数将在每次渲染后运行——这可能是一个性能问题，具体取决于您的应用程序类型。对此有什么解决办法吗？

考虑下面这个微不足道的组件:

```
function RandomNumberGenerator () {
  const name = 'name'

  useEffect(
    () => {
      console.log('Effect has been run!')
    },
    [name]
  )

  const [randomNumber, setRandomNumber] = useState(0)

  return (
    <div>
      <h1>{randomNumber}</h1>
      <button
        onClick={() => {
          setRandomNumber(Math.random())
        }}
      >
        Generate random number!
      </button>
    </div>
  )
} 
```

该组件呈现一个按钮和一个随机数。点击按钮后，会生成一个新的随机数。

[![](img/1a758b1a1a5c132aa18abcbd22640d88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TmRuACLZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AZVLW8R33-opRzmBCUXG-Kg.gif) 

<figcaption>随机数生成器。还要注意记录到控制台的值。</figcaption>

注意，useEffect 钩子的效果依赖于 name 变量。

```
useEffect(() => {
    console.log("Effect has been run!")
  }, [name]) 
```

在本例中，name 变量是一个简单的字符串。该效果将在组件挂载时运行；因此，console.log("效果已经运行！")将被调用。

在随后的渲染中，将进行浅层比较，例如，prevName === name，其中 prevName 表示新渲染之前名称的先前值。

字符串是按值比较的，所以“name”= = =“name”总是 **true** 。因此，效果不会运行。

因此，你得到的日志输出效果已经运行了！就一次！

[![](img/1a758b1a1a5c132aa18abcbd22640d88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TmRuACLZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AZVLW8R33-opRzmBCUXG-Kg.gif) 

<figcaption>值只记录一次！</figcaption>

现在，将 name 变量改为一个对象。

```
function RandomNumberGenerator() {
  // look here 👇
  const name = {firstName: "name"}

  useEffect(() => {
    console.log("Effect has been run!")
  }, [name])

  const [randomNumber, setRandomNumber] = useState(0);    

  return
{randomNumber}
{ setRandomNumber(Math.random()) }}>Generate random number! } 
```

在这种情况下，在第一次渲染之后，再次执行浅层检查。但是，由于对象是通过引用进行比较的— *而不是通过值进行比较—*,所以比较失败。例如，以下表达式返回 false:

`{firstName: "name"} === {firstName: "name"}`

因此，每次渲染后，效果都会在**之后运行，你会得到很多日志。**

[![](img/4d0b430063ef43ae5fd60510800a239d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W76ANyh_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AzM7WWz43YN7950GK3Ka6yg.gif) 

<figcaption>查看所有日志？</figcaption>

我们如何阻止这种情况发生？

#### 解决方案 1:使用 JSON.stringify

这个解决方案看起来是这样的:

```
...
...useEffect(() => {
    console.log("Effect has been run!")
}, [JSON.stringify(name)]) 
```

通过使用 JSON.stringify(name ),被比较的值现在是一个字符串，因此将通过值进行比较。

这是可行的，但要小心行事。仅对具有不太复杂的值和易于序列化的数据类型的对象使用 JSON.stringify。

#### 解决方案 2:使用手动条件检查

这个解决方案包括跟踪以前的值——在本例中是 name——并对其当前值进行深入的比较检查。

代码有点多，但工作原理是这样的:

```
// the isEqual function can come from anywhere 
// - as long as you perform a deep check. 
// This example uses a utility function from Lodash
import {isEqual} from 'lodash'

function RandomNumberGenerator() {
  const name = {firstName: "name"}

  useEffect(() => {
    if(!isEqual(prevName.current, name)) {
      console.log("Effect has been run!")
    }
  })

  const prevName = useRef; 
  useEffect(() => {
    prevName.current = name
  })

  const [randomNumber, setRandomNumber] = useState(0);

  return <div>
    <h1> {randomNumber} </h1>
    <button onClick={() => { setRandomNumber(Math.random()) }}>
       Generate random number!
    </button>
  </div>
} 
```

现在，我们在运行效果之前检查这些值是否不相等:

```
!isEqual(prevName.current, name) 
```

但是 prevName.current 是什么？通过钩子，您可以使用 useRef 钩子来跟踪值。在上面的例子中，负责这个的代码位是:

```
const prevName = useRef; 
useEffect(() => {
    prevName.current = name
}) 
```

这将跟踪在早期的 useEffect 钩子中使用的前一个名称。我知道这很难理解，所以我在下面提供了完整代码的注释版本:

```
/**
 * To read the annotations correctly, read all turtle comments first 🐢 
 // - from top to bottom. 
 * Then come back to read all unicorns 🦄 - from top to bottom. 
 */

function RandomNumberGenerator() {
  // 🐢 1\. The very first time this component is mounted, 
  // the value of the name variable is set below
  const name = {firstName: "name"}

  // 🐢 2\. This hook is NOT run. useEffect only runs sometime after render
  // 🦄 6\. After Render this hook is now run. 
  useEffect(() => {

  // 🦄 7\. When the comparison happens, the hoisted value 
  // of prevName.current is "undefined". 
  // Hence, "isEqual(prevName.current, name)" returns "false" 
  // as {firstName: "name"} is NOT equal to undefined.
    if(!isEqual(prevName.current, name)) {

  // 🦄 8\. "Effect has been run!" is logged to the console. 
  //console.log("Effect has been run!")
    }
  })

  // 🐢 3\. The prevName constant is created to hold some ref. 
  const prevName = useRef; 

  // 🐢 4\. This hook is NOT run 
  // 🦄 9\. The order of your hooks matter! After the first useEffect is run, 
  // this will be invoked too. 
  useEffect(() => {
    // 🦄 10\. Now "prevName.current" will be set to "name". 
    prevName.current = name; 
   // 🦄 11\. In subsequent renders, the prevName.current will now hold the same 
    // object value - {firstName: "name"} which is alsways equal to the current 
    // value in the first useEffect hook. So, nothing is logged to the console. 
  // 🦄 12\. The reason this effect holds the "previous" value is because 
    // it'll always be run later than the first hook. 
   })

  const [randomNumber, setRandomNumber] = useState(0)

  // 🐢 5\. Render is RUN now - note that here, name is equal to the object, 
  // {firstName: "name"} while the ref prevName.current holds no value. 
  return
{randomNumber}
{ setRandomNumber(Math.random()) }}> Generate random number! } 
```

#### 解决方案 3:使用 useMemo 钩子

在我看来，这个解决方案非常优雅。下面是它的样子:

```
function RandomNumberGenerator() {
  // look here 👇
  const name = useMemo(() => ({
    firstName: "name"
  }), [])

  useEffect(() => {
      console.log("Effect has been run!")
  }, [name])

  const [randomNumber, setRandomNumber] = useState(0)
  return
{randomNumber}
{ setRandomNumber(Math.random()) }}> Generate random number! } 
```

useEffect 挂钩仍然依赖于 name 值，但是这里的 name 值是 memoized，由 useMemo 提供。

```
const name = useMemo(() => ({
    firstName: "name"
}), []) 
```

useMemo 接受一个返回某个值的函数，在本例中是对象{firstName: "name"}。

useMemo 的第二个参数是一组依赖项，其工作方式与 useEffect 中的类似。如果没有传递数组，则每次渲染时都会重新计算该值。

传递一个空数组来计算挂载组件**时的值，而不需要**跨渲染重新计算该值。这使名称值在渲染中保持相同(通过引用)。

由于上面的解释，即使 name 是一个对象，useEffect 钩子现在也可以像预期的那样工作，而不需要多次调用 Effect。

name 现在是一个记忆化的对象，在不同的渲染中具有相同的引用。

```
...useEffect(() => {
      console.log("Effect has been run!")
}, [name]) // 👈 name is memoized! 
```

### 你的测试现在因为 useEffect 而中断？

当你重构你的应用程序(或组件)来使用钩子时，你可能会面临的一个更令人不安的问题是，你的一些旧测试现在可能会失败——似乎没有任何原因。

如果你发现自己处于这种境地，请理解失败的测试确实是有原因的。

对于 useEffect，需要注意的是效果回调不是同步运行的——它在渲染后的*晚*时间运行。因此，useEffect 不完全是 componentidmount+componentiddupdate+componentWillUnmount。

由于这种“异步”行为，当您引入 useEffect 时，您的一些(如果不是全部)旧测试现在可能会失败。

[![](img/cca094c3842d8e6f05baea3e345b8c6e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e6GJqIej--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aldu54Ang6lvOVa2JXXG1Dw%402x.png)

有什么解决办法吗？

使用来自[的](https://reactjs.org/docs/test-utils.html) [act](https://reactjs.org/docs/test-utils.html#act) 实用程序 react-test-utils 在这些用例中帮助很大。如果您使用 [react-testing-library](https://github.com/kentcdodds/react-testing-library) 进行测试，那么它可以很好地(在引擎盖下)与 act 集成。使用 react-testing-library，您仍然需要将测试中的手动更新，比如状态更新或触发事件，包装到 act_ 中。_

```
act.
act(() => {
    /* fire events that update state */
});
/* assert on the output */ 
```

在[这个讨论](https://github.com/kentcdodds/react-testing-library/issues/281#issuecomment-461145962)中有一个例子。在 act 中进行异步调用？[这里也有一个关于这个的讨论](https://github.com/kentcdodds/react-testing-library/issues/281)。

等等，什么？

您可能认为我掩饰了使用 act test 实用函数的解决方案。_ 我本打算写一份更详细的解释，但 Sunil Pai(T1)抢先了一步。如果你认为[的 React 文档](https://reactjs.org/docs/test-utils.html#act)没有很好地解释这个概念——我同意——你会在[这个回购](https://github.com/threepointone/react-act-examples)中找到 act 如何工作的惊人例子。

如果您使用像 [Enzyme](https://github.com/airbnb/enzyme) 这样的测试库，并且在您的测试中有一些实现细节，例如调用 instance()和 state()这样的方法，就会出现另一个与测试失败相关的问题。在这些情况下，仅仅通过将组件重构为功能组件，您的测试就可能失败。

### 一种更安全的重构渲染道具 API 的方法

我不知道你，但我到处都在使用渲染道具 API。

重构一个使用 render props API 的组件来使用基于钩子的实现没什么大不了的。不过，有个小问题。

考虑以下展示渲染属性 API 的组件:

```
class TrivialRenderProps extends Component {
  state = {
    loading: false,
    data: []
  }
  render() {
    return this.props.children(this.state)
  }
} 
```

这是一个人为的例子，但足够好了！这里有一个如何使用这个组件的例子:

```
function ConsumeTrivialRenderProps() {
  return <TrivialRenderProps>
    {({loading, data}) => {
      return <pre>
        {`loading: ${loading}`} <br />
        {`data: [${data}]`}
      </pre>
    }}
  </TrivialRenderProps>
} 
```

呈现 ConsumeTrivialRenderProps 组件仅显示从呈现属性 API 接收的加载值和数据值。

[![](img/55c108edc98bff24b8fe229941842569.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tzXur-Em--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/348/1%2ArLBl0-pfLUwSjzae8Y4ljQ.png)

到目前为止，一切顺利！

render props 的问题是它会让你的代码看起来比你想要的更加嵌套。幸运的是，如前所述，将 TrivialRenderProps 组件重构为 Hooks 实现并不是什么大事。

为此，您只需将组件实现包装在一个自定义钩子中，并返回与以前相同的数据。如果做对了，下面是如何使用重构后的钩子 API:

```
function ConsumeTrivialRenderProps() {
  const { loading, setLoading, data } = useTrivialRenderProps()
  return <pre>
    {`loading: ${loading}`} <br />
    {`data: [${data}]`}
  </pre>
} 
```

看起来整洁多了！

下面是自定义钩子 useTrivialRenderProps:

```
function useTrivialRenderProps() {
  const [data, setData] = useState([])
  const [loading, setLoading] = useState(false)
 return {
    data,
    loading,
  }
} 
```

就是这样！

```
// before 
class TrivialRenderProps extends Component {
  state = {
    loading: false,
    data: []
  }
  render() {
    return this.props.children(this.state)
  }
}

// after 
function useTrivialRenderProps() {
  const [data, setData] = useState([])
  const [loading, setLoading] = useState(false)

  return {
    data,
    loading,
  }
} 
```

那么这里的问题是什么呢？

在处理大型代码库时，您可能会在许多不同的地方使用某个渲染属性 API。将组件的实现更改为使用钩子意味着您必须更改组件在许多不同地方的使用方式。

我们可以在这里做些交易吗？绝对的！

您可以重构组件以使用钩子，但也可以公开一个呈现道具 API。通过这样做，你可以在你的代码库中逐步采用钩子，而不是一次改变很多代码。

这里有一个例子:

```
// hooks implementation 
function useTrivialRenderProps() {
  const [data, setData] = useState([])
  const [loading, setLoading] = useState(false)
  return {
    data,
    loading,
  }
}
// render props implementation 
const TrivialRenderProps = ({children, ...props}) => children(useTrivialRenderProps(props));
// export both 
export { useTrivialRenderProps };  
export default TrivialRenderProps; 
```

现在，通过导出这两个实现，您可以在整个代码库中逐步采用钩子，因为以前的渲染道具消费者和新的钩子消费者都可以完美地工作！

```
// this will work 👇
function ConsumeTrivialRenderProps() {
  return <TrivialRenderProps>
    {({loading, data}) => {
      return <pre>
        {`loading: ${loading}`} <br />
        {`data: [${data}]`}
      </pre>
    }}
  </TrivialRenderProps>
}
// so will this 👇
function ConsumeTrivialRenderProps() {
  const { loading, setLoading, data } = useTrivialRenderProps()
  return <pre>
    {`loading: ${loading}`} <br />
    {`data: [${data}]`}
  </pre>
} 
```

我发现有趣的是，新的渲染道具实现也在钩子下使用钩子。

```
// render props implementation 
const TrivialRenderProps = ({children, ...props}) => children(useTrivialRenderProps(props)); 
```

### 处理状态初始值

基于某些计算初始化某些状态属性的类组件并不少见。这里有一个基本的例子:

```
class MyComponent extends Component {
  constructor(props) {
    super(props)
    this.state = { token: null }
    if (this.props.token) {
      this.state.token = this.props.token
    } else {
      token = window.localStorage.getItem('app-token');
      if (token) {
        this.state.token = token
      }
    }
  }
} 
```

这是一个简单的例子，但它显示了一个一般性的问题。有可能你的组件一挂载，你就根据一些计算在构造函数中设置一些初始状态。

在本例中，我们检查是否有令牌属性传入，或者本地存储中是否有 app-token 密钥，然后我们基于此设置状态。在重构钩子时，如何处理这样的逻辑来设置初始状态？

或许 useState 钩子一个不太为人所知的特性是，你传递给 useState 钩子的 initialState 参数——useState(initial state)——也可能是一个函数！

从这个函数返回的任何内容都将被用作初始状态。下面是组件被重构使用钩子后的样子:

```
function MyComponent(props) {
   const [token, setToken] = useState(() => {
     if(props.token) {
       return props.token 
     } else {
       tokenLocal = window.localStorage.getItem('app-token');
       if (tokenLocal) {
         return tokenLocal
       }
     }
   })   
} 
```

从技术上讲，逻辑几乎保持不变。这里重要的是，如果您需要根据某些逻辑初始化状态，您可以在 useState 中使用函数。

### 结论

重构应用程序来使用钩子并不是你必须做的事情。为你自己和你的团队权衡选择。如果您选择重构组件来使用新的 Hooks API，那么我希望您已经在本文中找到了一些很好的技巧。

回头见！

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[实用的 React 钩子:如何重构你的应用程序以使用钩子](https://blog.logrocket.com/practical-react-hooks-how-to-refactor-your-app-to-use-hooks-b1867e7b0a53/)最先出现在[博客](https://blog.logrocket.com)上。