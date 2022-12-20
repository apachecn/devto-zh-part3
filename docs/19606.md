# JavaScript (ES5)函数——第 1 部分

> 原文：<https://dev.to/martyhimmel/javascript-es5-functions---part-1-1h3l>

*本文最初于 2017 年 1 月 2 日发布在我的网站上，地址为 [https://martyhimmel.me](https://martyhimmel.me) 。像 dev.to 上的许多其他人一样，我决定将我的技术博客帖子转移到这个网站。*

函数是包含的执行任务的代码块。通常，函数被创建为可重用的代码片段。可重用性意味着你不必在每个需要的地方反复编写相同的代码，这意味着你编写的代码更少，程序作为一个整体更容易维护。使用函数，您可以创建一次函数，并根据需要多次调用它。

如果您一直在关注这个系列，那么您已经看到了一些函数的例子。`console.log()` - `console`实际上是 JavaScript 中的一个全局对象，`.log()`是那个对象的一个函数(或者“方法”)。

关于“函数”和“方法”的一个简短说明方法是对象内部的函数。就是这样。如果一个函数不附属于一个对象(例如，在全局空间中)，它就是一个函数。如果一个函数被附加到一个对象上，它就是一个方法。

回到你已经见过的一些函数/方法。`.pop(), .push(), .shift(), .unshift()`都是数组方法。所有的 DOM 选择器(例如，`.getElementById()`)都是`document`对象的方法。

## 创建功能

在 JavaScript 中有两种创建函数的方法——作为函数表达式和作为函数声明。

## 函数表达式

函数表达式类似于创建变量。使用`var`关键字，后跟变量名，然后将其设置为等于`function() {};`。这样做就创建了一个“匿名”函数。可选地，您可以通过在`function`关键字和括号之间添加一个名称(例如`function myFunction() {}`)来给函数命名(这不同于变量名)。

要执行的代码放在花括号里。因为“函数表达式”被设置为变量，所以它必须以分号结束。下面是匿名函数和命名函数的例子:

```
// anonymous function
var myFunction = function() {
  // do something
};

// named function
// var myReferenceVariable = function myFunction() {
  // do something
} 
```

Enter fullscreen mode Exit fullscreen mode

命名函数表达式对调试很有用。如果一个函数是匿名的，并且发生了错误，控制台将只说“匿名函数”作为堆栈跟踪的一部分。如果命名了一个函数并出现错误，该名称将显示在堆栈跟踪中。

## 功能声明

函数声明以关键字`function`开始，接着是函数名，然后是括号和花括号的开始和结束。函数声明不需要分号——它们在这方面类似于循环和条件。这里有一个例子:

```
function myOtherFunction() {
  // do something
} 
```

Enter fullscreen mode Exit fullscreen mode

## 有什么区别？

这两种创建函数的方法有一个显著的区别，那就是何时可以调用(使用)函数。对于函数表达式，函数必须在文件中使用之前创建。通过函数声明，可以在函数创建之前或之后的任何时候调用函数。

在这个例子中，两个函数都是在被调用之前创建的，一切都按预期运行。

```
var myFunction = function() {
  console.log('inside myFunction');
};
myFunction(); // inside myFunction

function myOtherFunction() {
  console.log('inside myOtherFunction');
}
myOtherFunction(); // inside myOtherFunction 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们来看看如果在函数创建之前调用它，会发生什么。

```
myFunction(); // Uncaught TypeError: myFunction is not a function
var myFunction = function() {
  console.log('inside myFunction');
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
myOtherFunction(); // inside myOtherFunction
function myOtherFunction() {
  console.log('inside myOtherFunction');
} 
```

Enter fullscreen mode Exit fullscreen mode

在表达式示例中，在创建之前调用`myFunction()`会中断脚本。另一方面，在“声明”示例中，`myOtherFunction()`工作得非常好。这背后的原因是 JavaScript 处理变量和函数的方式，但是我将把细节留到另一个教程中。

在此期间，你应该使用哪一个？主要是喜好问题。在某些情况下，您需要一个表达式而不是一个声明，但是大多数时候，您可以自由地使用其中任何一个。请记住，表达式在创建之前是不能被调用的。

## 函数参数和自变量

让我们把术语抛开。对于函数，参数是一个局部变量，充当预期数据的占位符。创建函数时会用到参数。另一方面，当调用一个函数时，你是在给函数传递参数。参数是被传递的实际数据。真的，这类似于函数/方法命名。它们是同一个东西，但是开发人员使用不同的名称来区分它们何时被使用——函数定义中的参数和向函数传递数据时的参数。

当一个函数被创建时，你可以设置一个参数列表——当函数被调用时，期望传递给函数的数据。一个函数可以有任意多的参数，但是，为了可读性和可维护性，您应该尽量减少参数列表。

考虑这个例子:

```
function add(num1, num2) {
  return num1 + num2;
}
var value = add(4, 9);
console.log(value); // 13 
```

Enter fullscreen mode Exit fullscreen mode

`num1`和`num2`是参数。无论`num1`和`num2`是什么，它们都会被加在一起，然后返回值。

函数中的`return`语句是对函数求值的结果。在这种情况下，该函数计算两个数的总和。不是每个函数都会有一个`return`语句。假设您在页面上将一个字符串显示为 HTML 段落。该函数可能显示字符串，仅此而已——它执行一个操作，而不需要返回任何内容。

回到我们的例子。在`var value = add(4, 9);`行中，4 和 9 是传递给函数的参数(实际数据)。该函数评估代码，并将返回值赋给变量`value`。之后，该值被记录到控制台。

## 包装完毕

函数是编程中非常强大的构造。使用最后一个例子，我们可以看到函数的可重用性。

```
console.log(add(13, 12)); // 25
console.log(add(100, 34)); // 134
console.log(add(0, 1)); // 1
console.log(add(5, 72)); // 77 
```

Enter fullscreen mode Exit fullscreen mode

如果没有这个功能，你每次都必须手动添加数字。对于一个简单的加法问题来说，这没什么大不了的，只需将数字相加就可以减少代码量。但是让我们假设您已经有了类型检查。

```
function add(num1, num2) {
  var result = parseInt(num1) + parseInt(num2);
  if (isNaN(result)) {
    return false;
  }
  return value;
}
console.log(add(3, 4)); // 7
console.log(add(5, '8')); // 13
console.log(add(12, 'a')); // false 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的`add`函数更有用了。首先，它将传递的参数转换成数字，然后将它们相加。如果有一个参数不能被转换，那么`parseInt()`返回`NaN`(不是一个数字)。将`NaN`加到任何东西都会产生`NaN`，所以我们的函数返回 false。否则，它将返回值。每次你想把两个数字相加时，都必须输入所有这些内容，这很容易出错，更不用说不必要的重复了。

希望这能让你很好地掌握函数的使用、创建和威力。在下一篇教程中，我们将更深入地研究函数，真正挖掘它们的能力和用途。