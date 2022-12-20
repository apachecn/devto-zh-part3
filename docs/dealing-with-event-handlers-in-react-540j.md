# 在 React 中将回调作为道具来处理

> 原文：<https://dev.to/xnimorz/dealing-with-event-handlers-in-react-540j>

### TL；速度三角形定位法(dead reckoning)

1.  不要在一个地方混合 JSX 和业务逻辑，保持你的代码简单易懂。
2.  对于小的优化，您可以在类的类属性中缓存函数，或者对函数组件使用`useCallback`钩子。在这种情况下，纯组件不会在每次其父组件被重新渲染时都被重新渲染。特别是，当您将函数作为道具传递给 PureComponents 时，回调缓存可以有效地避免过多的更新周期。
3.  不要忘记，事件处理程序接收的是合成事件，而不是原始事件。如果退出当前函数范围，您将无法访问合成事件字段。如果你想得到函数范围之外的字段，你可以缓存你需要的字段。

### 第一部分。事件处理程序、缓存和代码可读性

React 有一种非常方便的方法来为 DOM 元素添加事件处理程序。这是初学者首先要面对的基本问题之一。

```
class MyComponent extends Component {
  render() {
    return <button onClick={() => console.log('Hello world!')}>Click me</button>;
  }
} 
```

这很容易，不是吗？当您看到这段代码时，不难理解当用户单击按钮时会发生什么。但是，如果事件处理程序中的代码量越来越多，我们该怎么办呢？
让我们假设，我们想要加载开发人员的列表，过滤他们`(user.team === 'search-team')`并使用他们点击按钮时的年龄进行排序:

```
class MyComponent extends Component {
  constructor(props) {
    super(props);
    this.state = { users: [] };
  }
  render() {
    return (
      <div>
        <ul>
          {this.state.users.map(user => (
            <li>{user.name}</li>
          ))}
        </ul>
        <button
          onClick={() => {
            console.log('Hello world!');
            window
              .fetch('/usersList')
              .then(result => result.json())
              .then(data => {
                const users = data
                  .filter(user => user.team === 'search-team')
                  .sort((a, b) => {
                    if (a.age > b.age) {
                      return 1;
                    }
                    if (a.age < b.age) {
                      return -1;
                    }
                    return 0;
                  });
                this.setState({
                  users: users,
                });
              });
          }}
        >
          Load users
        </button>
      </div>
    );
  }
} 
```

这个代码太复杂了。业务逻辑部分混合了 JSX 元素。避免这种情况的最简单方法是将函数移到类属性中:

```
class MyComponent extends Component {
  fetchUsers() {
    // Move business-logic code here
  }
  render() {
    return (
      <div>
        <ul>
          {this.state.users.map(user => (
            <li>{user.name}</li>
          ))}
        </ul>
        <button onClick={() => this.fetchUsers()}>Load users</button>
      </div>
    );
  }
} 
```

我们将业务逻辑从 JSX 代码移到了我们班上的独立字段。业务逻辑代码需要访问`this`，所以我们将回调设为:`onClick={() => this.fetchUsers()}`

除此之外，我们可以将`fetchUsers`类字段声明为一个箭头函数:

```
class MyComponent extends Component {
  fetchUsers = () => {
    // Move business-logic code here
  };
  render() {
    return (
      <div>
        <ul>
          {this.state.users.map(user => (
            <li>{user.name}</li>
          ))}
        </ul>
        <button onClick={this.fetchUsers}>Load users</button>
      </div>
    );
  }
} 
```

它允许我们将回调声明为`onClick={this.fetchUsers}`

两者有什么区别？

当我们将 callback 声明为`onClick={this.fetchUsers}`时，每个呈现调用都将传递相同的 onClick 引用给`button`。
当时，当我们使用`onClick={() => this.fetchUsers()}`时，每个渲染调用将初始化新函数`() => this.fetchUsers()`，并将它传递给`button` onClick prop。这意味着`nextProp.onClick`和`prop.onClick`不相等，即使我们使用纯组件而不是`button`它也会被重新渲染。

在发展过程中我们会受到哪些负面影响？

在绝大多数情况下，我们不会发现任何视觉性能问题，因为虚拟 DOM 没有任何变化，也没有任何物理上的重新呈现。然而，如果我们渲染大型组件列表，我们可能会发现大量数据的滞后。

#### 为什么理解函数如何传递给道具很重要？

你经常可以在 Twitter 或 StackOverflow 上找到这样的建议:

如果您在 React 应用程序中遇到性能问题，请尝试将问题位置的继承从 Component 更改为 PureComponent，或者定义 shouldComponentUpdate 以消除过多的更新周期。

如果我们将一个组件定义为 PureComponent，这意味着它已经有了`shouldComponentUpdate`函数，该函数在它的`props`和`nextProps`之间实现了 shallowEqual。

