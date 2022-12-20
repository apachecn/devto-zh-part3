# 本周我学习了:如何在 React 中模仿渲染道具组件

> 原文：<https://dev.to/cmborchert/this-week-i-learned-how-to-mock-a-render-props-component-in-react-2il6>

*最初发布于[cborchert . blog](https://cborchert.blog/this-week-i-learned-how-to-mock-a-render-props-component-in-react)T3】*

## [T1】简介](#intro)

因此，正如标题所示，这篇小文章是关于如何模拟测试套件的渲染道具，尤其是当您在运行测试时遇到以下错误时:

> 警告:函数作为 React 子级无效。如果您返回一个组件而不是从 render 返回，可能会发生这种情况。或者你想调用这个函数而不是返回它。

## TL；速度三角形定位法(dead reckoning)

你的模仿需要支持像孩子一样的函数，或者你应该使用浅层渲染。最后，我用下面的代码替换了我的基本模拟代码:

```
jest.mock(
  './path/to/MyComponent', 
  ({ children, ...rest }) => (
    <mock-my-component {...rest}>
      {typeof children === 'function' ? '[Child as a function]' : children}
    </mock-my-component>
  ) 
```

## 注意事项

我想指出的是，这篇文章中的情况来自于一个未理想化的、现实生活中有几十年历史的代码库，有几十个人在为它工作。我可能会讨论做一些可能不是理想化的实践或者最前沿的事情，但是可能会有人试图修正上面的警告。这篇文章是给你的。如果它能帮助别人理解某事，那就更好了。

另外，我在这方面还是个新手，所以鼓励积极的批评:)

## 前言:渲染道具？

如果您知道什么是渲染道具组件，请随意跳过本节。

理解 React 中的 Render Props 组件模式(也称为子组件模式的功能)需要一些脑力劳动。我第一次在野外遇到它时，我非常确定我只是坐在那里怀疑地盯着我的屏幕看了几分钟，然后决定我还不够聪明，不能处理我面前的代码，然后继续做别的事情。

有很多雄辩和聪明的人写的其他文章比我更好地解释了渲染道具，所以我只给出一个大概的概述:“渲染道具”是 React 中的一个组件模式，用于分离逻辑和渲染。例如，假设您有一个 DataProvider 组件，它提供一些数据，但是您知道您会希望在整个应用程序中以不同的方式使用这些数据。在这种情况下，您不希望每次都将自己局限于以特定的方式呈现数据。所以你创建了一个组件`<DataProvider />`,它处理数据，但在渲染时并不真正知道如何处理这些数据。问题是我们可以将一个函数传递给一个属性(比如叫做`render`),这个属性告诉`<DataProvider />`如何显示数据。瞧。渲染道具。

```
<DataProvider render={data => (
  <h1>Hello {data.target}</h1>
)}/> 
```

你可以在 [React 文档](https://reactjs.org/docs/render-props.html)或[这篇博文(作者罗宾·维鲁奇)](https://www.robinwieruch.de/react-render-props-pattern/)中读到更多关于这种模式的内容。要点是将呈现功能传递给数据管理组件。这只是分离关注点的另一种方式，你有时会在野外遇到它们，或者有一天出于某种原因你会发现自己正在写一个。

值得一提的是，有时候你根本不用一个命名的道具来传递渲染逻辑，但是，奇怪的是，你作为一个孩子直接传递。如果我们这样做，上面的`<DataProvider/>`组件最终会被这样使用:

```
<DataProvider>
{data => (
  <h1>Hello {data.target}</h1>
)}
<DataProvider/> 
```

基本上，我们把函数传递给`props.children`而不是`props.render`。我想这是因为它更容易筑巢？我不确定，但人们有时会这样做，这个小模式实际上是本周我的代码出错的症结所在，所以要小心！

我还在 codepen 上建立了一个小的[项目，可以给出一个渲染道具/函数的小插图，大部分取自 React 文档，供那些喜欢动手的人使用。](https://codesandbox.io/s/testing-render-props-jrdun)

[https://codesandbox.io/embed/testing-render-props-jrdun](https://codesandbox.io/embed/testing-render-props-jrdun)

## 错误

以我为例，这周就碰到了以下情况。我正在为我当前客户的相当大的测试套件(701 个测试套件；3296 个人测试-呀！)来修复测试过程中出现的所有警告和错误输出。这些通常是使用`i`作为键或者根本不使用键，或者不使用`act`来包装更新，或者导致通过测试(并因此成功的拉请求)的那种不良实践的结果，但是指出了代码或测试中的问题。

我经常遇到的一个错误是:

> 警告:函数作为 React 子级无效。如果您返回一个组件而不是从 render 返回，可能会发生这种情况。或者你想调用这个函数而不是返回它。

沿着堆栈跟踪，我不可避免地回到了一个组件的测试套件，该组件小时候有一个 Render Props 组件。这是怎么回事？

## 模拟设置

嗯，我们经常在测试中模仿组件的子组件，尤其是当我们使用快照来检查正在测试的组件的输出时。这意味着，如果我们改变孩子，我们不需要担心打破父母的测试套件。

最基本的方法是，我们经常用公式化的方法是:

```
jest.mock('./path/to/MyComponent', () => 'mock-my-component'); 
```

这样，`<MyComponent />`和它的道具就换成了道具相同的`<mock-my-component />`；即`<MyComponent foo="bar" />`替换为`<mock-my-component foo="bar" />`。

## 问题

你还和我在一起吗？你知道我的问题是什么吗？我花了一分钟。

基本上，我们把一个渲染道具/函数作为一个子道具组件，用一个期望普通`props.children`的组件交换它(使用 mock)，然后我们把它传递给老的子组件，这仍然是一个函数。

我是说，这对你有意义吗？

```
function adds(a, b){ return a+b; };

// ... some time later
const myComponent = ()=>(<div> {adds} </div>) 
```

我希望不会。这就是为什么 React 对我如此抓狂:我实际上传递了一个哑组件，它应该是一个无效的子组件。

那我们该怎么办？

## 解

我发现的两个解决方案是 1)根本不模仿，2)使用浅层渲染，或者 3)写一个更好的模仿。

事实证明，使用浅层渲染从组件中去掉了子道具，所以我们传递函数不是问题。在这种情况下，我们甚至不需要一个模拟！太好了，但也许那不是你喜欢的。在我们的例子中，我们有很多测试，根据我的经验，使用浅层渲染器会慢一些。

所以，我坚持解决方案 3)写更好的模拟。

代替

```
jest.mock('path/to/MyComponent', () => 'mock-my-component'); 
```

我最终选择了类似于

```
jest.mock(
  './path/to/MyComponent', 
  ({ children, ...rest }) => (
    <mock-my-component {...rest}>
      {typeof children === 'function' ? '[Child as a function]' : null}
    </mock-my-component>
  )
); 
```

这个模拟的优点是向我们显示了我们传递给组件的所有属性，不会给我们带来大的错误，并且在快照测试的情况下通知我们，我们已经将一个函数作为子级传递给了组件。

就是这样！

我希望这能帮助和我遇到同样问题的人，如果这里有你喜欢或不喜欢或有问题的东西，请留下评论。我们开始讨论吧。

谢谢！