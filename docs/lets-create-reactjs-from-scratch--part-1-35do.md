# 让我们从头开始创建 ReactJS 第 1 部分

> 原文：<https://dev.to/anamritraj/lets-create-reactjs-from-scratch--part-1-35do>

作为 React 的粉丝，我一直想知道它是如何在繁重的数据驱动的应用程序中表现如此出色的。此外，我一直想为 GitHub 上的 ReactJS 做贡献，但不知何故，我觉得对于除了制造问题之外从未为开源做出贡献的人来说，这个库可能会令人不知所措。

现在，我正在温习我的数据结构和算法技能，并在考虑如何应用我所学的知识。实现你自己的链表和散列表很酷，但是接下来呢？这是我想创建自己的 ReactJS 版本的时候。

这将帮助我做几件事:

*   了解 React 的内部工作方式。
*   应用我对数据结构的一点点知识来重新创建一些我目前用作黑盒的东西。
*   让我成为更好的 Javascript 开发人员。
*   帮助我为 ReactJS 的原始图书馆做贡献。

让我们也列出我打算添加到 *MyReact* 中的特性

*   呈现 DOM 元素
*   可能探索 JSX，并使用它来呈现 DOM 元素。
*   自定义类组件和状态
*   还有 MVP 虚拟 DOM！
*   此外，我想了解光纤架构，并重新创建它

那么我们开始吧，首先，我们需要一个名字。我真的不擅长给东西起名字。所以我把它命名为 *MyReact* 。很有创意吧？

好了，现在让我们真正开始。所以我们的目标是首先创建一个 ReactJs 的基本版本，而不用担心性能、可调试性、可移植性等等。尽管如此，我们将随着时间的推移对其进行改进，并且很快会用 MyReact 替换原来的 ReactJS。*笑起来像个恶棍*

> 你可以找到更多关于[实施细节](https://reactjs.org/docs/codebase-overview.html)的信息，或者欣赏保罗·欧尚尼斯的[演讲](https://www.youtube.com/watch?v=_MAD4Oly9yg)。

让我们从创建负责创建 DOM 元素的`render()`函数开始。要理解`document.createElement()`函数，你不需要成为 javascript 专家，但是为了本教程，让我们重温一下浏览器中可用的基本 DOM API

```
// Get an element by its id,
var parent = document.getElementByID('app-root')

// Now create a new element of a given tag
var child = document.createElement('h1')

// Add properties to the DOM element
child.classList = ['bold', 'fancy-color']
child.innerHTML = 'Hello World!'

// Now append that element to the parent
parent.appendChild(child) 
```

到目前为止，我们还没有定义任何新的东西，上面的所有操作在所有浏览器中都支持，不需要任何库。

如你所见，为了在网页上呈现标签，我们将使用`document.createElement()`。这将负责在页面上呈现标签。此外，我们可以使用类和自定义内联 CSS 修改属性，使其看起来像我们想要的任何东西。

现在我们有了在网页上呈现标签的方法，让我们后退一步，定义一个元素在 MyReact 中的样子。

```
// If we want to represent a DOM element as an object,
// following would be its properties
var element = {
  type: 'div',
  props: {
    // properties of the element
    id: 'body',
    // children can again contain elements,
    // which are going to have the same structure
    children: [
      { type: 'p', props: { nodeValue: 'If you want to know more ' } },
      { type: 'a', props: { href: '/home', nodeValue: 'Click here' } },
    ],
  },
} 
```

这里没有什么特别的，只是一个 javascript 对象，它表示 MyReact 中的以下结构。从现在开始我们称它们为 MyReact 元素。

```
<div id="body">
  <p>If you want to know more</p>
  <a href="/home">Click here</a>
</div> 
```

MyReact 元素**不是**真正不同于 [React 元素](https://reactjs.org/blog/2014/10/14/introducing-react-elements.html)。在 React 中，通常不使用 javascript 对象来定义和创建元素，可能会使用 JSX。我们将在以后的帖子中做同样的事情。现在，我们将使用上面的 javascript 对象来定义 MyReact 元素并将其呈现为 DOM 元素。

## 渲染功能

我们有 MyReact 元素，让我们实现`MyReact.render`函数，它相当于 [ReactDOM.render](https://facebook.github.io/react/docs/react-dom.html#render) 。该函数将创建由 javascript 对象定义的 DOM 元素，并将其附加到其父元素上。

```
function render(element, parent) {
  // Get the props and type from element object
  const { type, props } = element

  // Check if there are any childrens of the given element
  const childElements = props.children || []

  // render those childrens recursively first
  childElements.forEach(el => render(el, dom))

  // finally append the element to the parent element
  parent.appendChild(dom)
} 
```

这是你见过的最基本版本的`render`函数。所做的就是创建一个 DOM 元素，并递归地将它的所有子元素添加到父元素中。这仍然缺少所有属性和 eventListeners。

让我们使用`Object.keys`遍历`props`键来获取 eventListeners &属性并正确设置它们。

```
function render(element, parent) {
  // Get the props and type from element object
  const { type, props } = element

  // Filter for eventListeners in the props
  const isListener = name => name.startsWith('on')

  // Add eventListeners to the dom element
  Object.keys(props)
    .filter(isListener)
    .forEach(name => {
      const eventType = name.toLowerCase().substring(2)
      dom.addEventListener(eventType, props[name])
    })

  const isAttribute = name => !isAttribute && name !== 'children'

  Object.keys(props)
    .filter(isAttribute)
    .forEach(name => {
      dom[name] = props[name]
    })

  // Check if there are any childrens of the given element
  const childElements = props.children || []

  // render those childrens recursively first
  childElements.forEach(el => render(el, dom))

  // finally append the element to the parent element
  parent.appendChild(dom)
} 
```

## 渲染 DOM 文本节点

现在，这看起来是一个非常完整的`render`函数。除了一件事。纯文本节点。让我们定义一个文本元素作为 javascript 对象的样子。

```
const textElement = {
  type: span,
  props: {
    children: [
      {
        type: 'TEXT_NODE',
        props: { nodeValue: 'Hello World!' },
      },
    ],
  },
} 
```

当通过我们的`render`函数时，上面的定义会定义类似这样的东西

```
<span> Hello World! </span> 
```

注意我们是如何定义一个新类型`TEXT_ELEMENT`的，我们将在渲染函数中使用它。让我们看看如何使用它。我们将使用`createTextNode`函数，而不是使用`createElement`。属性将会像以前一样设置，我们不需要在`createTextNode`函数中显式地传递它。

```
function render(element, parent) {
  // Get the props and type from element object
  const { type, props } = element

  // Check if it is a text element
  const isTextElement = type === 'TEXT_ELEMENT'

  // Create a new dom element or TextNode based on the type
  const dom = !isTextElement
    ? document.createElement(type)
    : document.createTextNode("")

  // Filter for eventListeners in the props
  const isListener = name => name.startsWith('on')

  // Add eventListeners to the dom element
  Object.keys(props)
    .filter(isListener)
    .forEach(name => {
      const eventType = name.toLowerCase().substring(2)
      dom.addEventListener(eventType, props[name])
    })

  const isAttribute = name => !isAttribute && name !== 'children'

  Object.keys(props)
    .filter(isAttribute)
    .forEach(name => {
      dom[name] = props[name]
    })

  // Check if there are any childrens of the given element
  const childElements = props.children || []

  // render those childrens recursively first
  childElements.forEach(el => render(el, dom))

  // finally append the element to the parent element
  parent.appendChild(dom)
} 
```

关于这一点的完整代码可以在 [Github](https://github.com/anamritraj/my-react/blob/master/src/render.js) 获得。

我做了一个 [**codepen demo**](https://codepen.io/anamritraj/pen/aPgOPj) 来演示你如何在现实世界的应用中使用它。

## 尾注

这真的很有趣！关于这一点的完整代码可以在 [Github](https://github.com/anamritraj/my-react) 获得。这将是一系列的职位，我将逐步增加功能，并张贴我的发现。

我很想知道你希望我接下来实现什么功能。虽然我只是刚刚开始，但我计划让它尽可能接近最初的反应。它可能永远不会生产就绪，但我会学到很多东西。

下次见！

* * *

更新[下面是第二部](https://dev.to/anamritraj/lets-create-reactjs-from-scratch--part-2-1pf0)！