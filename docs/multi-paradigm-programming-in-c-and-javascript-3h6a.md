# C++和 Javascript 中的多范例编程

> 原文：<https://dev.to/dwd/multi-paradigm-programming-in-c-and-javascript-3h6a>

最近，我一直在使用 Elixir——一种更流行的函数式语言，它是围绕函数式编程的三个支柱构建的:

*   一流的功能
*   不可变数据
*   没有副作用(纯功能)

这些通常与面向对象编程的三个支柱相冲突:

*   对象拥有状态和行为
*   对象通过消息进行通信
*   对象是特定于任务的

冲突最大的是最后一个功能支柱和第一个面向对象的支柱。事实证明，如果一个对象拥有自己的状态，那么它的方法有时不可避免地会改变这个状态。

但是 C++和 Javascript 都允许程序员使用其中一种或者两种编程风格，甚至更多。

## 功能完善？

C++和 Javascript 都可以很容易地将函数作为一级值传递。现代 C++对此有`std::function`，而 Javascript 有更明显的`function`类型。

在 C++中用`const`关键字很容易使数据不可变，在 Javascript 中通常也是如此(尽管`const`在这里也有帮助)。

但是在语言库中可以找到更多的帮助。

## 程序员的命令式

在 C++或 Javascript 中，我们有许多命令式的循环结构。例如，两者都允许通过可迭代容器按值迭代。让我们把它们加起来:

在 C++中，我们将使用 C++11:
中的范围循环

```
int sum = 0;
for (auto i : iterable) {
  sum += i;
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Javascript 中，我们可以使用 ES6:

```
let sum = 0;
for (let i of iterable) {
  sum += i;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 程序员的宣言

当然，我们也可以使用 Reduce 样式来实现这一点。

C++版本利用了默认的 init 是`T()`，默认的二进制操作是`std::plus<>`这一事实，这意味着对一个数组求和相当容易——我在这里使用 C++17 的`std::reduce`,是因为我稍后将谈到的第一个参数:

```
auto result = std::reduce(std::execution::par, iterable.begin(), iterable.end()); 
```

Enter fullscreen mode Exit fullscreen mode

在 Javascript 中，我们使用`Array.reduce`方法，并在:
中加入一个 lambda -或者更确切地说是 arrow 函数

```
let result = iterable.reduce((sum, val) => sum + val); 
```

Enter fullscreen mode Exit fullscreen mode

对于 Javascript 版本，这两者之间没什么可选择的。Array.reduce 指定了操作的顺序，所以根据定义是完全一样的。对于 C++版本，由于那里的执行策略，它将自动并行执行更大的可迭代——`std::plus<>`调用可以以任何顺序运行。

## 合适的工作工具

在 C++中，模板是用声明性语言编写的——实际上是纯函数性语言。事实上，如果你看一下 [Fibonacci++](https://dev.to/dwd/fibonacci-3cak) ，你会发现两个模板实现都是纯函数的，两个递归的也是。

但是中间两个都是必须的。在构建时，纯函数性的会胜出——但是在运行时，命令性的会运行得更快。然而命令式代码肯定更难理解——正如我们所看到的，与命令式代码不同，函数式代码通常可以自动并行化。

在 Javascript 中，这两种编程范式也在不同的时间使用。React 的 JSX 很大程度上是声明性的，Redux 是纯函数性的，然而 React 组件是直接面向对象的。

## 范式鸡尾酒

然而，纯粹的函数式语言不允许这种选择——程序员被限制在做事情的单一方式中。对于一个习惯于拥有全部可用技术的程序员来说，这是一个可怕的限制。

但是，在 Javascript 和 C++中，我们可以根据需要将它们结合起来。假设我们有一个 Foo 类型，可以一起 flarbed。

```
auto result = std::reduce(std::experimental::par, iterable.begin(), Foo(), iterable.end(),
   {
    return acc.flarb(curr);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

我可能已经决定让 flarb 成为一个静态函数，因此只是把它作为 Foo::flarb 传入——但是这给了我一个展示 lambda 语法的机会——或者函数文字，如果你喜欢的话。

或者，如果你喜欢 Javascript:

```
let result = iterable.reduce((acc, curr) => acc.flarb(curr)); 
```

Enter fullscreen mode Exit fullscreen mode

这里的一个关键特性是，在这两种情况下，`flarb`都是只有我们的`Foo`类型的实现才知道的行为。因此，`Foo`可以孤立地被单元测试得整整齐齐。

在函数式语言中，您会得到类似“协议”的东西，其中特定的命名操作会根据参数的类型得到一系列的实现。这可能很强大，但是它依赖于完全不同的行为和数据，并且由于纯粹的功能需求，这意味着在一个对象中永远不能有可变的状态。

您不能简单地查看数据类型并决定它支持哪些协议。封装实际上也不是一件事——为了实现一个协议，它需要完全访问数据类型的内部数据。在面向对象中很简单的纯函数式编程中，太多的事情变得很难。

另一方面，能够在不同的层次上混合范例，允许程序员选择在什么时候使用哪一个。在 React 中，这意味着开发人员使用声明性 JSX 来描述组件架构，但随后切换到 OOP(具有可变状态，尽管受到小心保护)来实现。

在 C++中，根据当时的需要，程序员经常在不同的范例之间漂移。

## 面向功能对象？

当然，这并不意味着当你在做一些面向对象的工作时，你需要放弃纯函数式编程中非常好的想法。在 C++中，您甚至可以在这里得到一些帮助——`const`关键字使实例不可变，const 方法可以用来处理这些实例。当然，您仍然需要避免全局变量，但这通常是很好的实践。这些是纯函数方法吗？也许吧。这真的重要吗？

Javascript 的`const`有点不同，但仍然有用——反正 Javascript 中大多数基本数据类型都是不可变的，而且`const`防止重新绑定。同样，你需要避免全局变量——同样明显的是`document`和`window`将会是你可能会违反这条规则的情况。

## 获胜者是...

最好的范式永远是多范式。

虽然人们将 React 的成功归因于其功能设计，但我认为这是因为它不断地——并且始终如一地——在范式之间切换，为程序员提供正确的模型。

类似地，C++继续发展，尽管数百种更新的语言围绕着它出现(包括 Javascript！)因为开发人员可以很容易地在范例之间切换，以适应手头的工作。

熟悉几种范例以及支持它们的语言是一个很好的工具。