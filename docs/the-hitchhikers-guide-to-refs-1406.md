# 参考文献指南

> 原文：<https://dev.to/selbekk/the-hitchhikers-guide-to-refs-1406>

React 有一个叫做 refs 的特性。ref 就是 React 文档中所说的“出口”，它让你可以与东西的实例进行交互。它们应该谨慎使用，但有时会非常有用。

这篇文章将引导你了解 React 中有时令人困惑的引用世界，以及你应该如何、何时以及为什么使用它们。我们将介绍什么是裁判，创建裁判的不同方法，使用裁判，以及裁判何时是你的挑战的正确选择。我们走吧！💥

## 什么是 ref？

ref——引用的简称——是引用的一种方式...嗯，有些事。通常情况下，这是一个 DOM 节点或一个类组件。更准确地说，**ref 是对节点或组件**的实例的引用。

对于 DOM 节点，您可以访问它的 JavaScript API。就像你自己用 JavaScript 创建的一样，就像这样:

```
const buttonRef = document.createElement('button'); 
```

Enter fullscreen mode Exit fullscreen mode

这意味着你可以访问大量的命令式 API，比如`.focus()`或`.click()`

当您添加对类组件引用时，您可以访问它的实例。这意味着如果需要，您可以调用它的所有实例方法。如果您需要从子组件中获取一些状态，或者触发某种副作用，而传递一个已更改的属性不容易触发这种副作用，这可能会很有用。

## 如何创建 ref？

好了，我们已经了解了什么是 ref，但是我们如何使用它呢？事实证明有几种方法。让我们开始吧！

### React.createRef()

创建 ref 最简单的方法是使用 React 提供给我们的 API。通过调用`React.createRef()`，我们接收到一个 ref，我们可以将它放在任何我们想要的地方:

