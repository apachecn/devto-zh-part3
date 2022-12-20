# JSX 到底是什么？

> 原文：<https://dev.to/ryanharris/what-the-heck-is-jsx-45h4>

作为开发人员，我们使用各种工具和开源包来简化我们的工作。其中一些在整个社区中被广泛使用，以至于它们似乎是 JavaScript 的原生语言。虽然它们不是，但是它们可以从根本上改变你每天编写代码的方式。

其中一个你已经在使用的技术是 JSX，**一个 JavaScript** 的类似 XML 的语法扩展。它由脸书的向导创建，旨在简化开发人员的体验。正如说明书所说，创建 JSX 的理由是:

> “…定义一个简洁而熟悉的语法，用于定义带有属性的树结构。”~ [JSX 规格](https://facebook.github.io/jsx/)

现在，你可能会对自己说，“嘿，Ryan，这听起来很棒，但是**已经开始写代码了**，这是我们的第一个例子。

```
const helloWorld = <h1>Hello, World!</h1>; 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！上面的片段看起来很熟悉，但你有没有停下来想想它的威力？JSX 让我们能够传递由 HTML 或 React 元素组成的树形结构，就好像它们是标准的 JavaScript 值一样。疯了！

[![Hola](img/eee4c466a8d414711de66bc241d1976e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EajX91nP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1489945052260-4f21c52268b9%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D1867%26q%3D80)

来源:[乔恩·泰森](https://unsplash.com/@jontyson)

虽然在编写 React ( [或使用 React 来尝试 JSX](https://github.com/babel/babel/tree/master/packages/babel-plugin-syntax-jsx) )时，您不必使用 JSX，但不可否认的是，它是 React 生态系统的重要组成部分，所以让我们深入了解一下到底发生了什么！

* * *

### JSX 入门

使用 JSX 语法时要注意的第一件事是 **React 必须在范围**内。这是由于它是如何被编译的。以这个组件为例:

```
function Hello() {
  return <h1>Hello, World!</h1>; } 
```

Enter fullscreen mode Exit fullscreen mode

在幕后，由`Hello`组件呈现的每个元素都被传输到 React.createElement 调用中。在这种情况下:

```
function Hello() {
  return React.createElement("h1", {}, "Hello, World!");
} 
```

Enter fullscreen mode Exit fullscreen mode

嵌套元素也是如此。下面的两个例子最终会呈现相同的标记。

```
// Example 1: Using JSX syntax
function Nav() {
  return (
    <ul>
      <li>Home</li>
      <li>About</li>
      <li>Portfolio</li>
      <li>Contact</li>
    </ul>
  );
}
// Example 2: Not using JSX syntax
function Nav() {
  return React.createElement(
    "ul",
    {},
    React.createElement("li", null, "Home"),
    React.createElement("li", null, "About"),
    React.createElement("li", null, "Portfolio"),
    React.createElement("li", null, "Contact")
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

### React.createElement

当 React 创建元素时，它调用这个方法，这个方法有三个参数。

1.  元素名称
2.  表示元素道具的对象
3.  元素的子元素的数组

这里需要注意的一点是，React 将小写元素解释为 HTML 和 Pascal case(例如。`ThisIsPascalCase`)元素作为自定义组件。因此，**下面的例子会有不同的解释**。

```
// 1\. HTML element
React.createElement("div", null, "Some content text here");

// 2\. React element
React.createElement(Div, null, "Some content text here"); 
```

Enter fullscreen mode Exit fullscreen mode

第一个例子将生成一个以字符串`"Some content text here"`为子元素的`<div>`。然而，第二个版本会抛出一个错误(当然，除非定制组件`<Div />`在范围内)，因为`<Div />`是未定义的。

### JSX 道具

在 React 中工作时，您的组件通常会渲染子组件，并且需要向它们传递数据，以便子组件能够正确渲染。这些叫道具。

我喜欢把 React components 看作一群朋友。朋友是做什么的？他们互相给予支持。值得庆幸的是，JSX 为我们提供了很多实现这一目标的方法。

```
// 1\. Props defaulted to true
<User loggedIn />

// 2\. String literals
<User name="Jon Johnson" />

// 3\. JavaScript expressions
<User balance={5 + 5 + 10} /> 
// 4\. Spread attributes
<User preferences={...this.state} /> 
```

Enter fullscreen mode Exit fullscreen mode

但是要小心！不能将 if 语句或 for 循环作为道具传递，因为它们是[语句，而不是表达式](https://dev.to/promhize/javascript-in-depth-all-you-need-to-know-about-expressions-statements-and-expression-statements-5k2)。

[![Project deadline](img/4c638b2e18de601297d97f2e083262de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--owg6c4aH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1506452819137-0422416856b8%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D3366%26q%3D80)

来源:凯文·Ku

### JSX 的孩子

当你构建你的应用程序时，你最终开始让组件渲染子组件。然后这些组件有时必须呈现子组件。诸如此类。

因为 **JSX 是为了让我们更容易推理元素**的树状结构，所以这一切都变得非常简单。基本上，组件返回的任何元素都会成为其子元素。

使用 JSX 呈现子元素有四种方式:

#### 字符串

这是 JSX 孩子最简单的例子。在下面的例子中，React 创建了一个带有一个子元素的`<h1>` HTML 元素。然而，子元素不是另一个 HTML 元素，只是一个简单的字符串。

```
function AlertBanner() {
  return <h1>Your bill is due in 2 days</h1>; } 
```

Enter fullscreen mode Exit fullscreen mode

#### JSX 元素

这可能是新 React 开发人员最熟悉的用例。在下面的组件中，我们返回一个 HTML 子元素(`<header>`)，它有两个自己的子元素`<Nav />`和`<ProfilePic />`，这两个子元素都是自定义的 JSX 元素。

```
function Header(props) {
  return (
    <header>
      <Nav />
      <ProfilePic />
    </header>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 表情

表达式让我们可以轻松地在 UI 中呈现 JavaScript 计算的结果。基本加法就是一个简单的例子。

假设我们有一个名为`<BillFooter />`的组件来呈现关于账单或收据的信息。让我们假设它采用一个称为`total`的代表税前成本的道具和另一个代表适用税率的道具`taxRate`。

使用表达式，我们可以很容易地为用户呈现一些有用的信息！

```
function BillFooter(props) {
  return (
    <div>
      <h5>Tax: {props.total * props.taxRate}</h5>
      <h5>Total: {props.total + props.total * props.taxRate}</h5>
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 功能

通过函数，我们可以编程地创建元素和结构，然后 React 将为我们呈现这些元素和结构。这使得创建一个组件的多个实例或呈现重复的 UI 元素变得容易。

举个例子，让我们使用 JavaScript 的`.map()`函数来创建一个导航栏。

```
// Array of page information
const pages = [
  {
    id: 1,
    text: "Home",
    link: "/",
  },
  {
    id: 2,
    text: "Portfolio",
    link: "/portfolio",
  },
  {
    id: 3,
    text: "Contact",
    link: "/contact",
  },
];
// Renders a <ul> with programmatically created <li> children
function Nav() {
  return (
    <ul>
      {pages.map(page => {
        return (
          <li key={page.id}>
            <a href={page.link}>{page.text}</a>
          </li>
        );
      })}
    </ul>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们想给我们的站点添加一个新页面，我们需要做的就是给`pages`数组添加一个新对象，React 会处理剩下的事情！

**注意`key`道具**。我们的函数返回一个兄弟元素的数组，在本例中是`<li>` s，React 需要一种方法来跟踪哪些装载、卸载或更新。为此，它依赖于每个元素的唯一标识符。

* * *

### 使用工具！

[![Workshop, tool, screwdriver](img/18bb7919fcc479a7edaf09a4ab555c3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--79UIjzQq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1426927308491-6380b6a9936f%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D2702%26q%3D80)

来源:[谷仓图片](https://unsplash.com/@barnimages)

当然，没有 JSX，你也可以编写 React 应用程序，但是我不确定你为什么要这么做。

JSX 让我们像对待一等公民一样传递 JavaScript 中的元素，这种能力非常适合与 React 生态系统的其他部分合作。那么好吧，事实上，你可能每天都在写，甚至不知道。

底线是:用 JSX 就好。你会很高兴你做到了👍