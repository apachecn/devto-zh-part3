# JavaScript (ES5)函数——第 2 部分

> 原文：<https://dev.to/martyhimmel/javascript-es5-functions---part-2-33mm>

*此文最初于 2017 年 1 月 10 日发布在我的网站 [https://martyhimmel.me](https://martyhimmel.me) 。像 dev.to 上的许多其他人一样，我决定将我的技术博客帖子转移到这个网站。*

上一次，我们看了函数的基础知识以及如何创建它们。在本教程中，我们将更多地关注函数的使用，并将递归、闭包和函数视为“一等公民”或“高阶函数”(这两个术语指的是同一个概念)。

## 递归

递归函数是一种反复调用自身直到满足某种条件的函数。你可以把它想象成一个自循环函数。

对于第一个例子，我们将使用阶乘。如果你不熟悉阶乘，这是一个数学术语，它取一个数，然后乘以每个数(从 1 开始)直到给定的数。比如阶乘 5(写成`5!`)就是 1 * 2 * 3 * 4 * 5 的结果，所以 5！= 120.以此为基础，这里有一个用标准的`for`循环来处理它的函数。

```
function factorial(num) {
  if (num < 0) {
    return -1;
  }
  if (num === 0) {
    return 1;
  }
  var total = 1;
  for (var i = 1; i <= num; i++) {
    total *= i;
  }
  return total;
}
factorial(5); // 120 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们把它转换成一个递归函数。

```
function factorial(num) {
  if (num < 0) {
    return -1;
  }
  if (num === 0) {
    return 1;
  }
  return factorial(num - 1) * num;
}
factorial(5); // 120 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这简化了代码。不过递归函数在 JavaScript 中有其局限性。每次你调用一个函数，它都会被添加到堆栈中，这就占用了内存。堆栈越大，使用的内存就越多。如果堆栈太大，应用程序(或浏览器)可能会挂起和/或崩溃。

让我们以斐波那契数列为例。如果你不熟悉的话，斐波纳契数列是将前面两个值相加的一系列数字。从一开始，前几个数字是 1、1、2、3、5、8、13、21 等等。1 + 1 = 2，1 + 2 = 3，2 + 3 = 5，如此循环下去。其数学公式是*F<sub>n</sub>= F<sub>n-1</sub>+F<sub>n-2</sub>*-这个公式的性质非常适合递归函数。

```
function fibonacci(num) {
  if (num < 2) {
    return num;
  }
  return fibonacci(num - 1) + fibonacci(num - 2);
}
fibonacci(8); // 21 - the eighth number in the series 
```

Enter fullscreen mode Exit fullscreen mode

这是棘手的部分——根据您的系统，这可能会使浏览器以相对较低的数量挂起。在我的笔记本电脑上，当它计算的时候，在`fibonacci(30)`有一个短暂的停顿，在`fibonacci(40)`大约有 2 秒钟的等待，并且它在`fibonacci(55)`左右暂停。原因是这个递归函数的设置方式，它为每一个值调用两次以上的函数，再调用两次函数来计算这些值，以此类推。这是它的图示。

[![Chart of fibonacci function calls](img/80e7c674c46fb416a28969acd2dc55e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0LHutKMu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://martyhimmel.me/uploads/fibonacci.png)

正如你所看到的，它快速地(指数级地)建立了堆栈。如图所示，基数只有 5，有 15 个函数调用。基数为 10 时，有 177 个函数调用。你可以看到这是如何迅速失控的。

解决这个问题的方法是通过记忆——一个存储已知或先前计算的值并传递该信息的过程。这导致了更少的函数调用，这意味着堆栈远没有那么大，性能也大大提高了。这里有一个`fibonacci`函数的记忆版本。

```
function fibonacciMemoized(num, values) {
  // First call to this function, values will be undefined since the "values" argument isn't passed
  if (typeof values === 'undefined') {
    values = {};
  }

  if (num < 2) {
    return num;
  }

  // Calculate values if needed and stores them in the "values" object
  if (!values.hasOwnProperty(num - 1)) {
    values[num - 1] = fibonacciMemoized(num - 1, values);
  }
  if (!values.hasOwnProperty(num - 2)) {
    values[num - 2] = fibonacciMemoized(num - 2, values);
  }

  return values[num - 1] + values[num - 2];
}
fibonacciMemoized(8); // 21 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，任何时候计算一个值，它都被存储在`values`对象中，并在每次调用时传递。在非内存化版本中，即使在另一个分支中计算了相同的值，也会进行函数调用。在有记忆的版本中，一旦计算出一个值，就再也不用计算它了。检查`values`对象的数字值，如果存在，就使用它，而不是再次调用函数。分支现在看起来更像这样:

[![Chart of memoized fibonacci function calls](img/c0dd47eb8ae0def53bf4cf5a3caa417d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FiRMOC6T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://martyhimmel.me/uploads/fibonacci-memoized.png)

在非记忆版本中，随着基数的增大，函数调用的数量呈指数增长。在内存化版本中，随着基数变大，函数调用的数量只比基数多一个，导致堆栈明显变小，性能与上一版本相比呈指数级增长。在我的笔记本电脑上，调用`fibonacciMemoized(1000)`返回即时结果，而非记忆版本在 55 左右完全崩溃。

## 关闭

闭包最简单的定义是函数中的函数，但是这个定义没有抓住闭包的力量。在闭包中，内部函数可以访问外部函数的变量和参数。这里有一个例子。

```
function displayWelcomeMessage(firstName, lastName) {
  function fullName() {
    return firstName + '  ' + lastName;
  }
  return 'Welcome back, ' + fullName() + '!';
}
console.log(displayWelcomeMessage('John', 'Smith')); // Welcome back, John Smith! 
```

Enter fullscreen mode Exit fullscreen mode

内部函数从外部函数获取参数并连接它们，然后将其传递回外部函数。然后，外部函数用内部函数的结果创建一条欢迎消息，然后返回完整的欢迎消息。

闭包的好处之一是它创建了一个私有范围。在上面的例子中，如果你试图在`displayWelcomeMessage`函数之外调用`fullName()`，你会遇到一个`Uncaught ReferenceError: fullName is not defined`错误。`fullName()`仅在`displayWelcomeMessage`境内可用。

## 闭包和立即调用的函数表达式

使用闭包的一种常见方式是使用立即调用的函数表达式(IIFE)。生命是一种功能，它一被创造就开始运行。这是一个标准函数和生命的比较。

```
// Standard function
function foo() {
  // do something
}
foo(); // Need to call the function to use it

// Immediately Invoked Function Expression
(function() {
    // do something right now
})(); // anonymous IIFE - the code inside is executed immediately (no need to call it later) 
```

Enter fullscreen mode Exit fullscreen mode

很多时候，你会发现整个剧本被包裹在生活中。这样做可以防止全局范围被变量和函数污染。它实际上创建了一个私有范围的模块。这实际上也是 JavaScript 中几种设计模式的基础，比如模块和显示模块模式。

## 高阶函数

JavaScript 函数被称为“高阶”或“一级”函数(它们是同一个东西)。这意味着函数可以像对象一样使用——你可以传递一个函数作为参数，从另一个函数返回一个函数，等等。事件侦听器依赖于接受函数作为参数。

```
function buttonClickListener() {
  console.log('You clicked a button!');
}
document.getElementById('myButton').addEventListener('click', buttonClickListener); 
```

Enter fullscreen mode Exit fullscreen mode

这个例子通过传递函数名(`buttonClickListener`)作为参数，将一个点击事件附加到一个属性为“myButton”的按钮上。你可能已经注意到这和调用函数有点不同。当你调用一个函数时，你包括括号(例如`myFunction()`)。当你传递一个函数作为参数时，不包括括号。如果这样做，它将立即执行函数，而不是将其作为参数传递。

也可以使用匿名函数作为参数。这个例子和前面的例子有相同的功能，但是用一个匿名函数代替。

```
document.getElementById('myButton').addEventListener('click', function() {
  console.log('You clicked a button!');
}); 
```

Enter fullscreen mode Exit fullscreen mode