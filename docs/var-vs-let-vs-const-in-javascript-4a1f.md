# JavaScript 中的 var vs let vs const

> 原文：<https://dev.to/tylermcginnis/var-vs-let-vs-const-in-javascript-4a1f>

在这篇文章中，你将学习在 JavaScript (ES6)中创建变量的两种新方法，`let`和`const`。在这个过程中，我们将会看到`var`、`let`和`const`之间的区别，以及函数与块范围、变量提升和不变性等主题。

[https://www.youtube.com/embed/6vBYfLCE9-Q](https://www.youtube.com/embed/6vBYfLCE9-Q)

ES2015(或 ES6)引入了两种创建变量的新方法，`let`和`const`。但是在我们真正深入了解`var`、`let`和`const`之间的区别之前，你需要先了解一些先决条件。它们是变量声明 vs 初始化、作用域(特别是函数作用域)和提升。

#### 变量声明 vs 初始化

变量声明引入了一个新的标识符。

```
var declaration 
```

Enter fullscreen mode Exit fullscreen mode

上面我们创建了一个名为 declaration 的新标识符。在 JavaScript 中，变量在创建时用`undefined`的值初始化。这意味着如果我们尝试记录变量`declaration`，我们将得到`undefined`。

```
var declaration

console.log(declaration) // undefined 
```

Enter fullscreen mode Exit fullscreen mode

所以如果我们记录声明变量，我们会得到未定义的。

在变量声明的契约中，变量初始化是指第一次给变量赋值。

```
var declaration

console.log(declaration) // undefined

declaration = 'This is an initialization' 
```

Enter fullscreen mode Exit fullscreen mode

所以这里我们通过将变量`declaration`赋给一个字符串来初始化它。

这就引出了我们的第二个概念，范围。

#### 范围

作用域定义了变量和函数在程序内部的可访问位置。在 JavaScript 中，有两种作用域——**全局作用域**，和**函数作用域**。根据官方规格，

> "如果变量语句出现在 FunctionDeclaration 中，则变量是用该函数中的函数局部范围定义的."。

也就是说，如果你用`var`创建一个变量，这个变量的作用域是创建它的函数，并且只能在这个函数或者任何嵌套函数内部访问。

```
function getDate () {
  var date = new Date()

  return date
}

getDate()
console.log(date) // ❌ Reference Error 
```

Enter fullscreen mode Exit fullscreen mode

上面我们试图访问一个声明的函数之外的变量。因为`date`是`getData`函数的“作用域”,所以它只能在`getDate`本身或者`getDate`内部的任何嵌套函数中被访问(如下所示)。

```
function getDate () {
  var date = new Date()

  function formatDate () {
    return date.toDateString().slice(4) // ✅ 
  }

  return formatDate()
}

getDate()
console.log(date) // ❌ Reference Error 
```

Enter fullscreen mode Exit fullscreen mode

现在我们来看一个更高级的例子。假设我们有一个数组`prices`,我们需要一个函数来接收这个数组和一个`discount`,并返回给我们一个新的折扣价数组。最终目标可能是这样的。

```
discountPrices([100, 200, 300], .5) // [50, 100, 150] 
```

Enter fullscreen mode Exit fullscreen mode

实现可能看起来像这样

```
function discountPrices (prices, discount) {
  var discounted = []

  for (var i = 0; i < prices.length; i++) {
    var discountedPrice = prices[i] * (1 - discount)
    var finalPrice = Math.round(discountedPrice * 100) / 100
    discounted.push(finalPrice)
  }

  return discounted
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来很简单，但是这和块范围有什么关系呢？看一下那个`for`循环。在内部声明的变量可以在外部访问吗？事实证明，的确如此。

```
function discountPrices (prices, discount) {
  var discounted = []

  for (var i = 0; i < prices.length; i++) {
    var discountedPrice = prices[i] * (1 - discount)
    var finalPrice = Math.round(discountedPrice * 100) / 100
    discounted.push(finalPrice)
  }

  console.log(i) // 3
  console.log(discountedPrice) // 150
  console.log(finalPrice) // 150

  return discounted
} 
```

Enter fullscreen mode Exit fullscreen mode

如果 JavaScript 是你唯一知道的编程语言，你可能不会想到这一点。然而，如果你从另一种编程语言，特别是一种被阻塞范围的编程语言开始学习 JavaScript，你可能会有点担心这里发生了什么。不是真的坏了，只是有点奇怪。没有真正的理由仍然可以在`for`循环之外访问`i`、`discountedPrice`和`finalPrice`。它实际上对我们没有任何好处，在某些情况下甚至会给我们带来伤害。然而，因为用`var`声明的变量是函数作用域的，所以需要这样做。

既然我们已经讨论了变量声明、初始化和作用域，那么在深入研究`let`和`const`之前，我们需要做的最后一件事就是提升。

#### 吊装

还记得我们之前说过的“在 JavaScript 中，变量在创建时用`undefined`的值初始化。”。原来，这就是“提升”的全部。JavaScript 解释器将在所谓的“创建”阶段为变量声明分配一个默认值`undefined`。

> 关于创建阶段、提升和作用域的更深入的指导，请参见 [“JavaScript 中提升、作用域和闭包的终极指南”](https://tylermcginnis.com/ultimate-guide-to-execution-contexts-hoisting-scopes-and-closures-in-javascript/)

让我们看一下前面的例子，看看提升是如何影响它的。

```
function discountPrices (prices, discount) {
  var discounted = undefined
  var i = undefined
  var discountedPrice = undefined
  var finalPrice = undefined

  discounted = []
  for (var i = 0; i < prices.length; i++) {
    discountedPrice = prices[i] * (1 - discount)
    finalPrice = Math.round(discountedPrice * 100) / 100
    discounted.push(finalPrice)
  }

  console.log(i) // 3
  console.log(discountedPrice) // 150
  console.log(finalPrice) // 150

  return discounted
} 
```

Enter fullscreen mode Exit fullscreen mode

注意所有的变量声明都被赋予了默认值`undefined`。这就是为什么如果你试图在被声明之前访问其中一个变量**，你只会得到`undefined`。** 

```
function discountPrices (prices, discount) {
  console.log(discounted) // undefined

  var discounted = []

  for (var i = 0; i < prices.length; i++) {
    var discountedPrice = prices[i] * (1 - discount)
    var finalPrice = Math.round(discountedPrice * 100) / 100
    discounted.push(finalPrice)
  }

  console.log(i) // 3
  console.log(discountedPrice) // 150
  console.log(finalPrice) // 150

  return discounted
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你已经知道了关于`var`的一切，让我们最后谈谈你为什么在这里的全部要点，`var`、`let`和`const`之间有什么区别？

#### var VS let VS const

首先我们来对比一下`var`和`let`。`var`和`let`的主要区别在于，`let`不是函数范围的，而是块范围的。这意味着用`let`关键字创建的变量在创建它的“块”以及任何嵌套块中都是可用的。当我说“块”时，我指的是任何被花括号`{}`包围的东西，比如在`for`循环或`if`语句中。

所以让我们最后一次回头看看我们的`discountPrices`函数。

```
function discountPrices (prices, discount) {
  var discounted = []

  for (var i = 0; i < prices.length; i++) {
    var discountedPrice = prices[i] * (1 - discount)
    var finalPrice = Math.round(discountedPrice * 100) / 100
    discounted.push(finalPrice)
  }

  console.log(i) // 3
  console.log(discountedPrice) // 150
  console.log(finalPrice) // 150

  return discounted
} 
```

Enter fullscreen mode Exit fullscreen mode

记住，我们能够在`for`循环之外记录`i`、`discountedPrice`和`finalPrice`，因为它们是用`var`和`var`声明的，并且`var`是函数作用域的。但是现在，如果我们将那些`var`声明改为使用`let`并尝试运行它，会发生什么呢？

```
function discountPrices (prices, discount) {
  let discounted = []

  for (let i = 0; i < prices.length; i++) {
    let discountedPrice = prices[i] * (1 - discount)
    let finalPrice = Math.round(discountedPrice * 100) / 100
    discounted.push(finalPrice)
  }

  console.log(i) // 3
  console.log(discountedPrice) // 150
  console.log(finalPrice) // 150

  return discounted
}

discountPrices([100, 200, 300], .5) // ❌ ReferenceError: i is not defined 
```

Enter fullscreen mode Exit fullscreen mode

🙅‍♀️我们得到`ReferenceError: i is not defined`。这告诉我们，用`let`声明的变量是块范围的，而不是函数范围的。所以试图访问声明它们的“块”之外的`i`(或`discountedPrice`或`finalPrice`)将会给我们一个引用错误，就像我们刚刚看到的那样。

```
var VS let

var: function scoped

let: block scoped 
```

Enter fullscreen mode Exit fullscreen mode

下一个区别与提升有关。前面我们说过提升的定义是“JavaScript 解释器将在所谓的‘创建’阶段为变量声明分配一个默认值`undefined`”我们甚至在声明变量之前通过记录变量看到了这一点(你得到`undefined` )

```
function discountPrices (prices, discount) {
  console.log(discounted) // undefined

  var discounted = []

  for (var i = 0; i < prices.length; i++) {
    var discountedPrice = prices[i] * (1 - discount)
    var finalPrice = Math.round(discountedPrice * 100) / 100
    discounted.push(finalPrice)
  }

  console.log(i) // 3
  console.log(discountedPrice) // 150
  console.log(finalPrice) // 150

  return discounted
} 
```

Enter fullscreen mode Exit fullscreen mode

我想不出任何一个用例，你真的想在变量被声明之前访问它。似乎抛出一个 ReferenceError 比返回`undefined`更好。其实这正是`let`所做的。如果你试图在一个用`let`声明的变量被声明之前访问它，而不是得到`undefined`(就像那些用`var`声明的变量一样)，你会得到一个 ReferenceError。

```
function discountPrices (prices, discount) {
  console.log(discounted) // ❌ ReferenceError

  let discounted = []

  for (let i = 0; i < prices.length; i++) {
    let discountedPrice = prices[i] * (1 - discount)
    let finalPrice = Math.round(discountedPrice * 100) / 100
    discounted.push(finalPrice)
  }

  console.log(i) // 3
  console.log(discountedPrice) // 150
  console.log(finalPrice) // 150

  return discounted
} 
```

Enter fullscreen mode Exit fullscreen mode

```
var VS let

var: 
  function scoped
  undefined when accessing a variable before it's declared

let: 
  block scoped
  ReferenceError when accessing a variable before it's declared 
```

Enter fullscreen mode Exit fullscreen mode

#### 设 VS 常数

既然你明白了`var`和`let`的区别，那么`const`呢？原来，`const`和`let`几乎一模一样。然而，唯一的区别是，一旦你用`const`给一个变量赋值，你就不能再给它赋值了。

```
let name = 'Tyler'
const handle = 'tylermcginnis'

name = 'Tyler McGinnis' // ✅
handle = '@tylermcginnis' // ❌ TypeError: Assignment to constant variable. 
```

Enter fullscreen mode Exit fullscreen mode

上面的要点是，用`let`声明的变量可以重新赋值，但用`const`声明的变量不能。

酷，所以任何时候你想要一个变量是不可变的，你可以用`const`来声明它。不完全是。仅仅因为一个变量用`const`声明并不意味着它是不可变的，它只意味着值不能被重新赋值。这里有一个很好的例子。

```
const person = {
  name: 'Kim Kardashian'
}

person.name = 'Kim Kardashian West' // ✅

person = {} // ❌ Assignment to constant variable. 
```

Enter fullscreen mode Exit fullscreen mode

注意，改变一个对象的属性并不是重新分配它，所以即使一个对象是用`const`声明的，这并不意味着你不能改变它的任何属性。这只意味着你不能把它重新赋值给一个新值。

* * *

现在我们还没有回答的最重要的问题是，你应该用`var`、`let`还是`const`？最流行的观点，也是我赞同的观点，是你应该总是使用`const`，除非你知道变量将要改变。这样做的原因是通过使用`const`，你向你未来的自己以及任何其他未来的必须阅读你的代码的开发者发出信号，这个变量不应该改变。如果它需要改变(比如在`for`循环中)，你应该使用`let`。

所以在变化的变量和不变的变量之间，剩下的不多。这意味着你不应该再使用`var`。

现在，一个不受欢迎的观点，尽管它仍然有一些有效性，是你永远不应该使用`const`,因为即使你试图表明变量是不可变的，正如我们在上面看到的，也不完全是这样。同意这种观点的开发人员总是使用`let`，除非他们有像`_LOCATION_ = ...`这样实际上是常量的变量。

概括一下，`var`是函数作用域的，如果你试图在实际声明之前使用用`var`声明的变量，你只会得到`undefined`。`const`和`let`是阻塞作用域，如果你试图使用在声明前用`let`或`const`声明的变量，你会得到一个 ReferenceError。最后，`let`和`const`的区别在于，一旦你给`const`赋值，你就不能重新赋值，但是用`let`，你可以。

```
var VS let VS const

var: 
  function scoped
  undefined when accessing a variable before it's declared

let: 
  block scoped
  ReferenceError when accessing a variable before it's declared

const:
  block scoped
  ReferenceError when accessing a variable before it's declared
  can't be reassigned 
```

Enter fullscreen mode Exit fullscreen mode

* * *

这篇文章最初发表在 TylerMcGinnis.com 大学，是他们在 T2 现代 JavaScript 课程的一部分。