如果我们在更新生命周期中设置新的引用作为 PureComponent 的道具，我们将失去 PureComponent 的所有优势和优化。

我们来看一个例子。我们实现了输入组件，它将显示一个代表其更新次数的计数器

```
class Input extends PureComponent {
  renderedCount = 0;
  render() {
    this.renderedCount++;
    return (
      <div>
        <input onChange={this.props.onChange} />
        <p>Input component was rerendered {this.renderedCount} times</p>
      </div>
    );
  }
} 
```

现在我们创建两个组件，它们将呈现`Input`组件:

```
class A extends Component {
  state = { value: '' };
  onChange = e => {
    this.setState({ value: e.target.value });
  };
  render() {
    return (
      <div>
        <Input onChange={this.onChange} />
        <p>The value is: {this.state.value} </p>
      </div>
    );
  }
} 
```

第二:

```
class B extends Component {
  state = { value: '' };
  onChange(e) {
    this.setState({ value: e.target.value });
  }
  render() {
    return (
      <div>
        <Input onChange={e => this.onChange(e)} />
        <p>The value is: {this.state.value} </p>
      </div>
    );
  }
} 
```

你可以试试这里的例子:[https://codesandbox.io/s/2vwz6kjjkr](https://codesandbox.io/s/2vwz6kjjkr)
这个例子展示了如果我们每次在渲染中设置对 PureComponent 的新引用，我们会失去 pure component 的所有优势。

### 第二部分。在函数组件中使用事件处理程序

新的 React hooks 机制在`React@16.8`([https://reactjs.org/docs/hooks-intro.html](https://reactjs.org/docs/hooks-intro.html))的新版本中公布。它允许实现全功能的功能组件，用钩子构建完整的生命周期。使用这个特性，您可以将几乎所有的类组件更改为函数。(但这不是必须的)

让我们将`Input`组件从类重写为函数。

输入应该存储关于它被重新渲染了多少次的信息。有了类，我们可以通过关键字`this`使用实例字段。但是对于函数，我们不能用`this`声明一个变量。React 提供了`useRef`钩子，我们可以用它在 DOM 树中存储对 HtmlElement 的引用。此外,`useRef`可以方便地存储任何可变数据，比如类中的实例字段:

```
import React, { useRef } from 'react';

export default function Input({ onChange }) {
  const componentRerenderedTimes = useRef(0);
  componentRerenderedTimes.current++;

  return (
    <>
      <input onChange={onChange} />
      <p>Input component was rerendered {componentRerenderedTimes.current} times</p>
    </>
  );
} 
```

我们创建了组件，但它现在还不是`PureComponent`。我们可以添加一个库，这给了我们一个用 PureComponent 包装组件的方法，但是最好使用已经在 React 中出现的`memo`函数。它工作更快、更有效:

```
import React, { useRef, memo } from 'react';

export default memo(function Input({ onChange }) {
  const componentRerenderedTimes = useRef(0);
  componentRerenderedTimes.current++;

  return (
    <>
      <input onChange={onChange} />
      <p>Input component was rerendered {componentRerenderedTimes.current} times</p>
    </>
  );
}); 
```

我们的`Input`组件准备好了。现在我们将重写 A 和 B 组件。
我们可以很容易地重写`B`组件:

```
import React, { useState } from 'react';
function B() {
  const [value, setValue] = useState('');

  return (
    <div>
      <Input onChange={e => setValue(e.target.value)} />
      <p>The value is: {value} </p>
    </div>
  );
} 
```

我们使用了`useState` hook，它处理组件状态。它接收状态的初始值，并返回包含 2 项的数组:当前状态和设置新状态的函数。您可以在组件中调用几个`useState`钩子，它们中的每一个都将负责实例状态中自己的部分。

我们如何缓存回调？我们不能将它从组件代码中移走，因为它对于所有不同的组件实例都是通用的。对于这类问题，React 有专门的缓存和记忆挂钩。对我们来说最方便的钩子是 https://reactjs.org/docs/hooks-reference.html 的`useCallback`

所以，`A`的分量是:

```
import React, { useState, useCallback } from 'react';
function A() {
  const [value, setValue] = useState('');

  const onChange = useCallback(e => setValue(e.target.value), []);

  return (
    <div>
      <Input onChange={onChange} />
      <p>The value is: {value} </p>
    </div>
  );
} 
```

我们缓存了函数，这样`Input`组件就不会在每次其父组件重新渲染时重新渲染。

**`useCallback`如何工作？**

这个钩子返回函数的记忆版本。(这意味着引用不会在每次渲染调用时改变)。除了将被记忆的函数之外，这个钩子接受第二个参数。在我们的例子中，它是一个空数组。
第二个参数允许向钩子传递依赖列表。如果至少有一个字段发生了变化，钩子将返回一个带有新引用的函数的新版本，以强制组件正确工作。

内联回调和记忆化回调的区别你可以在这里看到:[https://codesandbox.io/s/0y7wm3pp1w](https://codesandbox.io/s/0y7wm3pp1w)

为什么需要依赖关系数组？

让我们假设，我们必须缓存一个函数，这个函数通过闭包依赖于某个值:

```
import React, { useCallback } from 'react';
import ReactDOM from 'react-dom';

import './styles.css';

function App({ a, text }) {
  const onClick = useCallback(e => alert(a), [
    /*a*/
  ]);

  return <button onClick={onClick}>{text}</button>; }
const rootElement = document.getElementById('root');
ReactDOM.render(<App text={'Click me'} a={1} />, rootElement); 
```

组件 App 依赖`a`道具。如果我们执行这个例子，一切都会正常工作。但是，当我们添加到最终重新渲染时，组件的行为将会不正确:

```
setTimeout(() => ReactDOM.render(<App text={'Next A'} a={2} />, rootElement), 5000); 
```

当超时执行时，点击按钮将显示`1`而不是`2`。这样做是因为我们缓存了之前渲染的函数，用之前的`a`变量做了闭包。这里重要的是，当父对象被重新渲染时，React 将创建一个新的道具对象，而不是改变现有的道具对象。
如果我们取消注释`/*a*/`，我们的代码将正确运行。当组件第二次重新呈现时，React hook 将检查 deps 中的数据是否已被更改，并将返回新函数(带有新引用)。

你可以试试这个例子:[https://codesandbox.io/s/6vo8jny1ln](https://codesandbox.io/s/6vo8jny1ln)

React 有许多函数，允许记忆数据:`useRef`、`useCallback`和`useMemo`。
最后一个和`useCallback`类似，但是用来记忆数据比记忆函数更方便。`useRef`既适合缓存对 DOM 元素的引用，也适合用作实例字段。

乍一看，`useRef` hook 可以用来缓存函数。它类似于存储方法的实例字段。然而，它不方便用于功能记忆。如果我们的记忆化函数使用了闭包，并且值在两次呈现之间发生了变化，那么函数将使用第一次呈现(缓存的)。这意味着我们必须手动更改对内存化函数的引用，或者只使用`useCallback`钩子。

https://codesandbox.io/s/p70pprpvvx[——下面是正确的`useCallback`用法和错误的`useRef`用法的例子。](https://codesandbox.io/s/p70pprpvvx)

### 第三部分。合成事件

我们已经看到了如何使用事件处理程序，如何在回调中使用闭包，但是 React 在事件处理程序中的事件对象上也有所不同。

看一下`Input`组件。它同步工作。但是，在某些情况下，您可能希望实现去抖或节流模式。去抖模式对于搜索字段来说非常方便，当用户停止输入符号时，你可以强制搜索。

让我们创建一个组件，它将调用 setState:

```
function SearchInput() {
  const [value, setValue] = useState('');

  const timerHandler = useRef();

  return (
    <>
      <input
        defaultValue={value}
        onChange={e => {
          clearTimeout(timerHandler.current);
          timerHandler.current = setTimeout(() => {
            setValue(e.target.value);
          }, 300); // wait, if user is still writing his query
        }}
      />
      <p>Search value is {value}</p>
    </>
  );
} 
```

这个代码不行。React 代理事件，并在同步回调后，React 清理事件对象以重用它，从而实现优化。所以我们的`onChange`回调接收合成事件，这将被清除。

如果我们以后想使用`e.target.value`，我们必须在异步代码段之前缓存它:

```
function SearchInput() {
  const [value, setValue] = useState('');

  const timerHandler = useRef();

  return (
    <>
      <input
        defaultValue={value}
        onChange={e => {
          clearTimeout(timerHandler.current);
          const pendingValue = e.target.value; // cached!
          timerHandler.current = setTimeout(() => {
            setValue(pendingValue);
          }, 300); // wait, if user is still writing his query
        }}
      />
      <p>Search value is {value}</p>
    </>
  );
} 
```

例子:[https://codesandbox.io/s/oj6p8opq0z](https://codesandbox.io/s/oj6p8opq0z)

如果必须缓存整个事件实例，可以调用`event.persist()`。该函数从 React 事件池中移除您的合成事件实例。但在我自己的工作中，我从未遇到过这种情况。

### 结论:

React 事件处理程序非常方便，因为它们

1.  自动实现订阅和退订
2.  简化我们的代码可读性

尽管有几点你应该记住:

1.  道具中的回调重定义
2.  合成事件

回调重定义通常不会对视觉性能产生大的影响，因为 DOM 没有改变。但是如果你面临性能问题，现在你正在改变组件到`Pure`或`memo`注意回调记忆，否则你将失去来自`PureComponents`的任何利润。您可以对类组件使用实例字段，或者对函数组件使用`useCallback`钩子。