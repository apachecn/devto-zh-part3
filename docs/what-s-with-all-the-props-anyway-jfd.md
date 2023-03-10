# 这些道具是怎么回事？

> 原文：<https://dev.to/laurieontech/what-s-with-all-the-props-anyway-jfd>

欢迎回到样板基础！今天我将深入 React 中的组件创建。

在 React 中，我们的页面由组件组成。这些是自包含的可重用代码片段，我们可以在整个应用程序中包含它们。

这是用于定义组件的语法示例。

```
class App extends Component {
  constructor(props) {
    super(props)
  }
  render() { `
    return (
      <h1>My fun post!</h1>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# ES6 和类

首先，重要的是要知道 React 一直使用类的概念进行操作。这很有趣，因为在 ES6 之前，ECMAScript 标准不支持类的概念。

> 如果你不熟悉 ECMAScript、JavaScript 和框架之间的关系，可以看看这篇文章。
> 
> [![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## Javascript 的层次
> 
> ### 劳丽 3 月 26 日 193 分钟阅读
> 
> #javascript #learning #react](/laurieontech/the-layers-of-javascript-15op)

当时，React 使用了一种变通方法来定义类。类似这样的。

```
const MyComponent = React.createClass({
  render() {
    return <p>I am a component!</p>;
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

然而，当 ES6 出现时，它引入了 Javascript 固有的类概念。现在，组件可以通过在其上构建来定义。

```
import {Component} from "react"

class App extends Component {
    ....
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码所做的是采用 ES6 中的`class`概念，并使用 React 库定义的另一个类`Component`来扩展它。`Component`类是从顶层导入的。

> 如果您想了解关于这个导入语法的更多信息，可以在这里找到。
> 
> [![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 出口、进口和违约，天啊！
> 
> ### 劳丽 Apr 29 ' 19 分钟阅读
> 
> #javascript #react #webdev #beginners](/laurieontech/exports-and-imports-and-defaults-oh-my-fa3)

# 快撇开渲染不谈

事实证明，React 类组件唯一需要包含的是一个`render`方法。这个方法告诉你的应用程序这个组件实际上要在屏幕上显示什么。组件中定义的其他内容都是额外的。

然而，对于许多组件来说，仅仅一个渲染功能并不能支持开发人员所寻求的功能级别。这就是构造函数(和额外的用户定义函数)的用武之地。

# 构造函数？道具？

在我们的组件内部，我们有能力编写一个构造函数。

```
 constructor(props) {
    super(props)
  } 
```

Enter fullscreen mode Exit fullscreen mode

此代码片段可在 React 教程中找到，它不是由 cli 本身生成的。

*   `constructor`是定义创建该组件时发生什么的函数。更详细地说，这是一个 ES6 类给我们的特殊函数，它将是类初始化时调用的第一段代码。

*   `props`被传递到构造函数中，这样它的内容就可以在类内部被引用。在 React 中，组件就是这样传递信息的。

# 这个超级玩意儿有点诡异

好的，现在在我们的构造函数中有这行代码。

```
super(props) 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了一些事情。首先，当我们调用`constructor`函数时，我们将调用`super`。这将执行组件的设置代码，该代码存储在我们正在扩展的`Component`类的`constructor`函数中！

现在，如果我们将`props`传递给`super()`，我们就能够使用`this`来引用`constructor`函数中的`props`。React 建议将此作为默认的最佳实践。从技术上来说，如果你从来没有在构造函数中引用过`this.props`，那你就不需要这样做。但是，这是建议。

# 构件构造完成！

这就是一切。我们已经定义了一个新的 React 组件，并提供了正确初始化它所需的样板代码。我们现在可以传道具，写渲染代码等。

正如我喜欢说的，我们经常在框架和项目中看到一些我们没有花时间去理解的代码行。我鼓励你好奇地探索你看到的一切！这会让你成为更好的程序员。

密切关注即将到来的样板基础帖子！