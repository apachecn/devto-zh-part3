# 只用函数组合子创建一个链表

> 原文：<https://dev.to/joelnet/creating-a-linked-list-using-only-function-combinators-5hhk>

[![ABC written on chalk board](img/6f5388cb7803df49e7a5d8890e804ea2.png)](https://www.pexels.com/photo/abc-books-chalk-chalkboard-265076/)

今天我将演示如何创建一个没有任何数据结构的链表，如`Object`或`Arrays`；相反，使用函数组合子。

我假设你已经熟悉什么是链表。如果你需要复习链表，请查看 [thank u，next:由](https://dev.to/aspittel/thank-u-next-an-introduction-to-linked-lists-4pph) [@aspittel](https://dev.to/aspittel) 撰写的链表简介。

我的目标是给你揭露一些你以前可能没见过的东西。展示 currying、局部应用、闭包和函数组合器的可能性。最重要的是，在做这件事的时候找点乐子。

⚠️:这篇文章嵌入了 runkit。你应该运行、修改、调整和使用本页的例子。

# 什么什么是函数组合子？

来自[的定义从功能上考虑:组合子](https://github.com/louthy/language-ext/wiki/Thinking-Functionally:-Combinators)

> “组合子”这个词用来描述其结果只取决于参数的函数。这意味着不依赖于外部世界，尤其是根本不能访问任何其他函数或全局值。
> 
> 实际上，这意味着组合函数仅限于以各种方式组合其参数。

这太难理解了，所以举几个例子可能会有帮助？

```
/* ☝️ These are combinators */
const I = a => a
const K = a => b => a
const V = a => b => c => c (a) (b)
const B = a => b => c => a (b (c))
//        -    -    -    ---------
//         \   |   /        |
//           arguments   ---
//                      /
//       only arguments are used

/* 👎 These are not */
const nope = a => a.map(double)
//                  --- ------
//                 /           \ 
//                /    ⚠️ reaching outside of the func
//               /
//     ⚠️ can't use map either.
const add => a => b => a + b
//                       -
//                      /
// ⚠️ Uh oh, `+` is not part of 'arguments' 
```

Enter fullscreen mode Exit fullscreen mode

重述上面的代码:一个组合子只能*使用它的参数。这不包括外部函数、方法和操作符！*

别担心，还是有点迷茫没关系。(⊙_☉)

# 舍弃结构

一个典型的链表将会使用如下的数据结构:

```
class Node {
  constructor(data, next) {
    this.data = data
    this.next = next
  }
}

/* or */

const node = (data, next) => ({ data, next })

/* or */

const node = (data, next) => [ data, next ] 
```

Enter fullscreen mode Exit fullscreen mode

但是我们不会使用这些数据结构。我们将使用函数组合子。

在我们进入组合子池的最深处之前，让我们从`node` :
的一个基本函数开始

```
function node (data, next) {
//             ----  ----
//           /            \
//       our data       the next node
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们如何访问`data`和`next`而不像对象一样使用`node`？如果你说`callbacks`，你就对了！

`///////////////////////////////////////////////////////////// // // // 📌 ATTENTION: You can modify and run these code blocks! // // // ///////////////////////////////////////////////////////////// function node (data, next, callback) { return callback(data, next) } // I can use bind to store my data and next values. const head = node.bind(null, 'data', null) // Use a callback to read the values from head. head((data, next) => { return { data, next } })`

我真的不喜欢使用`bind`的实现。所以我要修改`node`函数，这样我就可以使用局部应用来应用`data`和`next`。这与使用`bind`效果相同，但是语法更好。

`const node = data => next => callback => callback (data) (next) // ---- ---- -------- ---- ---- // \ | / / / // parameters are curried ------------- // / // closures make data and next available // to callback when it is finally called. // I can use bind to store my data and next values. const head = node ('data') (null) // ------ ---- // / / // We can partially apply the arguments data and null. // Use a callback to read the values from head. head (data => next => { return { data, next } })`

现在，如果你非常关注，你可能已经注意到`node`和上面的`V`组合子是一样的！

所以现在`node`可以简化为:

```
const node = V 
```

Enter fullscreen mode Exit fullscreen mode

我们可以创建这样的节点:

```
const evenOdd = node ('Even') ('Odd')
const leftRight = node ('Left') ('Right')
const yesNo = node ('Yes') ('No') 
```

Enter fullscreen mode Exit fullscreen mode

如果我们要查看局部应用程序正在做什么的分解，它看起来会像这样:

```
// first copy the node function
const evenOdd = data => next => callback => callback (data) (next)

// apply 'Even' to data.
const evenOdd =         next => callback => callback ('Even') (next)

// apply 'Odd' to next.
const evenOdd =                 callback => callback ('Even') ('Odd')

// We end up with this:
const evenOdd = callback => callback ('Even') ('Odd') 
```

Enter fullscreen mode Exit fullscreen mode

`evenOdd`现在只接受一个参数，即`callback`。`callback`需要一个类似这样的函数:

```
const callback = a => b => { /* ??? */ } 
```

Enter fullscreen mode Exit fullscreen mode

我们现在处于可以开始比赛的阶段。点击运行工具包上的`play`，修改`callback`返回`'Left'`。

`const V = a => b => c => c (a) (b)` `const node = V const leftRight = node ('Left') ('Right') // TODO: modify callback so the code returns 'Left' const callback = a => b => {} leftRight (callback) //=> 'Left'`

现在再次修改代码以返回`'Right'`。

厉害！现在让我们调用`'Left'`函数`data`和`'Right'`函数`next`。

```
const data = a => _ => a
const next = _ => b => b 
```

Enter fullscreen mode Exit fullscreen mode

用我们的新功能再运行一遍。

`const V = a => b => c => c (a) (b)` `const node = V const data = a => _ => a const next = _ => b => b const leftRight = node ('Left') ('Right') console.log (leftRight (data)) console.log (leftRight (next))`

你有没有注意到`data`也和我们的`K Combinator`一样？

```
// 💥 BOOM!
const data = K 
```

Enter fullscreen mode Exit fullscreen mode

`next`和`K Combinator`差不多，但又有点不一样。`next`返回`b`，而`data`返回`a`。这里有一个小技巧:

```
// 🧙‍♀️ MAGIC!
const next = K (I) 
```

Enter fullscreen mode Exit fullscreen mode

这个巧妙的技巧是一整篇文章的灵感来源[你无法解决的最简单的问题](https://dev.to/joelnet/the-easiest-problem-you-cannot-solve-2d3m)。我打赌你现在不到 2 秒就能解决这个问题！

# 链接列表

让我们把我们所学的翻译成一个链表。

`const I = a => a const K = a => b => a const V = a => b => c => c (a) (b)` `const node = V const data = K const next = K (I) const Nil = Symbol('Nil') // Just an Object to detect the end. const first = node ('1st') (Nil) // --- // / // Nil signifies the end const second = node ('2nd') (first) // ----- // / // pass the first node in as the next const third = node ('3rd') (second) // -----_ // / // pass the second node in as the next console.log (third (data)) //=> '3rd' console.log (third (next) (data)) //=> '2nd' console.log (third (next) (next) (data)) //=> '1st'`

# 清点名单

我们可以创建一个简单的函数来枚举列表并返回一个计数。

`const I = a => a const K = a => b => a const V = a => b => c => c (a) (b)` `const node = V const data = K const next = K (I) const Nil = Symbol('Nil') const length = (list, value = 0) => list === Nil ? value : length (list (next), value + 1) const first = node ('1st') (Nil) const second = node ('2nd') (first) const third = node ('3rd') (second) console.log (length (first)) //=> 1 console.log (length (second)) //=> 2 console.log (length (third)) //=> 3`

# 绘制列表

映射类似于`Array`。

`const I = a => a const K = a => b => a const V = a => b => c => c (a) (b)` `const node = V const data = K const next = K (I) const Nil = Symbol('Nil') // Don't worry about this implementation. // It is just to demo the code below. const map = func => list => list === Nil ? list : node (func (list (data))) (map (func) (list (next))) const first = node ('1st') (Nil) const second = node ('2nd') (first) const third = node ('3rd') (second) const upper = x => x.toUpperCase() const THIRD = map (upper) (third) console.log (THIRD (data)) //=> '3RD' console.log (THIRD (next) (data)) //=> '2ND' console.log (THIRD (next) (next) (data)) //=> '1ST'`

# 滤镜

过滤也类似于一个`Array`。

`const I = a => a const K = a => b => a const V = a => b => c => c (a) (b)` `const node = V const data = K const next = K (I) const Nil = Symbol('Nil') // Don't worry about this implementation. // It is just to demo the code below. const filter = predicate => list => list === Nil ? list : predicate (list (data)) ? node (list (data)) (filter (predicate) (list (next))) : filter (predicate) (list (next)) const first = node (1) (Nil) const second = node (2) (first) const third = node (3) (second) const fourth = node (4) (third) const isEven = x => x % 2 === 0 const evens = filter (isEven) (fourth) console.log (evens (data)) //=> 4 console.log (evens (next) (data)) //=> 2`

# 但是函数组合子真的有用吗？

当然，你不应该用这种方式创建链表。实际上，首先你不应该创建一个链表。所以这只是理论上的。

令人惊讶的是，函数组合子还有一些实际用途！

你可能认不出`B Combinator`

```
const B = a => b => c => a (b (c)) 
```

Enter fullscreen mode Exit fullscreen mode

除非是这样写的:

```
const compose = f => g => x => f (g (x)) 
```

Enter fullscreen mode Exit fullscreen mode

没错！`compose`正好是`B Combinator`！如果你好奇的话，`pipe`就是`Q Combinator`。

另一个有用的实用函数是`always`。拉姆达在他们的图书馆里有一个`always`。你也可以用一个简单的函数组合器重新创建它。

```
const always = K

const awesome = always ('Awesome!')

awesome () //=> 'Awesome!'
awesome (123) //=> 'Awesome!'
awesome ('hello') //=> 'Awesome!' 
```

Enter fullscreen mode Exit fullscreen mode

`tap`也是我经常使用的常用功能。可以这样写(如下)。这对控制副作用很有帮助。

```
const tap = func => val => {
  func (val) // execute my side effect
  return val // return the original value
} 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以这样写`tap`:

```
const tap = S (K) 
```

Enter fullscreen mode Exit fullscreen mode

这是许多真正有用的东西，可以用函数组合子来创建！

# 总结

*   我们学习了如何在不使用任何数据结构的情况下创建链表。
*   我们学习了什么是函数组合子，以及它们如何有用。
*   我们学习了如何使用 currying、部分应用程序和闭包来存储数据。

让我知道你可能还学到了什么！

让我知道你对 runkit 例子的看法。我正在考虑在我的文章中加入更多的内容。

你想了解更多关于函数组合子的知识吗？请在评论中告诉我！

如果你喜欢函数式 JavaScript，请在这里或 Twitter 上关注我。

[![Cheers!](img/661c03d25961a6e3b1ecbe00d148b35a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PqqJmrxK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jsy3a866frzp3u5oda0.jpg)