```
function App() {
  const inputRef = React.createRef();
  return (
    <>
      <input ref={inputRef} />
      <button onClick={() => inputRef.current.focus()}>
        Click to focus
      </button>
    </>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们通过将引用传递给`ref` prop 将它放在输入字段上，然后我们从按钮的 click 处理程序中调用它。如果您单击该按钮，输入字段将被聚焦。也许这不是你每天都要做的事情，但你已经明白了要点。

> #### [T1。当前什么？](#current-what)
> 
> 当您调用`React.createRef()`时，您会得到一个具有单一属性`current`的对象。为什么我们需要这个看起来令人讨厌的中间人——我们不能直接分配我们想要的吗？
> 
> 原来，这是有原因的！如果我们直接创建引用，React 将无法更新这个引用——我们必须从头开始创建一个新的引用。不过，通过拥有这个可变对象属性，React *可以*就地更新它，而不必再次重新创建它。你可以看看[这期](https://github.com/facebook/react/issues/12588)更好的解释。

`React.createRef()`是 React 的一个相当新的添加(它是在 16.3.0 中添加的)。添加它是为了简化整个引用过程。如果你想深入了解当初为什么添加它，请看一下 [RFC](https://github.com/reactjs/rfcs/blob/master/text/0017-new-create-ref.md#basic-example) 。

## `useRef`

`useRef`是一个钩子，因此只在函数组件中起作用。这并不意味着它不是一个伟大的工具！事实上，这些天我用它来写 99 %的参考文献。

当你调用`useRef`时，你会得到一个带有可变`current`值的不可变对象实例，比如`React.createRef()`。如果你愿意，可以传入一个初始值。下面举个例子！

```
function App() {
  const inputRef = React.useRef(null);
  return (
    <>
      <input ref={inputRef} />
      <button onClick={() => inputRef.current.focus()}>
        Click to focus
      </button>
    </>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

### 回调引用

在 React 16.3 问世之前，创建引用的首选方式是我们称之为“回调引用”的方式。基本上，它让您负责在某个地方创建和存储引用。看起来是这样的:

```
class App extends React.Component {
  render() {
    return (
      <>
        <input ref={ref => this.inputRef = ref} />
        <button onClick={() => this.inputRef.focus()}>
          Click to focus
        </button>
      </>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这也很好，但是由于一些原因，理解起来有点笨拙。首先，现在我们必须决定在哪里保存我的引用。第二，内联函数本身有一个[警告](https://reactjs.org/docs/refs-and-the-dom.html#caveats-with-callback-refs)。如果回调函数是内联的(如上)，它将被调用两次——一次是使用参数`ref`作为参数`null`,另一次是将其设置为实际实例。

您今天仍然可以使用这种方法，但是在您需要它所提供的灵活性时再使用它。例如，如果你动态地创建引用，这可能是一个用例。

### 弦参(复古)

如果你在一个旧的代码库上工作，你可能会遇到这样的代码:

```
class App extends React.Component {
  render() {
    return (
      <>
        <input ref="input" />
        <button onClick={() => this.refs.input.focus()}>
          Click to focus
        </button>
      </>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法被称为“字符串引用”，并计划在未来的某个时候被弃用。这个 API 很好也很简单，但是也有一些缺点。

如果您在代码库中看到它们，那么重构它们，使其看起来像前面的例子，或者使用`React.createRef()`为您创建 ref。你未来的自己会感谢你的！

## 何时使用参考文献(以及何时避免参考文献)

正如我在介绍中提到的，React 将 refs 称为“逃生出口”。为什么会这样？要回答这个问题，我们需要记住 React 是声明性的。

> ### 陈述句 vs 祈使句
> 
> 我总是忘记什么是什么，所以这里有一个快速的经验法则给那些和我想法一样的人:
> 
> 声明性代码告诉你你想要什么。命令式代码告诉你如何到达那里。
> 
> React 是声明性的，你可以写你想要的东西(一个带有“large”类的 H2)，而不是指定如何发生。声明性代码规则！

在一个完美的世界里，一切都是声明性的——但是网络不是那样构建的。我可以补充一点，理由很充分。相反，我们有时需要“逃离”到命令的世界。一些例子是:

*   聚焦输入(是的是的，我们已经讨论过了)
*   从其他库(如 jQuery)访问命令式 API
*   访问 DOM APIs(语音识别、动画等等)
*   调用子组件上的功能

对于所有这些用例，我们通过创建对它们的引用来获得命令式访问。一旦我们有了这些，我们就可以去镇上，整天呼唤方法和命令。

### 参应是规则的例外

尽管有一些真正的引用用例——它们不是你每次想在应用程序中发生什么事情时都应该抓住的东西。通常，您想要做的是一个小的重构，提升一些状态或者在命令式 API 上创建一个声明性的抽象。

换句话说，首先尝试在没有裁判的情况下解决你的挑战。如果你看不到一个好的解决方案，那么考虑一个参考。

## 转发参考

`ref`是 React 中的特殊道具。像`key`一样，它不是作为传递给组件的`props`散列的一部分传递的。相反，它是由 React“捕获”的，从来没有真正暴露给被引用的组件。

现在，十有八九，这就是你想要的。但是，如果您正在创建一个可重用的按钮或输入组件，那么传递的`ref` prop 可能意味着实际的 DOM 字段。

回到过去，你必须创建一个新的道具(`inputRef`或`domRef`或诸如此类的东西)，然后将这个道具应用到你的 DOM 节点，就像这样:

```
function InputField(props) {
  return <input ref={props.inputRef} />; } 
```

Enter fullscreen mode Exit fullscreen mode

这将导致许多令人困惑的 API！现在，我们有`React.forwardRef` :

```
React.forwardRef((props, ref) => {
  return <input ref={ref} {...props} />;
}); 
```

Enter fullscreen mode Exit fullscreen mode

这将只是转发任何发送到正确位置的`ref`！

这不是一个你会经常用到的 API 但是知道它的存在是一件很棒的事情。你可以在[文档](https://reactjs.org/docs/forwarding-refs.html)中阅读更多关于`forwardRef`的内容。

## 改变我们对裁判的看法

直到最近，`refs`还在谈论我们到目前为止所谈论的内容——对 DOM 节点或类组件实例的引用。然而，随着钩子的引入，出现了`useRef`——它改变了一切。又来了。

正如您之前看到的，您可以以类似于函数组件中的`React.createRef`的方式使用`useRef`来创建引用。然而，`useRef`并不仅仅局限于事物的实例！

事实上，`useRef`适用于任何值、实例、函数或任何你想在渲染之间保留的东西。把它想象成函数组件的“实例变量”。

这里有一个例子。我经常在我的应用程序中创建一个`InputGroup`组件来自动为我的输入字段创建一个 UUID id，就像这样:

```
import uuid from 'uuid/v4';

class InputGroup extends React.Component {
  this.id = `input-${uuid()}`;
  render() {
    return (
      <div>
        <label htmlFor={this.id}>{this.props.label}</label>
        {children({ id: this.id })}
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里不得不使用一个类组件很烦人——我没有使用任何花哨的 React 特性！让我们将其重构为一个函数组件:

```
import uuid from 'uuid/v4';

function InputGroup(props) {
  const id = useRef(uuid());
  return (
    <div>
      <label htmlFor={id}>{props.label}</label>
      {children({ id })}
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

这非常好——我现在可以在对我的函数组件的调用之间共享值了！我建议你去查看一下`useRef` 的官方文档——里面有一些很好的例子可以借鉴。

> 这有点偏离了 refs 的主题——但是为了完整起见，我把它包括进来了。因为您可以用同样的方式创建 DOM refs 这是一个很好的技巧。

## 结论

Refs 是 React 工具箱中的一个很好的工具。当您需要触发一些命令性的 DOM API，或者您需要访问类组件的实例时，它们是完美的。您应该**谨慎使用 refs】，并且只有在出于某种原因需要访问命令式 API 时才使用。考虑提升状态，而不是引用类组件实例。**

创建 refs 的方法有很多，但最简单的是用`React.useRef`创建函数组件，或者用`React.createRef`创建任何组件。您可能会在遗留代码库中偶然发现回调引用或字符串引用，但是新代码不应该在没有充分理由的情况下使用它们。

最后，`useRef`让我们不仅创建对 DOM 节点和组件实例的引用，还创建对任何值、函数或组件的引用。

## 想要更多素材？

这里有几个有用的链接，可以链接到描述与上述内容相同的其他内容，以及其他词语和粒度:

*   [参考文献上的官方文件](https://reactjs.org/docs/refs-and-the-dom.html)
*   [CSS Tricks 关于裁判的文章](https://css-tricks.com/working-with-refs-in-react/)
*   [HackerNoon 关于裁判的文章(2017 年起)](https://hackernoon.com/refs-in-react-all-you-need-to-know-fb9c9e2aeb81)
*   [参考文献简介](https://medium.com/@mrewusi/a-gentle-introduction-to-refs-in-react-f407101a5ea6)