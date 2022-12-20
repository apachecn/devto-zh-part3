# React 基础–状态、道具和功能组件

> 原文：<https://dev.to/blizzerand/react-basics--state-props--functional-components-5cll>

## 简介

React 是目前使用的最流行的前端库之一。然而，初学者可能会发现很难开始使用 React 中的概念，因为它们与 Java 等传统编程概念不同。不同的方面，如组件如何工作，组件的组成和层次结构，状态，道具，以及函数式编程概念都需要事先考虑。本指南试图通过为读者提供一种简单易行的方法来开始使用 React，从而使事情变得更简单。

## 设置反应

有两种流行的方法来设置 React。如果您希望快速设置 React，可以通过包含来自 unpkg 的脚本来使用一页设置。

```
<script src="https://unpkg.com/react@16/umd/react.development.js"></script>
<script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/6.26.0/babel.js"></script> 
```

否则，您可以通过运行 create-react-app:
来设置 React 环境

```
npx create-react-app my-app 
```

或者，你也可以使用纱线。\

```
yarn create react-app my-app 
```

以下是 yarn 创建的文件结构:

```
my-app
├── README.md
├── node_modules
├── package.json
├── .gitignore
├── public
│   ├── favicon.ico
│   ├── index.html
│   └── manifest.json
└── src
    ├── App.css
    ├── App.js
    ├── App.test.js
    ├── index.css
    ├── index.js
    ├── logo.svg
    └── serviceWorker.js 
```

要启动应用程序，您需要运行 npm run start 或 yarn start。

## 组件

组件是 React 应用程序的构建块。您可以只使用组件来构建整个应用程序。它们是可重用的独立代码块。它们是两种类型的组件，其分类以不同的名称广为人知:

1.  类别与功能组件
2.  智能组件与非智能组件
3.  容器与表示组件
4.  有状态组件与无状态组件

虽然这些成分以不同的名称为人所知，但分类的基础是相对相似的。类组件使用 ES6 类，而功能组件基于 JavaScript 函数。

因为 React 的核心是组件，所以更好地理解如何在 React 中组织组件是必不可少的。在本文中，我们将通过定义功能组件和类组件之间的区别来解决这个问题。这里有一个基本的例子来说明它们之间的区别。

用以下代码行替换 src/App.js:

| `class App extends Component { \``render() { \ const message = `This is the App Component.`; \ return ( \ <**div**>{message}**div**> \ ); \ } \ }` |
|  |

这里的 App 组件是一个 ES6 类，因此我们称它们为类组件。它扩展了作为 React API 一部分的组件类。它包含一个包含 return 语句的“render”方法。return 语句中的所有内容都呈现在浏览器中。您可以呈现 HTML 元素或其他组件(读取组合层次)。

如果移除 render 方法，React 将抛出一个错误，因为类组件需要包含一个 render 方法。然而，其他方法是可选的。ReactDOM.render()使用 id“root”在 div 元素中呈现 App 组件。

但是请记住，这不是创建组件的唯一方式。您还可以使用如下功能组件:

| `function App(props){``const message = `This is the App Component.`; \ return <**div**>{message}**div**> \ }` |
|  |

那么，下一个明显的问题是，什么是道具？

### 道具

属性是指由父组件传递给子组件的属性。例如，如果您需要将一个值从父组件传递到子组件，您可以将它们作为属性或道具传递下去。

```
class App extends React.Component {
 render() {
  return <Child value="SOS" />;  
 }
} 
```

```
class Child extends React.Component{
 render(){
  return <h3>The value passed from parent is {this.props.value}</h3>;
 }
} 
```

您可以用这个功能组件替换我们之前创建的类组件，它在浏览器中的显示也是一样的。

```
function Child (props) {
 return <h3>The value passed from parent is {props.value}</h3>;
 }
} 
```

那么，为什么我们有两种不同类型的组件，而你可以只使用一种呢？这是因为类组件具有某些被赋予的特性，而功能组件则缺乏这些特性。

## 功能组件

与更受欢迎的类组件相比，功能组件没有什么包袱。理论上，它们比类组件要快，但是如果你运行的是一个组件非常有限的小应用程序，这可能就无关紧要了。

功能组件的缺点是不能在功能组件中使用状态和生命周期挂钩。相反，它们只是表示性的组件，没有自己的逻辑。不像类组件，你也不能使用 componentDidMount 和其他类似的生命周期钩子。但是相反，你可以如下包装你的 web UI 的一部分:

```
const Button = props => (
   <button className="our_button" onClick={props.onClick}>
      {props.label}
   </button>
); 
```

以下是功能组件的一些优点:

1.  与类组件相比，它们是可重用的
2.  功能组件可能具有更好的性能
3.  它们很容易调试

所以，你可以包装你的按钮，输入字段等。内部功能组件，并传递该组件所需的所有东西作为道具。然而，某些其他逻辑涉及进行 API 调用，然后将结果存储在状态中。这就是类组件派上用场的地方。

## 类组件

### 状态

与道具类似，状态也包含数据，但是有一些不同。

Props 包含由父组件传递的数据。另一方面，状态包含组件本地的私有数据。与只读值的属性不同，状态可由组件读取和写入。它存储在组件的不同渲染之间变化的数据。

这里有一个例子——

```
class App extends React.Component {
 constructor(){
  super();
  this.state = {name :"Foo Bar"};
 }
 changeName(){
  this.setState({name : "Lorem Ipsum"});
 }

