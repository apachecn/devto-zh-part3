# Symbol.iterator 用于模块化迭代，无需就地克隆或修改

> 原文：<https://dev.to/tombarr/symbol-iterator-for-modular-iteration-without-cloning-or-modifying-in-place-1hji>

[`Symbol.iterator`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols) 是*协议，它使得像`Array`、`Set`和`Map`这样的本地对象是可迭代的。它提供了到语言特性的挂钩，比如[扩展操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)和 [`for…of`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of) 循环。*

 *有了`Symbol.iterator`，开发者可以**重新定义导航模式，而不需要就地克隆或修改一个对象**。这意味着向前、向后、随机、无限、成对，或者任何你能想到的方式迭代[。](https://medium.com/front-end-weekly/thank-u-symbol-iterator-next-aef9f09ff78)

协议规范非常简单:

*   **Iterable** :带有键为`Symbol.iterator`的函数的对象
*   **迭代器**:可迭代函数，用于获取要迭代的值*