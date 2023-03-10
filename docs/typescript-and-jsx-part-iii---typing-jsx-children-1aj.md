# 打字稿和 JSX 第三部分-打字 JSX 儿童

> 原文：<https://dev.to/ferdaber/typescript-and-jsx-part-iii---typing-jsx-children-1aj>

在上一篇文章中，我们了解了哪些类型可以成为 JSX 构造函数，现在是时候更深入地了解孩子是如何工作的了。

JSX 中孩子的概念很有趣:它既可以是 JSX 属性的形式，也可以嵌套在上层 JSX 表达式中:

```
// both are valid ways to write children
const myNestedDiv1 = (
  <div>
    <span>I'm stuck!</span>
  </div>
)

const myNestedDiv2 = (
  <div
    children={<span>I'm stuck!</span>}
  />
)

// what gets rendered?
const myNestedDiv3 = (
  <div
    children={<span>Child A</span>}
  >
    <span>Child B</span>
  </div>
) 
```

在`myNestedDiv3`中，运行时结果是不明确的，并且可能会因库而异！记住 JSX 只是一个句法糖，在 React 中我们有这个:

```
// before JSX
const myNestedDiv3Untranspiled = (
  <div
    children={<span>Child A</span>}
  >
    <span>Child B</span>
  </div>
)

// after JSX
const myNestedDiv3Transpiled = React.createElement(
  'div',
  { children: React.createElement('span', null, 'Child A') },
  React.createElement('span', null, 'Child B')
) 
```

因此，实际上完全由 React 运行时来决定如何呈现实际的标记。它可以优先于`children` prop，或者它可以优先于 rest 参数，或者它可以对两者进行某种合并！

因为子元素的概念不是由 JSX 规范强制执行的，所以 TypeScript 提供了一种方法来根据**元素属性类型**对嵌套的 JSX 表达式进行类型检查。元素属性类型是添加到 JSX 表达式的所有属性的类型，即传递到`React.createElement`中第二个参数的对象的类型。

这种类型检查是通过在`JSX`名称空间下指定一个名为`ElementChildrenAttribute`的属性来完成的(关于`JSX`名称空间的更多信息，请参见上一篇文章)。举个指导性的例子，假设您的`JSX`名称空间是:

```
namespace JSX {
  interface ElementChildrenAttribute {
    enfants: {} // 'children' in french
  }
} 
```

你有一个这样定义的组件:

```
interface Props {
  enfants?: JSX.Element | JSX.Element[]
  children?: string | string[]
}

function MyComponent(props: Props) { 
  return <div>{props.enfants}</div>
} 
```

然后会发生以下情况:

```
// error! the type `string` is not assignable 
// to the type `JSX.Element | JSX.Element[]` for the `enfants` attribute
const myComponentElement1 = <MyComponent>hello world!</MyComponent>

// good! `enfants` is of type `JSX.Element` which is assignable
// to itself or on array of itself
const myComponentElement2 = (
  <MyComponent>
    <MyComponent />
  </MyComponent>
) 
```

这是 TypeScript 在嵌套的 JSX 和你声明的接口中的一些属性之间定义连接的方式。

为什么当你使用 React 时，你似乎总能在一个`React.Component`中访问`this.props.children`？这实际上只是 React 类型本身的一部分，而不是 TypeScript 本身的一些内在特性。为了方便起见，React 类型定义自动注入`children`作为可选属性。

在上面的例子中，我们定义了一个函数`MyComponent`，它使用一个名为`props`的参数和一个特定的接口。它返回 JSX，所以它是一个有效的 JSX 构造函数类型。

TypeScript 怎么知道不能随便给一个`<MyComponent />`元素添加任何随机属性？只有`enfants`、`children`和`key`可以添加吗？

在本系列的下一篇文章中，我们将进一步了解 TypeScript 如何收集您可以添加到组件元素中的所有可能的属性(及其类型)。