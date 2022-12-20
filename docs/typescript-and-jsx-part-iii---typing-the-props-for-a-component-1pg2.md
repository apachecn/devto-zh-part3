# TypeScript 和 JSX 第四部分-对组件的属性进行类型化

> 原文：<https://dev.to/ferdaber/typescript-and-jsx-part-iii---typing-the-props-for-a-component-1pg2>

在上一篇文章中，我们学习了 TypeScript 类型如何根据构造函数的属性检查 JSX 子元素。这一次，我们将更深入地研究组件的其余道具，以及这些道具如何用于在创建 JSX 时有效的类型检查。

当确定哪些属性可以分配给由这些组件构造的 JSX 表达式时，TypeScript 以不同的方式对待内部、函数和类组件。

*   对于内部元素构造函数(小写标签名)，它在`JSX.IntrinsicElements`中查看相同属性名的类型
*   对于函数元素构造函数，它查看调用签名中第一个参数的类型
*   对于基于类的元素构造函数，它查看与`JSX.ElementAttributesProperty`同名的实例属性的类型，如果不存在，它将查看构造函数调用签名中第一个参数的类型

让我们详细看看每个案例:

#### 内在元素构造函数

如果您的`JSX`名称空间如下所示:

```
interface HTMLAttributes<T> {
  children?: ReactNode
  className?: string
  id?: string
  onClick?(event: MouseEvent<T>): void
  ref?: { current?: T }
}

namespace JSX {
  interface IntrinsicElements {
    a: HTMLAttributes<HTMLAnchorElement>
    button: HTMLAttributes<HTMLButtonElement>
    div: HTMLAttributes<HTMLElement>
    span: HTMLAttributes<HTMLElement>
  }
} 
```

那么对于一个锚元素，可用的属性你可以给一个相当于`JSX.IntrinsicElements['a']` :
的`<a />`标签

```
interface AnchorProps {
  children?: ReactNode
  className?: string
  id?: string
  onClick?(event: MouseEvent<HTMLAnchorElement>): void
  ref?: { current?: HTMLAnchorElement }
}

declare const props: AnchorProps

const myAnchor = <a {...props} /> 
```

#### 函数元素构造函数

如果你的组件看起来像这样:

```
interface Props {
  onClick?(event: MouseEvent<HTMLButtonElement>): void
  disabled?: boolean
  label: string
}

function MyButton(
  props: Props & { children?: ReactNode },
  some?: any,
  other?: any,
  parameters?: any
) {
  return <button />
} 
```

那么可用的属性是`Props`和`{ children?: ReactNode }`，因为这是函数中第一个参数的类型。请注意，TypeScript 也将考虑道具类型中的可选和必需属性:

```
const button = <MyButton /> // error because label is marked as required in Props! 
```

#### 类元素构造函数

如果你的类看起来像这样，并且你有一个像这样的`JSX`名称空间:

```
interface Props {
  onClick?(event: MouseEvent<HTMLButtonElement>): void
  disabled?: boolean
  label: string
}

class MyComponent {
  _props: Props

  constructor(props: Props & { children?: ReactNode }) {
    this.props = props
  }

  render() {
    return <button />
  }
}

namespace JSX {
  interface ElementClass {
    render(): any
  }

  interface ElementAttributesProperty {
    _props: {}
  }
} 
```

那么`MyComponent`可用的属性是`Props`(注意这个不能有`children`，因为`MyComponent`的实例类型有一个属性叫`_props`，和`JSX.ElementAttributesProperty`里面的属性名一样。如果名称空间`JSX`中的接口不存在，它会查看构造函数中第一个参数的类型，即带有`{ children?: ReactNode }`的`Props`。

这涵盖了组件可以在其中使用的所有“内部”属性。然而，在 React 中，我们有一个“外部”道具的概念，它是你可以传递到由组件构建的 JSX 表达式中的东西的实际契约。外部道具与内部道具不同的一个例子是`ref`和`key`，以及`defaultProps`:

*   `ref`和`key`不能在组件的实现中使用，但是`key`可以在 React 中被赋值给任何 JSX 表达式，而`ref`可以被赋值给任何基于类的和内在的 JSX 表达式，以及使用`forwardRef`的基于函数的表达式。
*   `defaultProps`允许一个特定的属性**总是**被定义在一个组件的实现中，但是当在那个组件的 JSX 表达式中分配相同的属性时，这个属性是可选的。

在下一篇文章中，我们将了解 TypeScript 如何使用更多的`JSX`名称空间魔法来实现这一点。