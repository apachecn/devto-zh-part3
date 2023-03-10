# 基于 TypeScript 的 React 应用程序中的类型别名与接口

> 原文：<https://dev.to/kosslebedev/type-aliases-vs-interfaces-in-typescript-based-react-apps-2jg2>

*类型别名*和*接口*是 TypeScript 语言特性，经常让第一次尝试 TypeScript 的人感到困惑。两者有什么区别？什么时候我们应该使用一个而不是另一个？

过去，类型别名和接口在功能上有很大不同。然而，在最新版本的 TypeScript 中，这不再是真的。随着时间的推移，它们已经长到几乎完全相同的程度。它们仍然有一些微妙的区别——由于支持声明合并，接口更加“可扩展”,由于支持联合类型，类型更加“可组合”。稍后我们将更详细地讨论这些差异。

由于类型别名和接口之间的本质差异，决定使用哪一个通常取决于您偏好的编程风格。如果你写面向对象的代码——使用接口，如果你写函数代码——使用类型别名。

现在我们来谈谈 React。

React 本质上更具功能性。功能组件通常比基于类的组件更受青睐。热辣闪亮的 React 钩子只是功能组件内部使用的函数。HOCs、Redux、纯函数以及 React 中广泛使用的一些其他概念都来自于函数世界。因此，基于这些原因，

> 在 React 应用程序中，只需使用类型别名。

现在我们来看看为什么。

* * *

## 1。接口的力量在 React 应用程序中没有用

区分接口和类型别名的主要因素之一是合并声明的能力。通过接口，我们可以重新打开以前声明的接口，并向其添加额外的属性: