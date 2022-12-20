# 打字稿和 JSX 第二部分-什么能创造 JSX？

> 原文：<https://dev.to/ferdaber/typescript-and-jsx-part-ii---what-can-create-jsx-22h6>

在本系列的第一部分中，当讨论 JSX 表达式的抽象语法树时，我们学习了基本的 JSX 语法和一些基本术语。现在让我们深入了解 TypeScript 如何检查 JSX 表达式的类型有效性。

不是所有的东西都是有效的 JSX 构造函数，你不能把任何随机值塞进 JSX 表达式的开始标签:

```
// bad! it's actually 'a'
const badAnchor = <anchor href='dev.to'>Go to dev.to!</anchor>

// bad! it's not a function!
const MyComponent = {}
const badFunctionElement = <MyComponent>Hi!</MyComponent>

// bad! it's not something that can render!
class MyClassComponent {
  constructor(props: any) { this.props = props }
}
const badClassElement = <MyClassComponent>Hi!</MyClassComponent> 
```

那么，TypeScript 如何知道什么时候是有效的 JSX 元素构造函数呢？答案就在神奇的`JSX`名称空间中。记住`jsxFactory`编译器选项(或`@jsx`杂注)是如何工作的，我们知道 React 的工厂函数是`React.createElement`。你也可以使用一些其他的库，其中工厂函数通常被称为`h` :

```
// @jsx React.createElement
import React from 'react'

// @jsx h
import { h } from 'preact' 
```

TypeScript 将尝试在工厂函数下查找名为`JSX`的名称空间，如果找不到，则退回到全局名称空间:

*   对于另一个名称空间下的工厂函数，比如`React.createElement`，它将寻找`React.JSX`
*   对于仅仅是一个裸标识符的工厂函数，比如`h`，它会寻找`h.JSX`
*   如果没有找到`JSX`名称空间，它将寻找一个全局`JSX`名称空间

React 类型定义声明了一个全局`JSX`名称空间，尽管这不是一个好主意，我们应该尽快改变它😅。

那么`JSX`名称空间有什么用呢？TypeScript 在它下面查找特定的接口，以确定每种类型的 JSX 元素构造函数可以接受什么:

*   对于“内在”元素构造函数(小写标签名)，它会查看在`JSX.IntrinsicElements`下是否存在具有相同键的属性。
*   对于函数元素构造函数，它检查其返回类型是否可赋给`JSX.Element`接口。
*   对于基于类的元素构造函数，它检查其实例类型是否可分配给`JSX.ElementClass`接口。

让我们详细看看每个案例:

#### 内在元素构造函数

如果您的 JSX 命名空间如下所示:

```
namespace JSX {
  interface IntrinsicElements {
    a: HTMLAttributes<HTMLAnchorElement>
    button: HTMLAttributes<HTMLButtonElement>
    div: HTMLAttributes<HTMLElement>
    span: HTMLAttributes<HTMLElement>
  }
} 
```

然后就可以渲染这些元素:

```
const validIntrinsicElements = [<a />, <button />, <div />, <span />]
// error properties 'select', 'main', and 'nav' do not exist on type 'JSX.IntrinsicElements'
const invalidIntrinsicElements = [<select />, <main />, <nav />] 
```

我们将在本系列的下一部分讨论属性类型本身的实际含义。

#### 函数元素构造函数

如果您的 JSX 命名空间如下所示:

```
namespace JSX {
  interface Element {
    key?: string
    type: string | (() => any)
    props: { [propName: string]: any }
  }
} 
```

你有一个这样的函数:

```
function MyComponent(props: any) {
  return {
    type: MyComponent,
    props: props
  }
} 
```

那么你就有了一个有效的构造函数！因为它的返回类型可赋给`JSX.Element` :

```
const myFunctionElement = <MyComponent /> // good to go! 
```

但是，当你有一个函数，它的返回类型没有注释，但是它返回 JSX，这是怎么回事呢？这是因为 TypeScript 会将任何 JSX 表达式的类型视为与`JSX.Element`相同的类型！

```
function MyComponent() {
  return <div>Hi!</div>
}

const myFunctionElement = <MyComponent /> // still okay

const nakedElement = <div>hi!</div>
type NakedElementType = typeof nakedElement // the type is JSX.Element 
```

一个敏锐的读者会注意到，当谈到 React 允许你从一个组件返回什么时，这有一些奇怪的陷阱。请记住，React 允许您从组件返回数组、字符串、数字和布尔值，它会很高兴地呈现:

```
function MyStringFragment() {
  return ['a', 'b', 'c'] // type is string[]
}

const myFragment = <MyStringFragment /> // TS error! 
```

哦，这是类型检查器的一个不幸的限制；如果我们想让检查通过，我们需要断言返回值的类型:

```
function MyStringFragment() {
  return ['a', 'b', 'c'] as any as JSX.Element
}

const myFragment = <MyStringFragment /> // good now! 
```

TypeScript 回购有一个未决问题，有望在未来解决这个问题:[https://github.com/Microsoft/TypeScript/issues/14729](https://github.com/Microsoft/TypeScript/issues/14729)。

#### 类元素构造函数

如果您的 JSX 命名空间如下所示:

```
namespace JSX {
  interface ElementClass {
    render(): any
  }
} 
```

你有这样一个类:

```
class Component {
  constructor(props: any) {
    this.props = props
  }

  render() {
    return { obviouslyNotAnElement: 'fooled ya!' }
  }

  someOtherMethod(): string
} 
```

那么你就有了一个有效的构造函数！因为它的实例类型可以赋给`JSX.ElementClass` :

```
const myComponentInstance = new Component({})

type myComponentInstanceType = {
  render(): { obviouslyNotAnElement: string }
  someOtherMethod(): string
}

type ComponentInstanceType = {
  render(): any
} 
```

显然真正的 React 类型是不同的，但这就是为什么我们总是从`React.Component`中`extend`，因为这是它在 React 的类型中大致的样子:

```
namespace React {
  type Renderable = JSX.Element | JSX.Element[] | number | string | boolean | null
  class Component {
    /* other methods like setState, componentDidUpdate, componentDidMount, etc */
    render(): Renderable
  }

  namespace JSX {
    interface ElementClass {
      render(): Renderable
    }
  }
} 
```

现在你声明的任何扩展`React.Component`的类都将是有效的构造函数！

总之:在我们谈论 props 之前，TypeScript 必须检查一个组件实际上是否是一个有效的 JSX 构造函数，否则当你试图在 JSX 表达式中使用它时，它会拒绝它。

在本系列的下一篇文章中，我们将讨论给定一个特定的 JSX 表达式，TypeScript 认为什么是有效的*属性*(记住:属性是你赋予 JSX 表达式的道具，就像 HTML 元素属性一样)。