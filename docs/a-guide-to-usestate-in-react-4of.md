# React 中使用状态的指南

> 原文：<https://dev.to/bnevilleoneill/a-guide-to-usestate-in-react-4of>

[![](img/774aeb739900a04934a2e740705ecf04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bum5l564--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/792/1%2AmaE0ve6HItzdk7hdkdApMQ.png)

useState 是一个钩子，它允许你在函数组件中拥有状态变量。

React 中有两种类型的组件，类组件和功能组件。

**类组件**是从 [React 扩展而来的](https://reactjs.org/docs/react-component.html) [ES6 类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)。组件可以有[状态和生命周期方法](https://reactjs.org/docs/state-and-lifecycle.html) :

```
class Message extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      message: ‘’    
    };
  }

  componentDidMount() {
    /* ... */
  }

  render() {
    return <div>{this.state.message}</div>;
  }
} 
```

**函数组件**是只接受参数作为组件属性并返回有效 JSX 的函数:

```
function Message(props) {
  return <div>{props.message}</div>
}
// Or as an arrow function
const Message = (props) =>  <div>{props.message}</div> 
```

如您所见，没有状态或生命周期方法。

然而，从 React 16.8 开始，我们可以使用名称以 use 开头的函数 **hooks、**来为函数组件添加状态变量，并为类的生命周期方法提供工具。

这篇文章是一个使用状态钩子的指南，相当于功能组件的 this.state/this.setSate。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### 宣告状态

useState 是 react 的一个命名导出，所以要使用它，可以写:

```
React.useState 
```

或者只需编写 useState:
即可导入

```
import React, { useState } from 'react'; 
```

但是不同于你可以在类中声明的状态对象，它允许你声明多个状态变量，就像这样:

```
import React from 'react';

class Message extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      message: '',
      list: [],    
    };
  }
  /* ... */
} 
```

useState 钩子允许你一次只声明一个状态变量(任何类型)，就像这样:

```
import React, { useState } from 'react';

const Message= () => {
   const messageState = useState( '' );
   const listState = useState( [] );
} 
```

useState 将状态变量的初始值作为参数。您可以直接传递它，如前面的例子所示，或者使用一个函数来延迟初始化变量(当初始状态是一个昂贵的计算的结果时很有用):

```
const Message= () => {
   const messageState = useState( () => expensiveComputation() );
   /* ... */
} 
```

初始值将仅在初始渲染时分配(如果是函数，将仅在初始渲染时执行)。

在随后的呈现中(由于组件或父组件中状态的改变)，将忽略 useState 挂钩的参数，并将检索当前值。

记住这一点很重要，因为，例如，如果您想要基于组件接收的新属性更新状态:

```
const Message= (props) => {
   const messageState = useState( props.message );
   /* ... */
} 
```

单独使用 useState 不起作用，因为它的参数只在第一次使用，而不是每次属性改变时使用(查看[这里的](https://stackoverflow.com/questions/54625831/how-to-sync-props-to-state-using-react-hook-setstate)以获得正确的方法)。

但是 useState 并不像前面的例子暗示的那样只返回一个变量。它返回一个数组，其中第一个元素是状态变量，第二个元素是更新变量的值的函数:

```
const Message= () => {
   const messageState = useState( '' );
   const message = messageState[0]; // Contains ''
   const setMessage = messageState[1]; // It’s a function
} 
```

通常，你会使用[数组析构](https://dev.to/sarah_chima/destructuring-assignment---arrays-16f)来简化上面的代码:

```
const Message= () => {
   const [message, setMessage]= useState( '' );
} 
```

这样，您可以像使用任何其他变量一样在功能组件中使用状态变量:

```
const Message = () => {
  const [message, setMessage] = useState( '' );

  return (
    <p>
      <strong>{message}</strong>
    </p>
  );
}; 
```

但是为什么 useState 会返回数组呢？

因为与对象相比，数组更加灵活和易于使用。

如果该方法返回一个具有固定属性集的对象，您将无法以简单的方式分配自定义名称。你必须这样做(假设对象的属性是 state 和 setState):

```
// Without using object destructuring
const messageState = useState( '' );
const message = messageState.state;
const setMessage = messageState

// Using object destructuring
const { state: message, setState: setMessage } = useState( '' );
const { state: list, setState: setList } = useState( [] ); 
```

### 更新状态

useState 返回的第二个元素是一个函数，它采用一个新值来更新状态变量。

下面是一个使用文本框在每次改变时更新状态变量的例子:

```
const Message = () => {
  const [message, setMessage] = useState( '' );

  return (
    <div>
      <input
         type="text"
         value={message}
         placeholder="Enter a message"
         onChange={e => setMessage(e.target.value)}
       />
      <p>
        <strong>{message}</strong>
      </p>
    </div>
  );
}; 
```

试试这里的。

但是，这个更新函数不会立即更新值。相反，它将更新操作排入队列。然后，在重新呈现组件后，useState 的参数将被忽略，该函数将返回最近的值。

如果使用以前的值来更新状态，则必须传递一个接收以前的值并返回新值的函数:

```
const Message = () => {
  const [message, setMessage] = useState( '' );

  return (
    <div>
      <input
        type="text"
        value={message}
        placeholder="Enter some letters"
        onChange={e => {
          const val = e.target.value;
          setMessage(prev => prev + val)
        } }
      />
      <p>
        <strong>{message}</strong>
      </p>
    </div>
  );
}; 
```

试试这里的。

但是，关于更新有两件重要的事情需要了解。

首先，如果使用与当前状态相同的值来更新状态(React 使用 [Object.is](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is#Description) 进行比较)，React 不会触发重新渲染。

例如，在处理对象时，很容易犯以下错误:

```
const Message = () => {
  const [messageObj, setMessage] = useState({ message: '' });

  return (
    <div>
      <input
        type="text"
        value={messageObj.message}
        placeholder="Enter a message"
        onChange={e => {
          messageObj.message = e.target.value;
          setMessage(messageObj); // Doesn't work
        }}
      />
      <p>
        <strong>{messageObj.message}</strong>
      </p>
  </div>
  );
}; 
```

试试这里的。

上面的例子没有创建一个新的对象，而是改变了现有的状态对象。反应过来，那是同一个物体。

要使它工作，必须创建一个新对象:

```
onChange={e => {
  const newMessageObj = { message: e.target.value };
  setMessage(newMessageObj); // Now it works
}} 
```

这就引出了你需要记住的第二件重要的事情。

当您更新状态变量时，与组件类中的 this.setState 不同，useState 返回的函数不会自动合并更新对象，而是替换它们。

按照前面的例子，如果我们向消息对象(id)添加另一个属性:

```
const Message = () => {
  const [messageObj, setMessage] = useState({ message: '', id: 1 });

  return (
    <div>
      <input
        type="text"
        value={messageObj.message}
        placeholder="Enter a message"
        onChange={e => {
          const newMessageObj = { message: e.target.value };
          setMessage(newMessageObj); // id property is lost
        }}
      />
      <p>
        <strong>{messageObj.id} : {messageObj.message}</strong>
      </p>
  </div>
  );
}; 
```

新的属性丢失了。

试试这里的。

您可以使用函数参数和对象扩展语法来复制这种行为:

```
onChange={e => {
  const val = e.target.value;
  setMessage(prevState => {
    return { ...prevState, message: val }
  });
}} 
```

这将产生与[对象相同的结果...prevState 部分将获取对象的所有属性，而 message: val 部分将覆盖 message 属性。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)

出于这个原因，React 文档建议[根据哪些值倾向于一起改变来将状态分成多个状态变量](https://reactjs.org/docs/hooks-faq.html#should-i-use-one-or-many-state-variables)。

### 使用状态挂钩的规则

useState 遵循与所有钩子相同的[规则](https://reactjs.org/docs/hooks-rules.html):

*   只调用顶层的钩子
*   仅从 React 函数调用挂钩

第二条规则很容易遵循。不要在类组件中使用 use state:

```
class App extends React.Component {
  render() {
    const [message, setMessage] = useState( '' );

    return (
      <p>
        <strong>{message}</strong>
      </p>
    );
  }
} 
```

或者常规的 JavaScript 函数(不在功能组件内部调用):

```
function getState() {
  const messageState = useState( '' );
  return messageState;
}
const [message, setMessage] = getState();
const Message = () => {
 /* ... */
} 
```

您将得到一个[错误](https://reactjs.org/warnings/invalid-hook-call-warning.html)。

第一条规则意味着，即使在函数组件内部，也不应该在循环、条件或嵌套函数中调用 useState，因为 React 依赖于调用 useState 函数的顺序来获得特定状态变量的正确值。

在这方面，最常见的错误是将 useState 调用或 update 函数调用包装在条件语句中(它们不会一直被执行):

```
const [message, setMessage] = useState( '' );
const [list, setList] = useState( [] );
if (condition) {
  setMessage( aMessage );  // Sometimes it will be executed, making the order change
}
setList( [1, 2, 3] ); 
```

一个功能组件可以有许多对 useState 或其他钩子的调用。每个钩子都存储在一个列表中，有一个变量跟踪当前执行的钩子。

当执行 useState 时，读取当前钩子的状态(或在第一次渲染时初始化)，然后，将变量改为指向下一个钩子。这就是为什么始终保持钩子调用的顺序是重要的，否则，可能会返回属于另一个状态变量的值。

总的来说，下面是一个逐步实现的例子:

1.  React 初始化钩子列表和跟踪当前钩子的变量
2.  React 首次调用您的组件
3.  React 找到对 useState 的调用，创建一个新的钩子对象(带有初始状态)，将当前钩子变量更改为指向该对象，将该对象添加到钩子列表，并返回带有初始状态的数组和更新它的函数
4.  React 找到对 useState 的另一个调用，并重复上一步的操作，存储一个新的钩子对象并更改当前的钩子变量
5.  组件状态发生变化
6.  React 将状态更新操作(由 useState 返回的函数执行)发送到要处理的队列
7.  React 确定它需要重新呈现组件
8.  React 重置当前钩子变量并调用你的组件
9.  React 查找对 useState 的调用，但是这一次，因为在钩子列表的第一个位置已经有一个钩子，所以它只改变当前的钩子变量，并返回具有当前状态的数组和更新它的函数
10.  React 找到对 useState 的另一个调用，由于第二个位置存在一个钩子，所以它再次只更改当前钩子变量，并返回带有当前状态的数组和更新它的函数

如果你喜欢读代码， [ReactFiberHooks](https://github.com/facebook/react/blob/fd557d453d37eab29eca18f0507750ab2093669d/packages/react-reconciler/src/ReactFiberHooks.js) 是一个你可以学习钩子如何工作的类。

### 结论

useState 是一个钩子(函数),允许你在函数组件中拥有状态变量。您将初始状态传递给这个函数，它返回一个包含当前状态值(不一定是初始状态)的变量和另一个更新该值的函数。

需要记住的要点是:

*   更新函数不会立即更新值
*   如果使用前一个值来更新状态，则必须传递一个接收前一个值并返回更新值的函数，例如 set message(previous val = > previous val+current val)
*   如果使用与当前状态相同的值(React 使用[对象. is](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is#Description) 进行比较)来更新状态，React 不会触发重新渲染
*   与类组件中的 this.setState 不同，当状态更新时，useState 不合并对象。它取代了它们
*   useState 遵循与所有钩子相同的规则。特别要注意这些函数被调用的顺序(有一个 [ESLint 插件](https://www.npmjs.com/package/eslint-plugin-react-hooks)会帮助你执行这些规则)

对于更高级的情况，可以使用 [useReducer](https://reactjs.org/docs/hooks-reference.html#usereducer) 钩子。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子【React 中的使用状态指南】([https://Blog . log rocket . com/A-guide-to-use state-in-React-ECB 9952 e 406 c/](https://blog.logrocket.com/a-guide-to-usestate-in-react-ecb9952e406c/)最先出现在 [LogRocket 博客](https://blog.logrocket.com)上。