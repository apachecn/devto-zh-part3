# 功能组合与组合的制作

> 原文：<https://dev.to/johnboy5358/the-makings-of-compose-7j0>

[![lego blocks](img/16f85cd2a1c6797edfbd2e0f0569c72c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pm5Np5dS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4wuxwt9rft2vfvl251c7.jpg) 
<small>图片由 [StockSnap](https://pixabay.com/users/StockSnap-894430/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2569802) 转自[pix abay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2569802)T9】</small>

## 这是给谁的？

任何对 JavaScript 中的函数式编程感兴趣的人，如果想了解 compose 函数是如何派生出来的，都是为了更好地理解。它不适合有经验的 fp 程序员。我们将在这里创建的 compose 函数甚至不是为生产使用而设计的。

## [T1】简介](#intro)

在这篇文章中，我将采取以下步骤

*   展示 Array.prototype.reduce 的正常用例
*   展示我们的能力。简化一组函数
*   从解耦 reduce 函数生成一般的 compose 函数
*   在一道简单的数学题上测试这篇作文
*   最后，用它来解决 TK 帖子中的购物车示例，见下文

因为我想让这篇文章尽可能的简短，所以我建议如果你不熟悉以下任何一个术语:一级函数、高阶函数、纯函数、currying、部分应用、不变性和折叠，那么请在 DEV...

*   [TK 的 Javascript 函数式编程原理](https://dev.to/leandrotk_/functional-programming-principles-in-javascript-26g7)
*   [学会折叠你的 JS 数组](https://dev.to/mebble/learn-to-fold-your-js-arrays-2o8p)

此外，请阅读乔尔托马斯关于 hackernoon.com 方法解耦的帖子。

*   Joel Thoms 的《函数式 JavaScript:从对象中分离方法》

所以现在你知道接下来会发生什么，剩下的就是细节了。

## 简单的开始

以下是 JS
中 reduce 的正常用例

```
 const  sum = (a, b) => a + b

const  sumIntegers = [1,2,3,4,5].reduce(sum, 0)
// => 15 
```

这里我们对整数列表求和。在代码顺序中，我们用回调函数将应用于 reduce 方法的整数数组的结果赋给 sumIntegers，或者“ ***reducer*** ”，sum 最后我们必须提供一个空的 ***或恒等值*** 作为最后一个参数来减少。理解*相同值*与它所服务的减速器有关是很重要的。对于 sum reducer，标识为 0:

```
0 + (a number) = that number
ie.  0 + 1 = 1,
and 0 + 2 = 2 
```

对于乘法运算，相同值为 1:

```
1 x (a number) = that number
ie. 1 x 2 = 2
and 1 x 4 = 4 
```

这意味着相同值**对归约的第一次迭代**没有影响。

## 让我们把它提升一个档次

在 JS 中，函数是[第一类](https://hackernoon.com/javascript-and-functional-programming-pt-2-first-class-functions-4437a1aec217)。它们本质上可以像任何其他数据类型一样对待，并且可以被简化或折叠，就像上面的整数列表一样；在 fp 中，当应用于函数时，这就是所谓的函数组合。

但是，如果我们要在一组函数上使用 reduce，那么我们的 reduce 是什么，它的相关标识值是什么？事实证明，它们都必须是函数，因为我们在化简函数。这个归约器通常被称为 **composeB** ，而空值被称为**恒等式**。顺便提一下，这些作为一组函数的一部分被称为**组合子**。看看这个 github 页面上常见的[组合子](https://gist.github.com/Avaq/1f0636ec5c8d6aed2e45)，B 或 composeB 组合子是我们将在下一步使用的(和 Identity 一起)。B 代表蓝鸟，来源于数学家 Raymond Smullyan 的一本书，他写了一本叫做 T10 的书来嘲笑一只知更鸟。一本关于组合逻辑的书。

应该注意的是，composeB 被**限制为**只接受**的**两个函数，而我们将在这里构建的 compose 函数将接受两个以上函数的列表。

那么 JS 中的 composeB 和 Identity 是什么样子的呢？他们在这里...

```
const  Identity  =  x  =>  x
// pass in a value and you get the same value back.

const  composeB  = (f, g) =>  x  =>  f(g(x))
// composes two functions together. Pass in a function f and a function g then apply g to data x and then run the result of that through f to get a final result. 
```

现在我们可以将这两个函数应用于 Array.prototype.reduce，为了便于演示，让我们将传入的所有数字加 5，然后将结果翻倍。

为此，我们需要...

```
const  add = a => b => a  +  b
// add is a curried function

const  add5 = add(5)
// make a new function add5

const  double = a => a * 2
// it speaks for itself 
```

compose 的特点是从右向左读，所以...

```
const add5ThenDouble  = [double, add5].reduce(composeB, Identity)

console.log(typeof  add5ThenDouble)
// => function 
```

所以，现在我们有了一个**add 5 tendouble**函数，让我们应用它...

```
console.log(add5ThenDouble(6))

// => (6 + 5) * 2 => 22 
```

它是可行的，但是等一下，我们真正想要的是一个叫做 compose 的通用函数，它将接受一个函数列表。为此，我们需要分离。从数组中还原。原型如下...

```
const reduce = (f, i) => coll => Array.prototype.reduce.call(coll, f, i) 
```

你可以看到这是一个固定的函数:double = >告诉我们这一点。我们获取 reducer 函数及其空值，然后是集合(JS 中的一个数组)

注意:注意我们的函数是如何将数据作为最后一个参数的。这很重要。

因此，应用它来制作一个通用的合成函数...

```
const compose = reduce(composeB, Identity) 
```

现在我们有了它，让我们用 compose 再做一次上面的工作...记住这个 compose 是从 reduce 派生出来的，所以它需要一个由 **[纯](https://hackernoon.com/javascript-and-functional-programming-pt-3-pure-functions-d572bb52e21c)** 函数组成的数组。

```
const  add5_ThenDouble = compose( [double, add5] )
// Remember, it reads right to left

console.log(add5_ThenDouble(6))
// => 22 
```

## 手中多了一只青鸟...

现在我们已经得到了我们的组合函数，让我们用它来解决 TK 帖子中的最后一个例子

[TK 的 Javascript 函数式编程原理](https://dev.to/leandrotk_/functional-programming-principles-in-javascript-26g7)

为此，我们首先需要购物车数据

```
 let  shoppingCart  = [
  { productTitle: "Functional Programming", type: "books", amount: 10 },
  { productTitle: "Kindle", type: "eletronics", amount: 30 },
  { productTitle: "Shoes", type: "fashion", amount: 20 },
  { productTitle: "Clean Code", type: "books", amount: 60 }
] 
```

咖喱功能会很好...

```
const  curry = f => (...args) =>
  args.length >=  f.length
  ? f(...args)
  : curry(f.bind(undefined, ...args)) 
```

几个其他解耦的功能，由库里包装。

```
const map = curry((fn, xs) => Array.prototype.map.call(xs, fn))
const filter = curry((fn, xs) => Array.prototype.filter.call(xs, fn)) 
```

最后，TK 的助手函数。

```
const sumAmount = (acc, amount) => acc + amount
const byBooks = (order) => order.type === "books"
const getAmount = (order) => order.amount 
```

这个问题的目标是找出“books”类型的金额总和。

注意:这个版本的 compose 需要一个函数数组。这与您可能在像 Ramda 这样的函数库中找到的不同。在 Ramda 的 [compose](https://ramdajs.com/docs/#compose) 中，你要合成的函数只是直接作为 compose 函数的参数使用。

```
const sumAmountsForBooks = compose([
  reduce(sumAmount,0),
  map(getAmount),
  filter(byBooks)
])
// reads bottom to top

console.log(sumAmountsForBooks(shoppingCart))
// => 70 
```

因此，我们得到了正确的结果，但让我们看看它如何处理更大的数据集。

## 测试，测试，测试...

```
// generate a test array, quick and dirty
function genTestAry(numElements) {
  let A = [], i=0, prod = ["books", "eletronics", "fashion", "diy", "health"]
  while (i < numElements) {
    A.push({productTitle:'', ['type']: prod[~~(i%5)], amount: 10})
    i++
  }
  return  A
}

// sCart with 100,000 objects.
const sCart = genTestAry(100000)

console.time('sumAmountsForBooks')
console.log("Sum for ", sCart.length, "objects:", sumAmountsForBooks(sCart))
// => Sum for 100000 objects: 200000
console.timeEnd('sumAmountsForBooks')
// reports approx 15 .. 10ms. Not bad for 100,000 items!
// As a bit of perspective; the blink of an eye is approx. 300 .. 400ms. 
```

## 总结

作为一个学术练习，这篇文章已经展示了 compose 函数可以被看作是一系列纯函数的简化。功能组合的过程通常被比作将一组乐高积木组合在一起，以构建一个大于其单个部分的总和。

当然，通常情况下“剥猫皮的方法不止一种”。上面的购物车代码可以一次性完成，如下所示...

```
const  getTotalAmount = (shoppingCart) =>
  shoppingCart.reduce((acc, obj) =>
  obj.type === 'books'
    ? acc + obj.amount
    : acc
  ,0)

console.time('getTotalAmount')
console.log("Sum for ", sCart.length, "objects", getTotalAmount(sCart))
console.timeEnd('getTotalAmount')
// Yes, reports much faster approx. < 5ms. 
```

是的，这段代码更短，它使用。链接并没有错；我喜欢点链接，但这并没有给我机会展示 compose 如何完成这项工作，以及 JavaScript 方法如何与其父对象分离。为此向乔尔·托马斯大声欢呼。但是，结果是，无论您决定使用什么方法，都没问题。但是函数组合是程序员工具箱中的另一个工具...这是一个积极的结尾。

我希望这篇文章能帮助你更好地理解函数组合，并且你会被诱惑去探索一个像 Ramda 或 Lodash 这样的函数库；面向函数式程序员的“实战测试”函数。然而，您可能只需要在没有库的情况下进行编写，所以在普通的 JavaScript 中，您可以使用下面的代码。

```
 const compose = (...fns) => xs => fns.reduceRight((v, f) => f(v), xs) 
```

如果你想学得更多，那么看看 TK 的[学习函数式编程库](https://github.com/LeandroTk/learning-functional-programming)

祝你的 fp 之旅好运！