# 使用箭头函数可能会降低性能

> 原文：<https://dev.to/georgecoldham/using-arrow-functions-might-be-costing-you-performance-4fm6>

哦，隐性回报也可能如此…

### 背景

我们都知道并喜欢 arrow 的功能，简洁的外观，方便的使用。但是使用它们是有代价的。

首先，如果你不熟悉箭头函数，这是它们与普通函数的简单比较。

```
// Traditional function declaration
function functionName (...parameters) {
    // Do some stuff…
    return result
}

// The same function as an arrow function
const functionName = (...parameters) => {
    // Do some stuff…
    return result
} 
```

Enter fullscreen mode Exit fullscreen mode

### 好了，我知道箭头函数是什么了。他们有多坏？

JavaScript 在运行时编译，不像其他语言需要在使用前编译。这意味着我们依赖运行时编译引擎来正确地解释和有效地处理我们的代码。这意味着不同的实现可以被不同地处理，尽管给出相同的结果。

### 比较

为了测试，我将下面函数的函数调用包装在一个 console.time/console.timeEnd 三明治中，并给每个函数传递相同的变量。

```
// Traditional function
function foo(bar) {
    return bar
}

// Arrow function
const foo = bar => {
    return bar
}

// Arrow function with implicit return (remember this from the beginning?)
const foo = bar => bar 
```

Enter fullscreen mode Exit fullscreen mode

### 结果

*传统函数:* **0.0746ms**
*箭头函数:* **0.0954ms**
*带隐式返回的箭头函数:* **0.105ms**

### 结论

与传统函数相比，箭头函数，尤其是使用隐式返回的箭头函数确实需要更多的时间来运行。隐式返回与箭头函数面临同样的问题，因为它们需要更多的编译时间。在较大的脚本中，这可能会导致显著的性能开销，尤其是包含在循环中时。

这是否意味着我们都应该停止使用它们呢？我不会，也不会建议你停止。我希望每个人都尽量减少他们的生产代码。所有主要的最小化器都会将你的代码预编译成传统的函数，仅仅是为了兼容性的原因，以抵消现实世界中的性能损失。如果您在一个不统一的开发环境中遇到了速度变慢的问题，那么您可以考虑这是一个可能的问题。然而在现实中，一个优化不佳的循环将比几个箭头函数产生更多的性能成本。

> 所有测试运行 5 次，并使用运行在 NodeJs 上的谷歌 [V8 引擎](https://v8.dev/)进行平均。
> Firefox 的 [SpiderMonkey](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/SpiderMonkey) 和微软的 [ChakraCore](https://github.com/microsoft/ChakraCore) 显示了相似的结果，尽管没有经过测试。