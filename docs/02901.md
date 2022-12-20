# 掌握 React 道具的初学者指南

> 原文：<https://dev.to/bnevilleoneill/the-beginner-s-guide-to-mastering-react-props-295g>

[![](img/83816d4fd3c2a236123a4b16738087da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ezDrxPbo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ab3XCMVTro6APEBYkmUjJIA.png)

当您学习如何使用 React 开发 web 应用程序时，您将不可避免地遇到道具的概念。理解道具工作的方式对于掌握 React 是必不可少的，但是完全掌握这个概念并不是一件容易的事情。

### 简介

Props 代表“属性”，它们在 React 应用程序中用于将数据从一个 React 组件发送到另一个 React 组件。让我们看看下面的示例代码。这里我们有一个单独的 React 组件呈现一个字符串:

```
import React, { Component } from "react";
import ReactDOM from "react-dom";

class App extends Component {
  render(){
    return <div>Hello, World!</div>
  }
}

ReactDOM.render(<App />, document.getElementById("root")); 
```

下面是如何将 props 添加到 App 组件中:就在 ReactDOM.render 上对 App 组件的调用旁边，键入一个随机属性并为其赋值。我将创建一个名称属性，并将其指定为“Nathan”:

```
import React, { Component } from "react";
import ReactDOM from "react-dom";

class App extends Component {
  render(){
    return <div>Hello, World!</div>
  }
}

ReactDOM.render(<App name="Nathan" />, document.getElementById("root")); 
```

这样，App 组件现在有了一个名为 name 的道具；您可以使用这个从类中调用它。让我告诉你我是如何问候自己的

```
import React, { Component } from "react";
import ReactDOM from "react-dom";

class App extends Component {
  render(){
    return <div>Hello, {this.props.name}!</div>
  }
}

ReactDOM.render(<App name="Nathan" />, document.getElementById("root")); 
```

这是 props 的基础:当你调用一个组件时，它允许你把你能想到的任何数据发送到这个组件中。当你有两个或更多的组件时，你可以传递数据。下面是另一个有两个组件的例子:
[https://codesandbox.io/embed/v0no66rnol](https://codesandbox.io/embed/v0no66rnol)
如上面的代码所示，您可以在组件被调用时通过添加属性在组件之间传递属性，就像您在调用常规 JavaScript 函数时传递参数一样。说到函数，由于 React 也允许您使用函数创建组件，接下来让我们看看 props 如何在函数组件中工作。

[![](img/18c623c22aecb029a54b76acf9d59712.png)](https://logrocket.com/signup/)

### 功能组件中的道具

在一个函数组件中，组件接收属性就像普通的函数参数一样。一个函数组件将接收 props 对象，该对象具有您在组件调用中描述的属性:

```
import React from "react";
import ReactDOM from "react-dom";

function App() {
  return <Greeting name="Nathan" age={27} occupation="Software Developer" />;
}

function Greeting(props) {
  return (
    <p>
      Hello! I'm {props.name}, a {props.age} years old {props.occupation}.
      Pleased to meet you!
    </p>
  );
}

ReactDOM.render(<App />, document.getElementById("root")); 
```

除了一次传递多个属性之外，在本例中，您还可以看到年龄属性是一个数字数据类型。这表明您可以将 JavaScript 中可用的任何类型的数据(比如数字、布尔或对象)传递到 props 中。这就是 props 使您能够使用自顶向下的方法发送数据的方式，其中较高级别的组件可以向其下面的组件发送数据。

### 代码复用带道具和状态

道具的使用可以让你复用更多的 React 代码，避免重复自己。在我们的例子中，您可以为许多不同的人重用相同的问候组件:

```
import React from "react";
import ReactDOM from "react-dom";

function App() {
  return (
    <div>
      <Greeting name="Nathan" age={27} occupation="Software Developer" />
      <Greeting name="Jane" age={24} occupation="Frontend Developer" />
    </div>
  );
}

function Greeting(props) {
  return (
    <p>
      Hello! I'm {props.name}, a {props.age} years old {props.occupation}.
      Pleased to meet you!
    </p>
  );
}

ReactDOM.render(<App />, document.getElementById("root")); 
```

太好了！但是，由于 props 是只读的，并且在 React 应用程序的整个生命周期中不得手动更改，因此在 React 应用程序中只使用 props 并不能真正使它成为一个可以响应用户交互并相应渲染的动态应用程序。为了做到这一点，你需要使用状态。

状态和道具一起构成了 React 应用程序的数据“模型”。虽然 props 应该是只读的，但是状态是用于可以基于用户动作而改变的数据的。让我们看看它们是如何一起创建动态应用程序的。

首先，让我们添加一个名为 textSwitch 的新状态，它将一个布尔值存储到 App 组件，并将其传递给 Greeting 组件。Greeting 组件将查看这个 state 值来决定要呈现什么:
[https://codesandbox.io/embed/k99zxqrq7](https://codesandbox.io/embed/k99zxqrq7)
这个代码示例展示了如何根据用户的状态和属性来有条件地呈现应用程序的视图。在 React 中，状态作为道具从一个组件传递到另一个组件。因为属性名和值将作为常规的属性对象属性传递到组件中，所以它不关心数据来自哪里。

### 属性类型和默认属性

当您开发 React 应用程序时，有时您可能需要构造和定义一个 prop 来避免 bug 和错误。同样，函数可能需要强制参数，如果要正确呈现，React 组件可能需要定义一个属性。

您可能犯了一个错误，忘记将一个需要的道具传递给需要它的组件:

```
import React from "react";
import ReactDOM from "react-dom";

function App() {
  return <Greeting name="Nathan" />;
}

function Greeting(props) {
  return (
    <p>
      Hello! I'm {props.name}, a {props.age} years old {props.occupation}.
      Pleased to meet you!
    </p>
  );
}

ReactDOM.render(<App />, document.getElementById("root")); 
```

当 props.age 和 props.occupation 在 Greeting 组件中未定义时，React 将简单地忽略表达式来调用它们的值并呈现文本的其余部分。它不会引发任何错误，但你知道你不能让这种事情不了了之。

这就是 [propTypes 帮助](https://reactjs.org/docs/typechecking-with-proptypes.html)的地方。PropTypes 是一个特殊的组件属性，可用于验证组件中的属性。这是一个单独的、可选的 npm 包，所以你需要在使用它之前先安装它:

```
npm install --save prop-types 
```

现在让我们在问候组件中制作所需的道具:

```
import React from "react";
import ReactDOM from "react-dom";
import PropTypes from "prop-types";

function App() {
  return <Greeting name="Nathan" />;
}

function Greeting(props) {
  return (
    <p>
      Hello! I'm {props.name}, a {props.age} years old {props.occupation}.
      Pleased to meet you!
    </p>
  );
}

Greeting.propTypes = {
  name: PropTypes.string.isRequired, // must be a string and defined
  age: PropTypes.number.isRequired, // must be a number and defined
  occupation: PropTypes.string.isRequired  // must be a string and defined
};

ReactDOM.render(<App />, document.getElementById("root")); 
```

声明了 propTypes 属性后，当其 props 没有通过 propTypes 验证时，Greeting 组件将向控制台发出警告。

您还可以使用另一个名为 defaultProps:
[https://codesandbox.io/embed/o4zm1ojn3y](https://codesandbox.io/embed/o4zm1ojn3y)
的特殊属性，在调用时不将属性传递到组件中的情况下，为属性定义默认值。现在，当在没有属性的情况下调用问候语时，将使用 defaultProps 中的默认值。

### 将数据从子组件传递到父组件

父组件是在其代码块中调用其他组件的任何组件，而子组件只是被父组件调用的组件。父组件使用 props 将数据向下传递给子组件。

您可能想知道，“如何将数据从子组件传递到父组件？”

答案是不可能的——至少直接不可能。但是在 React 中有一点:你也可以传递一个函数作为道具。这和问题有什么关系？让我们首先回到带有状态的代码示例:

```
import React, { useState } from "react";
import ReactDOM from "react-dom";

function App() {
  const [textSwitch, setTextSwitch] = useState(true);
  return (
    <div>
      <button onClick={() => setTextSwitch(!textSwitch)} type="button">
        Toggle Name
      </button>
      <Greeting text={textSwitch} />
    </div>
  );
}
function Greeting(props) {
  console.log(props.text);
  if (props.text) {
    return (
      <p>
        Hello! I'm Nathan and I'm a Software Developer. Pleased to meet you!
      </p>
    );
  }
  return (
    <p>Hello! I'm Jane and I'm a Frontend Developer. Pleased to meet you!</p>
  );
}

ReactDOM.render(<App />, document.getElementById("root")); 
```

React 应用程序通常有三个组件层，顶层组件调用一个子组件，子组件又调用另一个子组件。为了说明这一点，我们需要稍微调整一下上面的例子。

让我们将`<button>`元素移出 App，放入它自己的组件中。为了简单起见，我们称之为 ChangeGreeting。然后，您将从 Greeting 组件而不是 App 组件调用这个组件:

```
import React, { useState } from "react";
import ReactDOM from "react-dom";

function App() {
  const [textSwitch, setTextSwitch] = useState(true);
  return (
    <div>
      <Greeting
        text={textSwitch}
      />
    </div>
  );
}

function Greeting(props) {
  let element;
  if (props.text) {
    element = (
      <p>
        Hello! I'm Nathan and I'm a Software Developer. Pleased to meet you!
      </p>
    );
  } else {
    element = (
      <p>Hello! I'm Jane and I'm a Frontend Developer. Pleased to meet you!</p>
    );
  }
  return (
    <div>
      {element}
      <ChangeGreeting />
    </div>
  );
}

function ChangeGreeting(props) {
  return (
    <button type="button">
      Toggle Name
    </button>
  );
}

ReactDOM.render(<App />, document.getElementById("root")); 
```

现在，用于设置状态的按钮位于 ChangeGreeting 组件中，该组件比状态所在的位置(在 App 组件中)低两层。那么你怎么可能改变这个状态呢？答案是你向下发送一个函数，直到它到达需要它的组件:
[https://codesandbox.io/embed/ly55kw306q](https://codesandbox.io/embed/ly55kw306q)
在上面的例子中，App 组件正在发送 handleClick prop，它具有将状态更改为问候语组件的功能。Greeting 组件实际上并不需要它，但是它的子组件 ChangeGreeting 需要它，所以它将属性转发到那里。

在 ChangeGreeting 组件上，当按钮被单击时，它将调用 handleClick 函数，使 App 执行该函数。

当 App 中的状态更新后，React 视图被重新渲染，然后新的状态值通过 props 发送到 Greeting。

所以，是的——React 不能将数据从子组件向上发送到父组件，但是父组件可以将函数发送到子组件。了解了这一点，您就可以将更新状态的函数发送到子组件中，一旦调用该函数，父组件就会更新状态。

您不能发送数据，但您可以使用函数发送改变信号。

### 支柱钻孔及如何处理

传递数据的最后一个例子实际上代表了您在处理属性和状态时可能会遇到的另一个常见问题:属性钻取。

道具钻取是指沿着组件层向下传递道具，直到它们到达指定的子组件，而其他更高的组件实际上并不需要它们。

在上面的例子中，这看起来不错，但是请记住，我们只有三个组件。当您有许多组件，并且所有组件都使用道具和状态相互交互时，道具钻取可能会成为一个令人头疼的维护问题。

为了避免这个问题，您可以做的事情之一是减少组件的数量，并且只在需要重用特定的组件时才创建新的组件。

回到这个例子，绝对不需要单独的 ChangeGreeting 组件，直到 Greeting 之外的另一个组件实际调用同一段代码。你只需要两个组件就可以做到:

```
import React, { useState } from "react";
import ReactDOM from "react-dom";

function App() {
  const [textSwitch, setTextSwitch] = useState(true);
  return (
    <div>
      <Greeting
        text={textSwitch}
        handleClick={() => setTextSwitch(!textSwitch)}
      />
    </div>
  );
}

function Greeting(props) {
  let element;
  if (props.text) {
    element = (
      <p>
        Hello! I'm Nathan and I'm a Software Developer. Pleased to meet you!
      </p>
    );
  } else {
    element = (
      <p>Hello! I'm Jane and I'm a Frontend Developer. Pleased to meet you!</p>
    );
  }
  return (
    <div>
      {element}
      <button onClick={props.handleClick} type="button">
        Toggle Name
      </button>
    </div>
  );
}

ReactDOM.render(<App />, document.getElementById("root")); 
```

这就对了——这样传递道具不需要道具钻孔。

### 结论

和所有学习 React 的事情一样，道具学起来很容易，但是很难掌握。现在你知道 props 是不可变的(只读的)数据，用于使 React 组件相互“对话”。它们非常类似于传递给函数的参数，可以是开发人员自己指定的任何内容。

状态和道具使您能够创建一个动态的 React 应用程序，该应用程序具有可重用、可维护和数据驱动的可靠代码库。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[反应道具初学者指南](https://blog.logrocket.com/the-beginners-guide-to-mastering-react-props-3f6f01fd7099/)首先出现在[日志博客](https://blog.logrocket.com)上。