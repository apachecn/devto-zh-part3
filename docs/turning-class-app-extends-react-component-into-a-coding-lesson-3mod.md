# 转`类 App 扩展 React。编码课程的组成部分

> 原文：<https://dev.to/carlmungazi/turning-class-app-extends-react-component-into-a-coding-lesson-3mod>

自从 2017 年拉开 [Mithril.js](https://mithril.js.org/) 的引擎盖，我就对框架和库架构产生了兴趣。探究源代码让我意识到，这些工具的作者使用了我日常使用的相同的语言特性，尽管是在更高级的水平上。在早期，当深入研究代码库时，我遵循一种非常被动的方法来阅读代码，在这里和那里插入断点，然后继续前进。这有它的好处，因为你可以通过阅读写得好的代码学到很多东西。然而，到了某个阶段，这种方法就会变得乏味或者变得更有活力。因此，在这篇短文中，我将分享一些我积极钻研源代码的方法。

这篇文章的背景是，它来自我做的这个[教程](https://blog.atulr.com/react-custom-renderer-1/)。完成后，我很好奇需要多少代码来确保`class App extends React.Component`工作。

```
class App extends React.Component {
  state = {
    text: Date.now()
  }

  onButtonClick = () => {
    this.setState(() => ({ text: Date.now() }))
  }

  render() {
    // ...
  }
}

ReactExperimentalRenderer.render(
  <App />, 
  document.getElementById('root')
); 
```

使用上面这个简单的应用程序，我开始了我的新冒险。以前，我会直接跳到代码中，但是我首先问自己:**当我写`class App extends React.Component`时，我扩展了什么类型的对象？**。在记下一些关于期望找到`setState`方法和引用`createElement`函数[将 JSX 变成反应元素](https://reactjs.org/docs/jsx-in-depth.html)的想法后，我一头扎了进去。

### 基础类

在名为 [`ReactBaseClasses`](https://github.com/facebook/react/blob/v16.8.4/packages/react/src/ReactBaseClasses.js) 的文件中你会发现下面的函数。原始函数有更多的注释，但我只留下了与本文相关的注释:

```
function Component (props, context, updater) {
  this.props = props;
  this.context = context;
  this.refs = {};
  // We initialize the default updater but the real one gets injected by the
  // renderer.
  this.updater = updater || ReactNoopUpdateQueue ;
}

Component.prototype.isReactComponent = {};

Component.prototype.setState = function(partialState, callback) {
  this.updater.enqueueSetState(this, partialState, callback, 'setState')
}

Component.prototype.forceUpdate = function(callback) {
  this.updater.enqueueForceUpdate(this, callback, 'forceUpdate');
}

//...

export {Component, PureComponent}; 
```

您可以使用此代码来列出一系列问题。我们的目的不是要创建一个详尽的列表，甚至不是要回答所有的问题。相反，要专注于学习如何提出好的问题。我想到的问题是:

1.  为什么`Component`是函数而不是 ES6 类？
2.  `this.setState`调用另一个函数，根据注释，这个函数是由渲染器注入的。这是如何发生的，更新程序是如何实现的？
3.  `function Component (props, context, updater) { /* ... */ }`是我们写`class App extends React.Component`时要扩展的。`extends`是如何工作的？
4.  哪个[优先规则](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md)适用于我们传递`this.updater.enqueueSetState`一个`this`绑定的方式？

### 为什么会有反应。组件是函数而不是 ES6 类。

我想不出这个问题的答案，所以我问了[堆栈溢出](https://stackoverflow.com/questions/55249313/why-is-the-internal-implementation-of-react-component-a-function-and-not-an-es6)。普遍的共识是，这样做是为了适应不支持 ES6 类的环境。我原以为会有更好的理由，但这些回答提醒我，你遇到的每一段代码都不必复杂。

### 什么是`updater`？

这个是更新器，[这个](https://github.com/facebook/react/blob/v16.8.4/packages/react-reconciler/src/ReactFiberClassComponent.js#L496)是它被设置的地方。与我们之前的问题不同，这个问题需要一些[背景](https://medium.com/@CarlMungazi/a-journey-through-reactdom-render-e8fc5edc11fd)。通常，每当我遇到兔子洞，我都会跳进去。然而，这并不总是有成效的，因为并不是每个兔子洞都需要调查。但是，您可以做的是粗略地看一下上述漏洞中的代码，并记下主题以供将来查询。

在这种情况下，你会遇到[链表](https://github.com/facebook/react/blob/v16.8.4/packages/react-reconciler/src/ReactUpdateQueue.js)。这可以引导你找到[解释](https://medium.com/react-in-depth/the-how-and-why-on-reacts-usage-of-linked-list-in-fiber-67f1014d0eb7) React 的[链表用法](https://dev.to/wuz/linked-lists-in-the-wild-react-hooks-3ep8)的文章。在研究像 [`ReactNoopUpdateQueue`](https://github.com/facebook/react/blob/v16.8.5/packages/react/src/ReactNoopUpdateQueue.js) 这样的 noop 函数的有用性时，你可能还会遇到[有趣的](https://stackoverflow.com/questions/2069345/what-real-purpose-does-noop-serve-in-jquery-1-4) [花絮](https://twitter.com/_ericelliott/status/652135250072461312?lang=en)。

### `extends`关键字是如何工作的？

简而言之，`extends`关键字用于[创建](http://exploringjs.com/es6/ch_classes.html#_subclassing) [子类](https://scotch.io/tutorials/better-javascript-with-es6-pt-ii-a-deep-dive-into-classes#toc-creating-subclasses-with-extends-calling-with-super)。在我们的例子中，`App`是`React.Component`的子类。React [创建`App`的一个实例](https://github.com/facebook/react/blob/v16.8.4/packages/react-reconciler/src/ReactFiberClassComponent.js#L555)，然后有趣的事情开始了。同样，问这样的问题会让你写出更优秀的作品。

### `this`在做什么？

我们的点击处理程序如下所示:

```
onButtonClick = () => {
  this.setState( () => ({ text: Date.now() }) )
} 
```

`setState`方法是这样的:

```
Component.prototype.setState = function(partialState, callback) {
  this.updater.enqueueSetState(this, partialState, callback, 'setState');
} 
```

并且`this.updater.enqueueSetState`这样:

```
function enqueueSetState(inst, payload, callback) {
  var fiber = get(inst);
  // ... 
} 
```

组件上的更新通过组件对应的纤程对象来管理。在调用`setState`时，`updater`属性(它是一个对象)已经存在于我们的`App`组件中，但是我们需要确保在`App`的上下文中调用`enqueueSetState`。幸运的是，传递给`enqueueSetState`的`this`上下文是我们的`App`组件，因此 React 使用它通过`get(inst)`调用获得对`App`的纤程对象的引用。

另外，注意在我们的`onButtonClick`方法中，我们将一个匿名函数作为第一个参数传递给`this.setState`。被`enqueueSetState`如何对待？[这个](https://github.com/facebook/react/blob/v16.8.5/packages/react-reconciler/src/ReactUpdateQueue.js#L393)是怎么来的:

```
partialState = _payload2.call(instance, prevState, nextProps) 
```

使用`.call`方法通过传入的第一个参数给 React 一个对`App`的引用。与之前使用`enqueueSetState`时`this`绑定更为隐含不同，使用`.call`使其更加明确。

### 接下来呢？

以这种方式询问源代码是提高编程技能的最佳方式之一。为什么好吧，让我们列出在上述过程中学到的一些东西:

*   考虑您的代码将在哪个环境中运行以及这将如何影响您选择的语言特性的重要性
*   链接列表的一个实际例子
*   Noop 函数
*   关于 ES6 课程的深入参考资料
*   隐式和显式绑定方式`this`

除了增长你的知识之外，阅读你经常使用的框架或库的源代码也有助于诸如调试或为开源做贡献之类的事情。例如，我对 React 源代码的第一次(也是唯一一次)提交被[错别字](https://github.com/facebook/react/commit/3494ee57e69f1f6cdf5e128dfbaa39db4dfd12db) [修复](https://github.com/facebook/react/pull/14576/commits/de432247d781dfda93a815aef9dd689fd42a7108)。