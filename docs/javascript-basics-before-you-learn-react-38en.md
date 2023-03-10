# 学习 React 之前的 JavaScript 基础知识

> 原文：<https://dev.to/nsebhastian/javascript-basics-before-you-learn-react-38en>

在理想的情况下，在深入 React 之前，您可以学习关于 JavaScript 和 web 开发的所有知识。

不幸的是，我们生活在一个不完美的世界，所以在 React 之前大嚼 JavaScript 只会让你血流不止。

如果您已经有一些 JavaScript 经验，那么在 React 之前，您需要学习的只是您将实际用于开发 React 应用程序的 JavaScript 特性。

在学习 React 之前，您应该熟悉的关于 JavaScript 的事情有:

*   [ES6 类](#es6-classes)
*   [新变量声明 let/const](#declaring-variables-with-es6-let-and-const)
*   [箭头功能](#the-arrow-function)
*   [销毁作业](#destructuring-assignment-for-arrays-and-objects)
*   [映射和过滤](#map-and-filter)
*   [ES6 模块系统](#es6-module-system)

这是 JavaScript 特性的 20%,你将在 80%的时间里使用它，所以在本教程中，我将帮助你学习所有的特性。

## 探索创建 React App

开始学习 React 的通常情况是运行`create-react-app`包，它设置了运行 React 所需的一切。然后流程结束后，打开`src/app.js`会给我们呈现整个 app 中唯一的 React 类:

```
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <p>
            Edit <code>src/App.js</code> and save to reload.
          </p>
          <a
            className="App-link"
            href="https://reactjs.org"
            target="_blank"
            rel="noopener noreferrer"
          >
            Learn React
          </a>
        </header>
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

如果你以前没有学过 ES6，你会认为这个类语句是 React 的一个特性。这实际上是 ES6 的一个新特性，这就是为什么正确地学习 ES6 会让你更好地理解 React 代码。我们将从 ES6 课程开始。

## ES6 类

ES6 引入了类语法，其使用方式与面向对象语言如 Java 或 Python 类似。ES6 中的一个基本类应该是这样的:

```
class Developer {
  constructor(name){
    this.name = name;
  }

  hello(){
    return 'Hello World! I am ' + this.name + ' and I am a web developer';
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

语法后面是一个标识符(或简单的名字),可以用来创建新的对象。在对象初始化时总是调用`constructor`方法。传递到对象中的任何参数都将被传递到新对象中。比如:

```
var nathan = new Developer('Nathan');
nathan.hello(); // Hello World! I am Nathan and I am a web developer 
```

Enter fullscreen mode Exit fullscreen mode

一个类可以根据需要定义任意多的方法，在这种情况下，我们有返回一个字符串的`hello`方法。

### 类继承

一个类可以`extends`定义另一个类，从那个类初始化的新对象将拥有两个类的所有方法。

```
class ReactDeveloper extends Developer {
  installReact(){
    return 'installing React .. Done.';
  }
}

var nathan = new ReactDeveloper('Nathan');
nathan.hello(); // Hello World! I am Nathan and I am a web developer
nathan.installReact(); // installing React .. Done. 
```

Enter fullscreen mode Exit fullscreen mode

`extends`另一个类的类通常称为*子类*或*子类*，而被扩展的类称为*父类*或*超类*。子类也可以*覆盖*父类中定义的方法，这意味着它将用定义的新方法替换方法定义。例如，让我们重写`hello`函数:

```
class ReactDeveloper extends Developer {
  installReact(){
    return 'installing React .. Done.';
  }

  hello(){
    return 'Hello World! I am ' + this.name + ' and I am a REACT developer';
  }
}

var nathan = new ReactDeveloper('Nathan');
nathan.hello(); // Hello World! I am Nathan and I am a REACT developer 
```

Enter fullscreen mode Exit fullscreen mode

给你。来自`Developer`类的`hello`方法已被覆盖。

### 用在 React 中

现在我们理解了 ES6 类和继承，我们就可以理解在`src/app.js`中定义的 React 类了。这是一个 React 组件，但它实际上只是一个普通的 ES6 类，继承了 React 组件类的定义，后者是从 React 包中导入的。

```
import React, { Component } from 'react';

class App extends Component {
  // class content
  render(){
    return (
      <h1>Hello React!</h1>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们能够使用`render()`方法、JSX、`this.state`和其他方法的原因。所有这些定义都在`Component`类中。但是我们将在后面看到，类并不是定义 React 组件的唯一方式。如果不需要状态等生命周期方法，可以用函数代替。

## 用 ES6 `let`和`const`声明变量

因为 JavaScript `var`关键字全局声明变量，ES6 中引入了两个新的变量声明来解决这个问题，即`let`和`const`。都是一样的，在其中都是用来声明变量的。不同的是`const`在声明后不能改变它的值，而`let`可以。这两个声明都是局部的，这意味着如果在函数范围内声明`let`，就不能在函数外调用它。

```
const name = "David";
let age = 28;
var occupation = "Software Engineer"; 
```

Enter fullscreen mode Exit fullscreen mode

### 用哪个？

默认情况下，经验法则是使用`const`声明变量。稍后当你编写应用程序时，你会意识到`const`的值需要改变。这时候你就应该把`const`重构为`let`。希望它能让你习惯新的关键词，你会开始认识到你的应用程序中需要使用`const`或`let`的模式。

### 我们什么时候在 React 中使用？

每当我们需要变量的时候。考虑下面的例子:

```
import React, { Component } from 'react';

class App extends Component {
  // class content
  render(){
    const greeting = 'Welcome to React';
    return (
      <h1>{greeting}</h1>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为问候在整个应用程序生命周期中不会改变，所以我们在这里使用`const`来定义它。

## 箭头功能

Arrow 函数是 ES6 的一个新特性，在现代代码库中被广泛使用，因为它保持了代码的简洁和可读性。这个特性允许我们使用更短的语法编写函数

```
// regular function
const testFunction = function() {
  // content..
}

// arrow function
const testFunction = () => {
  // content..
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您是一名经验丰富的 JS 开发人员，从常规的函数语法转移到 arrow 语法开始可能会不舒服。当我学习 arrow 函数时，我用这两个简单的步骤来重写我的函数:

1.  移除函数关键字
2.  在`()`后添加粗箭头符号`=>`

圆括号仍然用于传递参数，如果只有一个参数，可以省略圆括号。

```
 const testFunction = (firstName, lastName) => {
  return firstName+'  '+lastName;
}

const singleParam = firstName => {
  return firstName;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 隐返

如果你的箭头函数只有一行，你可以不使用`return`关键字和花括号`{}`
返回值

```
const testFunction = () => 'hello there.';
testFunction(); 
```

Enter fullscreen mode Exit fullscreen mode

### 用在 React 中

创建 React 组件的另一种方法是使用箭头函数。反应带箭头功能:

```
const HelloWorld = (props) => {
  return <h1>{props.hello}</h1>; } 
```

Enter fullscreen mode Exit fullscreen mode

相当于 ES6 类组件

```
class HelloWorld extends Component {
  render() {
    return (
      <h1>{props.hello}</h1>;
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在 React 应用程序中使用 arrow 函数可以使代码更加简洁。但是它也将从组件中移除状态的使用。这种类型的组件称为*无状态功能组件*。您可以在许多 React 教程中找到该名称。

## 数组和对象的析构赋值

ES6 中引入的最有用的新语法之一，析构赋值是简单地复制对象或数组的一部分，并将它们放入命名变量中。一个简单的例子:

```
const developer = {
  firstName: 'Nathan',
  lastName: 'Sebhastian',
  developer: true,
  age: 25,
}

//destructure developer object
const { firstName, lastName } = developer;
console.log(firstName); // returns 'Nathan'
console.log(lastName); // returns 'Sebhastian'
console.log(developer); // returns the object 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们将来自`developer`对象的名字和姓氏赋给了新变量`firstName`和`lastName`。现在如果你想把`firstName`放入一个名为`name`的新变量中呢？

```
const { firstName:name } = developer;
console.log(name); // returns 'Nathan' 
```

Enter fullscreen mode Exit fullscreen mode

析构也适用于数组，只是它使用了索引而不是对象键:

```
const numbers = [1,2,3,4,5];
const [one, two] = numbers; // one = 1, two = 2 
```

Enter fullscreen mode Exit fullscreen mode

通过用`,` :
传递索引，可以跳过一些索引的析构

```
const [one, two, , four] = numbers; // one = 1, two = 2, four = 4 
```

Enter fullscreen mode Exit fullscreen mode

### 用在 React 中

多用于析构方法中的`state`，例如:

```
reactFunction = () => {
  const { name, email } = this.state;
}; 
```

Enter fullscreen mode Exit fullscreen mode

或者在功能性无状态组件中，考虑上一章的例子:

```
const HelloWorld = (props) => {
  return <h1>{props.hello}</h1>; } 
```

Enter fullscreen mode Exit fullscreen mode

我们可以简单地立即析构参数:

```
const HelloWorld = ({ hello }) => {
  return <h1>{hello}</h1>; } 
```

Enter fullscreen mode Exit fullscreen mode

析构数组也用在 React 的`useState`钩子上:

```
const [user, setUser] = useState(''); 
```

Enter fullscreen mode Exit fullscreen mode

## 映射和过滤

虽然本教程关注的是 ES6，但是 JavaScript array `map`和`filter`方法也需要提及，因为它们可能是构建 React 应用程序时最常用的 ES5 特性之一。特别是在处理数据方面。

这两种方法在处理数据时使用得更多。例如，假设从 API 结果中提取数据返回一个 JSON 数据数组:

```
const users = [
  { name: 'Nathan', age: 25 },
  { name: 'Jack', age: 30 },
  { name: 'Joe', age: 28 },
]; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以在 React 中呈现一个条目列表如下:

```
import React, { Component } from 'react';

class App extends Component {
  // class content
  render(){
    const users = [
      { name: 'Nathan', age: 25 },
      { name: 'Jack', age: 30 },
      { name: 'Joe', age: 28 },
    ];

    return (
      <ul>
        {users
          .map(user => <li>{user.name}</li>)
        }
      </ul>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以过滤渲染中的数据。

```
<ul>
  {users
    .filter(user => user.age > 26)
    .map(user => <li>{user.name}</li>)
  }
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

## ES6 模块系统

ES6 模块系统支持 JavaScript 导入和导出文件。为了解释这一点，我们再来看一下`src/app.js`代码。

```
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <p>
            Edit <code>src/App.js</code> and save to reload.
          </p>
          <a
            className="App-link"
            href="https://reactjs.org"
            target="_blank"
            rel="noopener noreferrer"
          >
            Learn React
          </a>
        </header>
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

在代码的第一行，我们看到了导入语句:

```
import React, { Component } from 'react'; 
```

Enter fullscreen mode Exit fullscreen mode

在最后一行我们看到了`export default`语句:

```
export default App; 
```

Enter fullscreen mode Exit fullscreen mode

为了理解这些陈述，让我们先讨论一下模块语法。

模块只是一个 JavaScript 文件，它使用`export`关键字导出一个或多个值(可以是对象、函数或变量)。首先，在`src`目录
中创建一个名为`util.js`的新文件

```
touch util.js 
```

Enter fullscreen mode Exit fullscreen mode

然后在里面写一个函数。这是一个**默认出口**

```
export default function times(x) {
  return x * x;
} 
```

Enter fullscreen mode Exit fullscreen mode

或者多个**命名出口**

```
export function times(x) {
  return x * x;
}

export function plusTwo(number) {
  return number + 2;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以从`src/App.js`
导入

```
import { times, plusTwo } from './util.js';

console.log(times(2));
console.log(plusTwo(3)); 
```

Enter fullscreen mode Exit fullscreen mode

每个模块可以有多个命名导出，但只能有一个默认导出。默认的导出可以在不使用花括号和相应的导出函数名的情况下导入:

```
// in util.js
export default function times(x) {
  return x * x;
}

// in app.js
import k from './util.js';

console.log(k(4)); // returns 16 
```

Enter fullscreen mode Exit fullscreen mode

但是对于命名导出，您必须使用花括号和确切的名称来导入。或者，导入可以使用别名来避免两个不同的导入使用相同的名称:

```
// in util.js
export function times(x) {
  return x * x;
}

export function plusTwo(number) {
  return number + 2;
}

// in app.js
import { times as multiplication, plusTwo as plus2 } from './util.js'; 
```

Enter fullscreen mode Exit fullscreen mode

从绝对名称导入，如:

```
import React from 'react'; 
```

Enter fullscreen mode Exit fullscreen mode

将对`node_modules`进行 JavaScript 检查，以获得相应的包名。因此，如果您正在导入一个本地文件，不要忘记使用正确的路径。

### 用在 React 中

显然，我们已经在`src/App.js`文件中看到了这一点，然后在导出的`App`组件被渲染的`index.js`文件中也看到了这一点。让我们暂时忽略 serviceWorker 部分。

```
//index.js file

import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';

ReactDOM.render(<App />, document.getElementById('root'));

// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: http://bit.ly/CRA-PWA
serviceWorker.unregister(); 
```

Enter fullscreen mode Exit fullscreen mode

请注意 App 是如何从`./App`目录导入的，并且省略了`.js`扩展名。我们可以只在导入 JavaScript 文件时省略文件扩展名，但是我们必须在其他文件中包含它，比如`.css`。我们还导入了另一个节点模块`react-dom`，它使我们能够将 React 组件呈现到 HTML 元素中。

至于 PWA，是让 React application 离线工作的一个特性，但是由于默认是禁用的，所以一开始也没必要学。在你有足够的信心构建 React 用户界面之后，最好学习 PWA。

## 结论

React 的伟大之处在于，它不像其他 web 框架那样在 JavaScript 之上添加任何外来的抽象层。

这也是 React 变得非常受 JS 开发者欢迎的原因。它简单地使用了 JavaScript 的精华，使得构建用户界面更容易和可维护。

React 应用程序中的 JavaScript 远不止 React specifix 语法，所以一旦您更好地理解了 JavaScript——尤其是 ES6——您就可以满怀信心地编写 React 应用程序了。

但这并不意味着你必须掌握 JavaScript 的所有知识才能开始编写 React app。现在就去写一个吧，随着机会的到来，你会成为一个更好的开发人员。

如果你喜欢这篇文章，可以考虑在 Twitter 上关注我，以便在我发布新的编程相关文章时得到通知。