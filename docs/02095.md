# 用 React 钩子理解 JavaScript 中的闭包

> 原文：<https://dev.to/krsgrnt/making-sense-of-closure-in-javascript-with-react-hooks-2c74>

> 闭包是函数和声明该函数的词法环境的组合。 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)

当我们在 JavaScript 中谈论闭包时，我们真正的意思是什么？当内部函数可以访问外部函数范围内的变量时，即使外部函数已经返回:
也会发生闭包

```
const outerFunction = () => {
    const firstName = 'John';
    const secondName = 'Doe';

    const innerFunction = () => {
        console.log(`${firstName} ${secondName}`);
    };

    return innerFunction;
}; 
```

在上面的例子中，*外部函数*由两个变量和第二个函数组成。该函数运行时，将返回*内部函数* :

```
const closureExample = outerFunction();
closureExample(); 
```

通过将返回的内部函数分配给名为 closureExample 的变量，然后执行它，我们将看到 firstName 和 lastName 的值被注销到控制台。这是因为 innerFunction 在 outerFunction 之上有闭包:它可以访问它的属性，即使在它们所在的函数已经执行之后。

React 中新的 Hooks API 是一个很好的闭包例子。其中一个挂钩 useEffect()用于在组件完成呈现后执行代码。类似 React 的*componentidmount*生命周期方法:

```
import React, { useEffect } from 'react';

const Header = props => {
    const { pageTitle, headerTitle } = props;

    useEffect(() => {
        document.title = headerTitle;
        return () => {
            document.title = pageTitle;
        }
    });

    return (
        <h1>{headerTitle}</h1>
    );
} 
```

在上面的例子中，我们的 Header 组件接收两个名为 headerTitle 和 pageTitle 的属性，并返回由 h1 标记包围的前者。在呈现标题之后，useEffect()钩子调用一个函数，该函数也将文档的标题设置为 headerTitle。传递给 useEffect() *的函数本身返回一个函数*，当调用该函数时，它会将文档的标题更改为 pageTitle。

因为 useEffect()钩子将在组件返回后运行传递给它的函数，但是该函数将文档的标题设置为该组件范围内的属性，所以我们可以说它对组件的属性有闭包。closure 的第二个实例发生在 React 调用从该函数返回的函数时，这将发生在组件以类似于 *componentWillUnmount* 生命周期方法的方式从 DOM 中卸载之后。这个最后的函数将文档标题设置为 pageTitle 的值，也作用于早已返回的组件。这种模式是在组件之后进行“清理”的好方法，因为您可以将值重置为默认值或取消出站获取请求。

很长一段时间以来，JavaScript 中的闭包更多的是在工作面试中抓住开发人员的一个问题，而不是该语言具有实用价值的一个特性。有了 React 的 Hooks API，在野外看到这个概念的例子变得容易多了。