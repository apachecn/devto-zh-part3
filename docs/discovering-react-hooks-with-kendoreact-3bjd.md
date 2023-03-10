# 用 KendoReact 发现 React 挂钩

> 原文：<https://dev.to/progresstelerik/discovering-react-hooks-with-kendoreact-3bjd>

自从 React 16.8 发布以来，React 挂钩就可以使用了。我们将学习如何通过使用我们的 KendoReact 组件开始应用这些钩子。

随着 Hooks 的发布，在 React 中构建应用程序和管理其组件的本地状态现在变得更加容易、更加直接和简洁。从概念的角度来看，有很多关于钩子的知识需要了解，我们在 Progress Telerik 博客上有很多文章可以帮助你熟悉它们——我在下面重点介绍了其中的几篇。在这篇文章中，我们将学习钩子以及如何使用 [KendoReact](https://www.telerik.com/kendo-react-ui) 组件来应用它们。

构建 React 应用程序并不总是轻而易举的事情——在 Hooks 之前，您必须了解 React 中的许多模式和实践，才能完成日常工作，如状态管理、分离组件中的逻辑、确保您可以跨组件共享生命周期方法等。它需要了解几种不同的技术，如混合、高阶组件和/或渲染道具，这些通常只能用类来完成。

在 Hooks 做出反应之前，开发人员在面对不断增长的状态问题时，会使用 Redux 或 MobX 来管理他们的数据和通信状态。这是 Redux 的一个自然用法，但是也有其他形式的应用程序状态，使用 Redux 可能不是一个好的选择，比如使用`this.state`和`setState`的基于类的组件内部的状态。`setState`是 React 中提供的一种方法，允许用户定义和随时间改变状态。这种能力过去只在类组件中可用，直到钩子出现。

下面是我们博客上的一些文章，更详细地解释了钩子:

*   [问好创建 React 应用程序(2/3)](https://dev.to/progresstelerik/hello-create-react-app-20-316-temp-slug-3014493) 帮助开始使用 React
*   [学习基本的反应钩子状态和效果](https://dev.to/progresstelerik/how-to-use-basic-react-hooks-for-state-and-effects-4bld)了解局部状态和效果
*   [学习上下文的基本 React 挂钩](https://dev.to/progresstelerik/how-to-use-basic-react-hooks-for-context-45pp-temp-slug-9116858)
*   [学习减速器的基本反应钩](https://dev.to/progresstelerik/how-to-use-basic-react-hooks-for-reducers-4nfo-temp-slug-1109404)
*   [学习创建定制的 React 挂钩](https://dev.to/progresstelerik/everything-you-need-to-create-a-custom-react-hook-47kl)

关于钩子的 ReactJS.org 文档也是你应该知道的一个很好的资源。

正如我之前所说的，钩子对于处理某些类型的应用程序状态非常有用。几个例子是控制状态、本地组件状态和会话状态。我想在使用我们的 KendoReact 组件时利用钩子，但是我想从简单的开始。让我们从一个使用类的演示中重构一个 StackBlitz 演示，并将其切换到使用功能组件。

我们将寻找演示使用`this.state`和`this.setState`的实例，因为当我们将组件转换为函数时，我们将不再需要使用`this`关键字，我们也不需要使用构造函数或调用`setState`。当我们使用钩子工作时，我们做事情的方式略有不同。所以让我们开始重构 KendoReact 演示，它展示了如何使用我们的 [KendoReact 对话框](https://www.telerik.com/kendo-react-ui/components/dialogs/dialog)。我已经[从对话框总览页面分叉出原始的 StackBlitz 演示](https://stackblitz.com/edit/kendoreact-dialog-class-based?file=app/main.jsx)，这将是我们的起点。

如果你看一下我在下面展示的这个演示的`main.jsx`页面，我们可以发现在使用功能组件和 React 钩子时，有几个目标区域会发生变化。用绿色突出显示的代码和行需要修改，用红色突出显示的行可以完全删除。

[![refactor_highlights](img/cd27f8bc9482ff50e96fe7a3a8244a40.png "refactor\_highlights")](https://d585tldpucybw.cloudfront.net/sfimages/default-source/blogs/2019/2019-05/refactor_highlights.png?sfvrsn=8f624148_0)

1.  在第 6 行我们有一个`class`定义，我们需要将它转换成一个功能组件。
2.  在第 7 行我们有一个构造函数，在第 8 行有一个对`super()`的调用，在第 10 行我们有一些绑定。这些在我们使用钩子的功能组件中都是不需要的。
3.  在第 9 行，我们创建了状态的一个实例，并给它一个默认值`true`,这将是一个对`useState`钩子的调用。
4.  在第 13 行，我们需要重命名`toggleDialog`函数，并将其切换到 ES6 Arrow 函数风格语法，第 14 行到第 16 行将简单地调用由我们的`useState()`赋值提供的更新方法`setVisible`，并且它将引用的值将是`visible`而不是`this.state.visible`。
5.  在第 19 行，我们有一个对`render()`的调用，这在我们的函数组件中是不必要的
6.  在第 22、23、26 和 27 行，我们有对`this.`和`this.state`的引用，需要引用`visible`和`toggleVisible`而不是`toggleDialog`，稍后我会解释为什么我要重命名这个函数。

让我们开始吧。我们要做的第一件事就是把类转换成一个功能组件，去掉构造函数，调用`toggleDialog()`函数的绑定`super()`。这里我们可以使用多种语法选项——我更喜欢 ES6 箭头函数风格:

```
const multiply = (x, y) => { return x * y }; 
```

在我们的组件中，第 5 行将如下所示:

```
const DialogWrapper = () => { 
```

让我们继续设置我们的钩子来代替状态对象。我们不会创建一个名为 state 的对象，而是设置一个对`useState()`的调用，并将其返回值析构为一个保存我们的状态的变量和一个更新/设置方法来更新该状态。我们的州名将是`visible`，它的更新方法将被称为`setVisible`。我们将基本上删除整个构造函数，并替换为下面这一行:

```
const [visible, setVisible] = useState(true); 
```

既然我们使用的是`useState()`基本钩子，我们也需要导入它。我们的 React 导入现在看起来像:

```
import React, { useState } from 'react'; 
```

接下来，我们需要这个组件中的一个函数来处理调用`setVisible`来切换它的值。我们决定将它命名为`toggleVisible`而不是`toggleDialog`,因为我们是在一个功能组件中，以前使用的语法将不起作用。为此，我将其更新为 ES6 箭头函数样式。

该功能将简单地将`visible`状态设置为与其当时的当前状态相反。在幕后，React 正在管理这个`visible`变量，就像你在一个类组件中调用`setState`一样。这种管理只是被称为`useReducer`的幕后事物抽象出来，但我们不打算在这个简单的例子中深入了解所有这些是如何工作的。我们的新代码如下所示:

```
const DialogWrapper = () => {;
  const [visible, setVisible] = useState(true);
  const toggleVisible = () => setVisible(!visible); 
```

现在我们需要去掉`render()`块和它的两个花括号。此外，我们需要删除对这个`this.toggleDialog`和`this.state.visible`的所有引用，并相应地将它们更改为`toggleVisible`和`visible`。现在，在我们的`return()`中，我们将有以下变化:

```
return (
  <div>
  <Button className="k-button" onClick={toggleVisible}>Open Dialog</Button>
  {visible && <Dialog title={"Please confirm"} onClose={toggleVisible}>
    <p style={{ margin: "25px", textAlign: "center" }}>Are you sure you want to continue?</p>
    <DialogActionsBar>
    <Button className="k-button" onClick={toggleVisible}>No</Button>
    <Button className="k-button" onClick={toggleVisible}>Yes</Button>
    </DialogActionsBar>
  </Dialog>}
  </div>
); 
```

同样，我们刚刚更新了`return()`中的代码，不再引用`this`关键字，而是使用新的函数名`toggleVisible`。

这些都是需要做出的改变。我们已经成功地将我们的 KendoReact 演示转换为使用一个功能组件和基本的`useState`钩子。让我们来看看使用名为 GitHistory 的强大工具后，我们的整体变化是什么样的:

[![](img/92b147861e397d317340b9e240a32660.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xoCWQb1Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/class-vs-functional-animation.gif%3Fsfvrsn%3D8a9c864d_0)

我在这里所做的是将原始的基于 StackBlitz 类的演示下载到它自己的 Git repo 中。基于类的版本将是第一次提交，然后在重构为我们所做的函数式钩子风格后，我进行了第二次提交。GitHistory 让我能够清理这些提交，并以动画的方式查看我们的`main.jsx`文件在这两次提交中是如何变化的。我认为对于学习如何使用钩子的人来说，这是一个强大的视觉效果，可以看到从旧的基于类的方法到基于函数的方法的变化。

我还把这个回购推送到我的 GitHub 账户，在那里你可以自己用 GitHistory 查看。 [GitHistory](https://dev.to/scottw/-git-history-5fpc-temp-slug-9335267) (由 [Rodrigo Pombo](https://github.com/pomber) 创建)是一个非常酷的插件，它允许你区分你的回购中的任何文件，并通过提交擦洗，以非常直观的方式查看文件如何随着时间的推移而改变。

这是一个停下来的好地方。我们学习了如何使用钩子将具有简单状态对象的类组件转换成功能组件。在这个博客系列的下一部分，我们将更深入地设置几个 [KendoReact](https://www.telerik.com/kendo-react-ui) 组件，这些组件有更多的基本钩子，加上一些像`useReducer`这样的高级钩子，并将我们的钩子技能带得更远。