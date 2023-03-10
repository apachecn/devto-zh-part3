# JavaScript 中的函数上下文

> 原文：<https://dev.to/kartik2406/function-context-in-javascript-4mca>

如果你知道任何面向对象的语言，你会非常熟悉这个关键字。在这些语言中[“this”](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)指的是类的当前实例。虽然在 JS 中这是真的，但您可能也看到过它被用在函数内部。这个的值在 JS 中也叫做 context。

函数上下文取决于函数是如何被调用的。同样，arrow 函数没有自己的作用域，它包含的元素的作用域被传递给它。

JavaScript 还允许您使用 call()、apply()、bind()方法来更改函数的上下文。这些方法是存在于函数对象上的属性。

### [调用()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call)方法

使用它，您可以向函数传递上下文和参数。

语法

```
functionName.call(contextObject, funtcion args) 
```

假设您有以下功能

```
function multiplyByN(n) {
  console.log(this.multiplier * n)
} 
```

如果您运行这个，您将得到 NaN 作为输出，因为它没有上下文，使得乘数未定义。

现在，如果你想用 call 给出函数的上下文，你可以做下面的

```
multiplyByN.call(context, n) 
```

所以如果你想用 2 乘以 3

```
multiplyByN.call({ multiplier: 2 }, 3)
// Output: 6 
```

### [敷()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)法

apply 方法以类似的方式工作，但是这里参数作为数组传递。

```
multiplyByN.apply({ multiplier: 2 }, [3])
// Output: 6 
```

### [绑定()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)方法

Bind 返回一个新方法，该方法绑定了上下文的值。
下面是语法

```
boundFunction = functuinName.bind(context) 
```

所以上面的例子会变成

```
multiplyBy2 = multiplyByN.bind({ multiplier: 2 })
multiplyBy2(3)
// Output: 6 
```

在这三个函数中，bind()非常有趣，因为它返回一个带有我们指定的上下文的新函数。这允许您将这个新函数传递给数组方法、回调。这样你就可以拥有可重用的功能块。

让我们来看一个详细的例子。让我们来计算一下你 1 年储蓄的回报金额。
现在这个例子的目的是演示 bind()方法的使用，calcReturns()函数是非常基本的，你可以把它改成你喜欢的任何东西。

```
let savings = [2000, 3000]

let calcReturns = function(ammount) {
  return ammount + ammount * (this.interest / 100)
}

let calWith3percent = calcReturns.bind({ interest: 3 })

savings.map(calWith3percent)

// Output: [ 2060, 3090 ] 
```

如果我们把利息作为参数，这是不可能的，因为数组函数的回调有它自己的参数

或者如果您想检查不同于
的利率

```
let interests = [3, 4, 7]

interests.forEach(interest => {
  let calculate = calcReturns.bind({ interest: interest })
  console.log(savings.map(calculate))
})

// Output: [ 2060, 3090 ] [ 2080, 3120 ] [ 2140, 3210 ] 
```

在计算回报函数中加入一些适当的逻辑，你可以得到一个相当不错的投资计算器。

### 你为什么要使用上下文你可以传递参数给函数？

是的，大多数时候你可以只使用参数，但是正如你从上面的例子中看到的，有时使用上下文将允许你编写非常可重用的代码。你应该检查一下[这篇 StackOverflow 帖子](https://stackoverflow.com/questions/15318904/javascript-what-is-the-benefit-of-using-function-context-vs-passing-as-paramete)以获得更多的信息。

这篇文章到此为止，你可以在我很久以前写的这篇文章中读到更多关于使用 bind()的内容。

如果你喜欢这篇文章，请分享它:)。