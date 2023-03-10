# 调用、应用、绑定-基本用法

> 原文：<https://dev.to/alexantoniades/call-apply-bind-the-basic-usages-5gpl>

[![](img/bb3fc0ca30b8a082d6ff96cb05da6a9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sVu3rVlQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aekd2c0xiy9hm2oxufk8.png)

在本文中，我将向您展示调用/应用和绑定的一些基本用法以及它们所解决的问题。我还将向您展示一些实际的例子，以便开始在您的项目中使用它们。

调用/应用和绑定都是函数原型中的方法。这两种方法做的是同样的事情，但略有不同。他们实际上做的是允许我们用给定的上下文和参数调用一个函数。它们让我们调用一个函数并访问另一个函数或对象的属性。我们可以借用一个对象原型的方法，并将其用于另一个对象，例如，我们可以将 Array 的 *slice* 方法应用于一个字符串，或者使用 Math 的 *max* 来查找数组中一组数字的最大值。

**什么是“这个”？**

我不会在这里深入讨论太多细节，但是理解*这个*关键字对于理解调用/应用&绑定的概念是至关重要的。

javascript 中的一切都是对象，函数也是对象(一级对象)。每个对象(或函数)都有一个分配给它的 *this* 对象。*这个*对象作为对象的变量和方法的引用:

```
let obj = {
  name: "Alex",
  getName: function() {
    return `My Name is: ${this.name}`;
  }
}

obj.getName(); // "My Name is: Alex" 
```

Enter fullscreen mode Exit fullscreen mode

*this.name* 是对对象的【obj】*name*属性值的引用。换句话说*这个*指的是这个特定物体的属性。

但是如果我们试图访问对象外部的 *name* 变量会怎么样呢？

```
let name = "Alex";
let obj = {
  getName: function() {
    return `My Name is: ${this.name}`;
  }
}

obj.getName(); // My Name is: Undefined 
```

Enter fullscreen mode Exit fullscreen mode

现在我们得到了未定义的 T1，因为 T2 的名字 T3 已经不在我们的局部变量环境中了。

**调用方法**

调用接受一个*这个*值和一个参数列表:

```
Function.call(this, arg1,arg2,...) 
```

Enter fullscreen mode Exit fullscreen mode

假设我们有一个对象，它有一个 *food* 属性键和一个*favood*函数，该函数接受一个字符串作为参数。为了让 *favFood* 访问对象的 *food 键，我们需要使用 call 方法调用 favFood，并给它 *obj* 的 _this* 上下文。简单地说，我们需要将*对象*附加到*收藏夹:*

```
let obj = { food: "Pizza" }

function favFood(text) {
  return `${this.food} ${text}`;
}

let text = "is awesome!";
favFood.call(obj, text); // "Pizza is awesome!" 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们刚刚将 object ( *obj* )作为第一个参数传递给了 call 方法——因此它是 context(*this*)——并将一个参数作为第二个参数传递给了我们的 *favFood* 函数。这样我们就可以访问这个特定对象的任何方法和属性( *obj* )。

我们也可以传递用逗号分隔的多个参数。

**应用方法**

Apply 与 call 相同，但 apply 接受单个参数数组。

```
Function.apply(this, array) 
```

Enter fullscreen mode Exit fullscreen mode

```
let obj = { 
  add: function(a,b,c) {
    return a + b + c;
  }
}

function sumItUp(a,b,c) {
  return this.add(a,b,c);
}

let numbers = [1,2,3];
sumItUp.apply(obj, numbers); // 6 
```

Enter fullscreen mode Exit fullscreen mode

**绑定方法**

关于 bind 的棘手之处在于，它具有与 apply 相同的功能，但是它不是立即调用函数，而是返回一个绑定函数:

```
let obj = { 
  add: function(a,b,c) {
    return a + b + c;
  }
}

function sumItUp(numbers) {
  return this.add(...numbers);
}

let numbers = [1,2,3];
let bound = sumItUp.bind(obj); // Returns a bound function
bound(numbers) // 6 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们将 *obj* 传递给 sumItUp 函数——以便访问 *obj* 上下文——然后我们调用绑定函数并传递一个数字数组作为参数。bind 的好处在于，您可以在任何需要的时候调用返回的绑定函数。

**借用原型方法**

这些方法(调用、应用、绑定)的酷之处在于我们可以从其他对象的原型借用方法和功能。

**从数学对象中借用 Max**

假设我们有一个数字数组，我们需要找到数组中的最大数字:

```
let numArray = [1,3,4,5,6]; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们知道, *Math* 对象有一个方法可以找到一列数字的最小值和最大值，但是数组不被支持，因为它们不被认为是数字，因此不是一个有效的参数。如果我们尝试:

```
let numArray = [1,3,4,5,6];
Math.max(numArray); // NaN 
```

Enter fullscreen mode Exit fullscreen mode

我们将得到 NaN(不是一个数字)，这是完全正常的，因为数组不是一个数字:

```
Array === Number // False 
```

Enter fullscreen mode Exit fullscreen mode

但是最酷的部分来了，通过使用 apply 我们可以把数组作为参数传递给 *Math* 对象，就像这样:

```
let numArray = [1,2,3,4,5];
Math.max.apply(null, numArray); // 5 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们将空值作为第一个参数传递，因为我们不需要将任何上下文传递给 *max* 方法，相反，我们只使用第二个参数来传递我们的数组，该数组将被转换为参数，并最终被 max 作为有效参数接受。

**从数组对象中借用过滤器**

现在，假设我们有一些字母，我们想过滤掉其中的一些，并使用数组的 filter 方法将它们存储在一个数组中。

```
let letters = 'abcdef';
let arrayFilteredLetters = letters.filter(letter => letter);
console.log(arrayFilteredLettes); // Filter is not a function. 
```

Enter fullscreen mode Exit fullscreen mode

我们得到的*过滤器不是一个函数*，因为*字母*是一个字符串对象，因此它不能访问数组的*过滤器*方法。我们可以做的是再次使用*调用*来调用*过滤器*并传递*字母*作为参数。

```
let letters = 'abcdef';
let arrayFilteredLetters = Array.prototype.filter.call(letters, letter => letter !== 'c' && letter !== 'd');
console.log(arrayFilteredLetters); // [ 'a', 'b', 'e', 'f' ] 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们可以使用 call/apply & bind 从一个对象的原型借用方法，并将其用于另一个对象。这是 call/apply & bind 最酷的应用之一。

**关于箭头功能的重要说明**

在箭头函数的情况下，我们的方法:Call/Apply & Bind 不能像预期的那样工作。

正如 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) 的文档所述:

*"由于箭头函数没有自己的 this，方法 call()或 apply()只能传入参数。此参数被忽略。*

箭头函数没有自己的*这个*。 *This* 是词汇绑定的，它使用调用 arrow 函数的上下文的 *this* 。调用/应用&绑定只能用于传递参数。

**结论**

到目前为止，您应该能够理解 call/apply & bind 的基本用法和应用，并且能够将不同的上下文附加到函数和对象上。您还可以从其他原型借用方法，并使用它来传递不相关的值——比如使用 array 的 filter 在字符串上循环

在 javascript 中，做一件事有无数种方法。上面所有的例子都有很多不同的语法和方法来达到相同的效果。在这篇文章中，我决定使用简单的例子，以确保你以最简单的方式获得基础知识。

玩得开心！