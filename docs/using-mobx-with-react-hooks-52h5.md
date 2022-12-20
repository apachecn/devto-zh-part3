# 将 MobX 与 React 挂钩一起使用

> 原文：<https://dev.to/ryands17/using-mobx-with-react-hooks-52h5>

这篇文章假设读者熟悉 MobX。

#### 钩子彻底改变了我们在 React 中编写功能组件的方式。

这个提议给我们编写功能组件的方式带来了巨大的变化。结合像 [MobX](https://mobx.js.org/) 这样的状态管理库，管理你的应用程序状态变得太容易了，再也不用编写类组件了。

对于 React，我们通过`mobx-react`包获得官方绑定。但是对于钩子，我们需要使用另一个库 [mobx-react-lite](https://github.com/mobxjs/mobx-react-lite) 。这给了我们定制的钩子，我们可以用它直接在我们的组件中创建可观察的东西。

一个简单的例子如下:
[https://codesandbox.io/embed/8y05z5p4q8](https://codesandbox.io/embed/8y05z5p4q8)

这里，钩子`useObservable`给了我们一种在一个对象中创建可观察对象、动作和计算属性的新方法。可以在这个对象上访问所需的值，组件通过`observer`包装器对更改做出反应。

现在您可能想知道，如果我有需要跨组件共享的状态怎么办？`mobx-react-lite`不包括`Provider`,但是我们不再需要它，因为 React 已经有了那个模式...语境！

上下文是我们可以跨多个组件共享状态的方式，而不需要像提升状态或道具钻取这样的技术。React 现在提供了一个名为`useContext`的钩子，可以帮助我们在多个组件之间共享我们的 MobX 存储。

下面是同一个例子，其特点是经典的 MobX store 被定义为一个类，并通过上下文使用。

[https://codesandbox.io/embed/3kmkj56qm](https://codesandbox.io/embed/3kmkj56qm)

如果您查看`TodoStore.js`文件，您可以看到这就是通常定义 MobX 存储的方式。一个上下文是从这个商店的一个实例中创建的，现在可以在多个组件之间共享。轻松点。

关于如何测试这些组件，我将写一篇后续文章。感谢阅读！