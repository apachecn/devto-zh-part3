# 解构地图、过滤和简化

> 原文：<https://dev.to/joelnet/deconstructing-map-filter-and-reduce-1d1a>

[![Disassembled Camera](img/4fb969222c9e6756c1c6e0b2003514aa.png)](https://pixabay.com/en/digital-camera-disassembly-336670/)

今天我们将通过从零开始解构和重建来掌握`map`、`filter`和`reduce`。

当我小的时候，我收到一只手表作为礼物。令我母亲非常害怕的是，我做的第一件事是抓起我能找到的最小的螺丝刀，一点一点地把它拆开。我想看看内部，检查每一个部分。

令我母亲欣慰的是，我能够把手表恢复到原来的工作状态。彻底检查了内部结构后，我带着对手表运转原理的更好理解离开了。

[![Pulp Fiction watch scene with Captain Koons](img/794b2f690ff3dd356f738f2f418a2d32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OvA4hErg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u3oy3w6o82oif1121nkm.jpg)

今天，我仍然喜欢把东西拆开，以便更好地理解它们。我也鼓励你这样做。

先从外部看`reduce`。我马上就能辨认出四个部分。`array`、`method`、`reducer`和一个`initialValue`。

```
const items = [ 1, 2, 3, 4 ]
const initialValue = 0
const reducer = (accumulator, currentValue) => accumulator + currentValue
items.reduce(reducer, initialValue) //=> 10
/* \     \      \          \
  array   \      \           - initial value
        method    \
                reducer
*/ 
```

一切都是不言自明的。除了`reducer`以外的一切。这需要进一步细分。

注意:减速器有 4 个参数，现在我们将忽略最后 2 个参数，并将重点放在`accumulator`和`currentValue`上。

这些参数通常缩写为`acc`和`cur`。

```
const reducer = (acc, cur) => acc + cur 
```

因为您已经熟悉 for 循环，我可以使用下面的 for 循环来帮助演示什么是`accumulator`和`currentValue`以及它们是如何使用的。

```
const items = [ 1, 2, 3, 4 ]
let acc = 0
//         \
//       initial value
for (let i = 0; i < items.length; i++) {
  const cur = items[i]
//        \
//     current value
  acc = acc + cur
//     \
//   update the accumulator
} 
```

并插入`reducer`...

```
for (let i = 0; i < items.length; i++) {
  const cur = items[i]
  acc = reducer(acc, cur)
} 
```

如果你想看到更多这样的分解，查看 [Map，Filter，Reduce vs For Loops(语法)](https://dev.to/joelnet/map-filter-reduce-vs-for-loops-syntax-2k5l)。

# 累加器

在上面的例子中，`accumulator`是一个`Number`，但是它不一定是一个`Number`，它可以是任何类型。

在这个例子中，`acc`是一个`Array`，`reducer`将一个双精度值推入`accumulator`。

```
const items = [ 1, 2, 3, 4 ]

const reducer = (acc, cur) => {
  acc.push(cur * 2)
  return acc
/*         \
   The reducer must always return the accumulator
*/       
}

let acc = []

for (let i = 0; i < items.length; i++) {
  const cur = items[i]
  acc = reducer(acc, cur)
}

acc //=> [ 2, 4, 6, 8 ] 
```

在本例中，`accumulator`是一个对象，新值被添加到该对象中。

```
const items = [ 1, 2, 3, 4 ]

const reducer = (acc, cur) => {
  acc[cur] = cur * 2
  return acc
}

let acc = {}

for (let i = 0; i < items.length; i++) {
  const cur = items[i]
  acc = reducer(acc, cur)
}

acc //=> { 1:2, 2:4, 3:6, 4:8 } 
```

您应该注意到，在这些示例之间，for 循环代码是相同的。不相信我？继续向前滚动并检查！只有`initialValue`和`reducer`改变了。所以不管`accumulator`是`Number`、`Array`、`Object`还是其他类型...你只需要改变`initialValue`和`reducer`，而不是回路！

# 减少

因为我们知道 for 循环不会改变，所以很容易将其提取到自己的函数中，`reduce`。

```
const reduce = () => {
  for (let i = 0; i < items.length; i++) {
    const cur = items[i]
    acc = reducer(acc, cur)
  }
} 
```

你的棉绒应该抱怨缺少`reducer`和`items`，所以让我们添加它们。我们还会添加一个`initialValue`。

```
const reduce = (items, reducer, initialValue) => {
  let acc = initialValue
  for (let i = 0; i < items.length; i++) {
    const cur = items[i]
    acc = reducer(acc, cur)
  }
  return acc
} 
```

就这样吗？我们刚刚创造了`reduce`？似乎太简单了！

嗯，我们确实忽略了`reducer`中的那两个额外参数。另外，`reduce`中的`initialValue`应该是可选的，但在我们的版本中是必需的。我们稍后会谈到这一点。

# 地图

可以说`map`是`reduce`的派生物。在这种情况下，我们可以从上面使用我们的`reducer`，将它传递给`reduce`，并提供一个初始值`[]`。初始值是`[]`，因为我们的结果将是`Array`。

```
const map = (items, func) => {
//                    |
//        function to modify value
  const initialValue = []
  const reducer = (acc, cur) => {
    acc.push(func(cur))
//            |
//      execute func on the currentValue
    return acc
  }
  return reduce(items, reducer, initialValue)
}

const double = x => x * 2

map(items, double) //=> [ 2, 4, 6, 8 ] 
```

# 滤镜

`filter`和`map`几乎一模一样。我们只需更改`reducer`，根据`predicate`的结果过滤值。

```
const filter = (items, predicate) => {
//                         |
//       if truthy, append to accumulator
  const initialValue = []
  const reducer = (acc, cur) => {
    if (predicate(cur)) {
//         |
// run predicate on currentValue
      acc.push(cur)
    }
    return acc
  }
  return reduce(items, reducer, initialValue)
}

const isEven = x => x % 2 === 0

filter(items, isEven) //=> [ 2, 4 ] 
```

# 其他特性

`reduce`中的`initialValue`应该是可选的。我们应该能够这样做，并得到一个结果`10`，而不是得到`NaN`。

```
const add = (acc, cur) => acc + cur

const items = [ 1, 2, 3, 4 ]

reduce(items, add) //=> NaN 
```

你如何使`initialValue`成为可选的？在评论中展示你的代码。

我在上面提到过，一个缩减器需要 4 个参数。所有 4 个参数都是:

*   累加器(累加器)
*   当前值(当前值)
*   当前索引
*   源数组(源)

我们已经实现了`accumulator`和`currentValue`。你将如何实现`currentIndex`和`source`？在评论里给我看看你的代码。

# 额外学分

修改`reduce`以同时使用`Array`和`Iterator`。这是`Array`的 reduce 做不到的。

```
// range is an Iterator.
const range = require('mojiscript/list/range')

const reduce = (items, reducer, initialValue) => {
  let acc = initialValue
  for (let i = 0; i < items.length; i++) {
    const cur = items[i]
    acc = reducer(acc, cur)
  }
  return acc
}

const add = (acc, cur) => acc + cur

// Make this return 10
reduce(range(0)(5), add, 0) 
```

创建一个`reduceWhile`函数。这就像`reduce`，但是需要一个额外的函数，当给定的条件满足时，这个函数将中断迭代。请将此视为 for 循环中的`break`。

```
const predicate = (acc, cur) => acc + cur < 7

const reduce = (items, predicate, reducer, initialValue) => {
  /* solution goes here */
} 
```

# P.S

本文以特定的方式对参数进行了排序，以便于初学者阅读。但是如果我要设计这些函数是 FP 友好的，我会这样排序参数:

*   述语
*   还原剂
*   初始值
*   目录

# 总结

在解构了`map`、`filter`和`reduce`以了解他们的内在秘密后，他们变得更容易接近我们。

很容易看到，通过构建自己的`reduce`，您可以扩展特性，比如能够支持`Iterator`或提前中断。通过支持一个`async Iterator`和一个`async reducer`，我在 [MojiScript 的`reduce`](https://mojiscript.js.org) 上走得更远。

你有什么想让我说得更详细的吗？看了这篇文章，你学到了什么吗？请在评论中告诉我！

如果你喜欢函数式 JavaScript，请在这里或 Twitter 上关注我。

[![Cheers!](img/661c03d25961a6e3b1ecbe00d148b35a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PqqJmrxK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jsy3a866frzp3u5oda0.jpg)