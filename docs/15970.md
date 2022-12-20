# 打字稿和 JSX 第一部分-什么是 JSX？

> 原文：<https://dev.to/ferdaber/typescript-and-jsx-part-i---what-is-jsx-3g8b>

截至 2019 年，TypeScript 作为 web 开发人员的首选编程语言越来越受欢迎。在这个帖子系列中，我们将探索 TS 编译器如何处理 JSX，以及它如何与最流行的利用 JSX 的框架交互:React。

首先，JSX 是如何运作的？这里有几个 JSX 的例子:

```
// a native 'span' element with some text children
const mySpan = <span>Hello world!</span>

// a custom 'CustomSpan' element with some props and some children
const myCustomSpan = (
  <CustomSpan
    key='_myspan'
    bold
    color="red"
  >
    Hello world!
  </CustomSpan>
)

// a native, self-closing 'input' element without any children
const myInput = <input />

// a custom 'Container' element with multiple children
const myWidget = (
  <Container>
    I am a widget
    <Button>Click me!</Button>
  </Container>
) 
```

JSX 是 JavaScript 的一个不符合 ECMAScript 的语法附加物，它由 TypeScript 通过`--jsx`编译器标志来支持。如果你是一名 React 开发人员，那么 JSX 实际上只是一个语法糖，可以编译成这样(如果你使用 TypeScript 编译器):

```
// a native 'span' element with some text children
const mySpan = React.createElement('span', null, 'Hello world!')

// a custom 'CustomSpan' element with some props and some children
const myCustomSpan = React.createElement(
  CustomSpan,
  { key: 'myspan', bold: true, color: 'red' },
  'Hello world!'
)

// a native, self-closing 'input' element without any children
const myInput = React.createElement('input', null)

// a custom 'Container' element with multiple children
const myWidget = React.createElement(
  Container,
  { onClick: console.log },
  'I am a widget',
  React.createElement(Button, null, 'Click me!')
) 
```

这已经是相当多的解剖；让我们注意一下这个转换中的一些有趣的事情:

*   整个 **JSX 表达式**变成了对一个名为`React.createElement`的函数的调用。这实际上是为什么如果你使用 JSX，你总是需要`import React from 'react'`，即使变量`React`实际上从未在你的代码中使用过！
*   JSX 表达式中的**标签**名称被移动到函数调用的第一个参数。
    *   如果标签名以大写字符开始，或者(在例子中没有显示)它是一个属性访问(像`<foo.bar />`)，它就保持原样。
    *   如果标签名是一个小写单词，它将被转换成一个字符串(`input -> 'input'`)
*   所有的 props(或者在抽象语法树中被称为**属性**)都被转换成一个对象，该对象被移动到函数调用的第二个参数，需要注意一些特殊的语法:
    *   如果没有传入任何道具，则该值不是空对象，也不是`undefined`，而只是`null`。
    *   速记属性语法(像`myInput`中的`bold`属性)被转换成一个值为`true`的对象属性。
    *   即使 React 将`key`和`ref`视为特殊的，它们仍然是转换中的常规属性(就语法而言)。
    *   对象属性的顺序与它们在 JSX 表达式中作为属性出现的顺序相同。
*   如果需要的话，孩子会被转换(如果他们也是 JSX)，并且按照他们出现的顺序排列，作为函数调用的其余参数。
    *   React 有一个特定的行为，其中 JSX 的一个子节点只显示为`props.children`中的那个节点，但显示为多个子节点的节点数组。这根本不是语法或规范所强制的。事实上，Preact 总是将子元素包装在一个数组中，而不管数组中有多少子元素，所以这部分是实现细节。

这就是 JSX 语法的全部内容；最终，它只是构建嵌套函数调用的语法糖，而不会伤害您的大脑。

那么，为什么编译器知道使用`React.createElement`作为函数，而不是其他什么呢？原来你可以把它改成你想要的任何东西！你所要做的就是在你的文件顶部添加一个注释或者设置一个编译器标志:

```
/* @jsx myCustomJsxFactory.produce */

// your code here 
```

```
// tsconfig.json
{
  "compilerOptions": { "jsxFactory": "myCustomJsxFactory.produce" }
} 
```

他们做同样的事情，结果只是默认值是`React.createElement`。

在本系列的下一篇文章中，我们将通过一个构建我们自己的 JSX 工厂函数的工作示例来探索 TypeScript 如何知道如何根据 JSX 语法进行类型检查。