 render(){
  return (
   <div>
     <h3>Hello {this.state.name}</h3>
     <button type='button' onClick=this.changeName.bind(this)}>
      Save
     </button>
   </div>
  );
 }
} 
```

如上例所示，一旦构造函数被初始化，就可以在呈现方法中使用。与 props 类似，可以通过“this.state”对象访问状态。单击保存按钮，用户可以根据自己的选择更改状态的值名。this.setState()负责更新状态。

### setState()

this.setState()是 React API 的一部分，用于修改状态。这在 React 组件中是默认可用的，也是改变状态的唯一方式。当对象作为参数传递给 setState 时，React 通过修改传递给它的键来异步更改状态。React 将查看传递的对象，并仅使用提供的值更改提供的状态键。

## 生命周期方法

React 为用户提供了被称为生命周期挂钩的特殊方法。这些生命周期挂钩在组件生命周期的特定时间执行。幸运的是，用户能够在这些生命周期挂钩中包含他们自己的功能。您可以在组件内部定义生命周期挂钩，以定义组件在装载、接收新道具、卸载等时做什么。下面是一些常用的生命周期挂钩的例子。

### componentidmount()

挂载是指组件最初在浏览器中呈现时所花费的时间。componentDidMount()在组件挂载后执行。这是获取特定数据或启动任何事情的好地方。

下面是一个组件挂载时发生的事件的示例。\

1.  通过调用 API 端点获取数据

2.  使用 this.setState()
    将响应存储到状态中

```
 componentDidMount(){
    componentDidMount() {
    fetch(API + DEFAULT_QUERY)
      .then(response => response.json())
      .then(data => 
         this.setState({ 
           person: 
               {
                name: data.name, age: data.age
               }
        })
     );
    }
 } 
```

### [T1】\](#)

componentWillUnMount()

这将在组件卸载之前执行。如果您想清除一些全局状态(存储在 Redux store 中)或删除一些事件侦听器，这应该是您的代码应该去的地方。

例如，如果你已经为 scroll 设置了一个事件监听器，你可以如下移除它:

```
componentWillUnmount() {
       window.removeEventListener('scroll', this.onScroll, false);
   } 
```

### componentDidUpdate()

顾名思义，componentDidUpdate()在组件完全更新后执行。这是处理数据更改和相关修改的地方。用户可能需要处理特定的网络请求，或者根据更改的数据执行计算。在这样的场景中，componentDidUpdate()是应该去的地方。

下面是一个实际例子—

```
class App extends React.Component {
 constructor(){
  super(); 
  this.state = {
   person : {name : "" , age : ""}
  };
 }
 componentDidMount(){
    componentDidMount() {
    fetch(API + DEFAULT_QUERY)
      .then(response => response.json())
      .then(data => 
         this.setState({ 
           person: 
               {
                name: data.name, age: data.age
               }
        })
     );
    }
 }

componentDidUpdate(prevProps) {
  // Typical usage (don't forget to compare props):
  if (this.props.person.name !== prevProps.person.name) {
    this.fetchData(this.props.name);
  }
}

 render(){
  return (
   <div>
    <p>Name : {this.state.person.name}</p>
    <p>Age : {this.state.person.age}</p>
   </div>
  );
 }
} 
```

我们的第一个状态包括两个不同的属性，即。姓名和年龄，这两者都有一个空字符串作为值。在 componentDidMount()中，用户可以根据需要设置状态和修改名称。

## 最后的话——选择正确的组件

那么，在功能组件和类组件之间如何选择呢？如果需要状态或组件生命周期，我通常从功能组件开始，然后转移到类组件。如果没有，你可以坚持使用功能组件。

您可以将功能组件用于任何不需要状态或者旨在充当 UI 元素的东西。如果它有复杂的逻辑，那么你应该把它放在一个类组件中。