# 减少

> 原文：<https://dev.to/homam/reduce-213>

最近，我需要解析一个半结构化的长文本文档，并将其转换成数据结构。作为一个懒惰的程序员，我不想手工复制和粘贴文本一千次。我的解决方案非常简单:逐行读取文档，跟踪数组中我尚未成功解析的每一行，并尝试在每次迭代结束时解析数组，并在每次解析成功时清空数组。并重复直到 EOF。

这是解析器通常的工作方式。我的小技巧很容易做到，只是因为我在 reduce 函数中包含了我的逻辑。

这个经历提醒了我，我必须写一下 reduce 函数的功能和效用。

如果你要加入函数式编程的行列，请阅读这篇文章。

* * *

## 累加数字

让我们创建一个函数来对数组中的数字求和。(您可以在您的浏览器控制台中尝试这些片段)

```
let oneToTen = [1,2,3,4,5,6,7,8,9,10]

let sum = function(arr) {
  let acc = 0 // the accumulated sum
  for(var i = 0; i < arr.length; i++) {
    let a = arr[i] // ith item in the array
    acc += a
  }
  return acc
}

sum(oneToTen) 
```

简单，可以！但是像编程中的大多数事情一样，有一种更好的方式来做这件事:

```
oneToTen.reduce((acc, a) => acc + a, 0) 
```

功能非常强大，如果你是第一次看到它，它看起来确实很神奇。

Reduce 还有很多其他的名字:聚合。NET Linq，fold in [Scala](https://medium.com/@mattmichihara/scala-folds-1989244d4fa4) ，foldl in [Haskell](https://wiki.haskell.org/Foldr_Foldl_Foldl') ， [Erlang](http://erlang.org/doc/man/lists.html#foldl-3) ， [accumulate](https://www.geeksforgeeks.org/numeric-header-in-c-stl-set-1-accumulate-and-partial_sum/) in C++。在维基百科第页的文件夹[查看完整列表。](https://en.wikipedia.org/wiki/Fold_(higher-order_function))

在 JavaScript 中 [Array.prototype.reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 接收两个参数。第一个是函数，第二个参数是归约过程的初始值(或种子)(这里是 0)。

下面是上面代码的一个更详细的版本:

```
oneToTen.reduce(function(acc, a) {
  return acc + a;
}, 0) 
```

您可以将此版本中的`acc`和`a`变量与之前循环版本中类似命名的变量进行比较。

那么它是如何工作的呢？

reduce 中的函数(我们称之为 reduction 或 aggregation 函数)被调用多次，数组中的每个项目只被调用一次。这与 for 的主体内部的操作非常相似。在每一步，reduction 函数通过对先前的累积值(`acc`)和数组`a`中的当前项求和来返回当前的累积值。

让我们添加一些日志来看看每一步的结果:

```
let oneToTen = [1,2,3,4,5,6,7,8,9,10]

oneToTen.reduce((acc, a) =>  {
  console.log(`acc = ${acc}, a = ${a}`)
  return acc + a
}, 0) 
```

`reduce`是循环操作的抽象。我们可以将数组上的任何操作转换为 reduce。

可能计算数组中的项数是我们用数组做的最简单也是最常见的事情之一。JavaScript 数组原生支持`Array.prototype.length`。但是由于这是对数组的操作，我们也可以使用 reduce 来计算数组的大小:

```
['a', 'b', 'c', 'd', 'e'].reduce((acc, _a) => acc + 1, 0) 
```

数组的长度不依赖于数组中每一项的实际值。这就是为什么我们在上面的代码中不使用参数 *`_a`* 的原因。

这里 reduce 的种子值是 0；如果它所操作的数组为空，reduce 将返回种子值。

当然，您应该在生产代码中继续使用`Array.prototype.length`和大多数原生数组函数。或者使用像[拉姆达](https://ramdajs.com/)这样的库。这里的许多例子是为了展示 reduce 函数的通用性和强大功能。

到目前为止，我们看到的归约操作产生了一个数字结果。现在让我们检查字符串连接。

Standard `Array.prototype.join`使用它的参数连接一个字符串数组，并返回连接的字符串。我们也可以使用 reduce:
来定义它

```
['reduce', 'is', 'cool'].reduce((acc, a) => acc + '  ' + a, '')

// " reduce is cool" 
```

请注意字符串开头的额外空格。

我们有额外的空间，因为我们用空字符串开始减少。第一个`acc`的值是初始空字符串。然后在 reduction 函数中我们添加了一个空格，然后是单词`"reduce"` :

```
['reduce', 'is', 'cool'].reduce((acc, a) => {
  console.log(`acc = '${acc}', a = '${a}'`)
  return acc + '  ' + a
}, '')

// " reduce is cool" 
```

我们可以通过不向 reduce:
传递任何初始值来轻松解决这个问题

```
['reduce', 'is', 'cool'].reduce((acc, a) => acc + '  ' + a)

// "reduce is cool" 
```

但是我认为这个实现也是有问题的，因为它对于空数组是失败的。

我们可以使用 if 表达式来处理不必要的空格。我们检查 acc 是否等于空字符串(这意味着我们在第一次迭代中):

```
['reduce', 'is', 'cool']
  .reduce((acc, a) => acc === '' ? a : acc + '  ' + a, '') 
```

如果你不习惯 JavaScript 中的 if-then-else 表达式，上面的代码相当于这个:

```
['reduce', 'is', 'cool'].reduce((acc, a) => {
  if(acc === '') {
    return a;
  } else {
    return acc + '  ' + a;
  }
}, '') 
```

我在这里更喜欢 if-then-else 表达式，因为它们确保我不会忘记 else 子句。本教程中的每个 if 都需要一个 else。

我也总是传递一个种子值来减少函数。

我们可以创建 join 函数:

```
function join(c, arr) {
  return arr.reduce((acc, a) => {
    if(acc === '') {
      return a;
    } else {
      return acc + c + a;
    } 
  }, '')
}

join('*', ['reduce', 'is', 'cool']) 
```

或者更简洁地说:

```
let join = (c, arr) => arr.reduce(
   (acc, a) => (acc === '' ? '' : acc + c) + a
 , '') 
```

* * *

## 数组函数

让我们从 **`map`** :
开始探索用 reduce 定义一些基本的数组操作

```
let map = (f, arr) => arr.reduce((acc, a) => { 
  const mappedA = f(a) // apply f to the current item in the array
  return acc.concat([mappedA])
},[]) 

// the initial seed is an empty array, this is the result of reduction if the input array is empty

map(x => x * 2, oneToTen)

// [2, 4, 6, 8, 10, 12, 14, 16, 18, 20] 
```

还有 **`filter`** :

```
let filter = (f, arr) => arr.reduce((acc, a) => {
  const include = f(a)
  return include ? acc.concat([a]) : acc
}, [])

filter(
    x => x.startsWith('A')
  , ['Orange', 'Apple', 'Pearl', 'Avocado', 'Pomegranate']
)

// ["Apple", "Avocado"] 
```

我们现在可以看到模式。

identity 只是用它接收到的数组的完全相同的元素创建数组，而不做任何其他操作:

```
let identity = arr => arr.reduce((acc, a) => acc.concat([a]), [])

identity(['a', 'b', 'c', 'd', 'e', 'f'])

// ['a', 'b', 'c', 'd', 'e', 'f'] 
```

现在让我们用 reduce 来定义反函数。检查其定义与身份有何不同:

```
let reverse = arr => arr.reduce((acc, a) => [a].concat(acc), [])

reverse(['a', 'b', 'c', 'd', 'e', 'f'])

// ["f", "e", "d", "c", "b", "a"] 
```

**`take`** 返回数组中的前 N 项作为新数组:

```
let take = (howMany, arr) => arr.reduce(
   (acc, a) => acc.length === howMany ? acc : acc.concat([a])
 , []
)

take(3, ['a', 'b', 'c', 'd'])

// ['a', 'b', 'c'] 
```

**`head`** 是返回数组中第一项的函数(类似于`arr[0]`)。last 返回数组的最后一项:

```
let head = arr => arr.reduce((acc, *_a*) => acc)

let last = arr => arr.reduce((*_acc*, a) => a)

head(['a', 'b', 'c', 'd']) // "a"

last(['a', 'b', 'c', 'd']) // "d" 
```

和一点理智检查:

```
head(reverse(['a', 'b', 'c', 'd'])) === last(['a', 'b', 'c', 'd'])

// true 
```

**`drop`** 函数删除数组中的前 N 项并返回其余项。我们可以使用 take 和 reverse 定义 drop:

```
let drop = (howMany, arr) => {
  const reversedArr = reverse(arr)
  const topN = take(arr.length - howMany, reversedArr)
  return reverse(topN)
}

drop(3, ['a','b','c','d','e']) // ["d", "e"] 
```

这个定义效率不是很高，因为我们遍历数组三次:(`reverse`、`take`、`reverse`)。

我们可以简单地对数组中的项进行计数，排除索引小于 N 的项:

```
drop = (howMany, arr) => arr.reduce(
  (acc, a) => {
    // current index in array
    const currentIndex = acc.currentIndex + 1 

    const result = currentIndex >= howMany 
      ? acc.result.concat([a])
      : acc.result
    return {currentIndex, result}
  }
  , {currentIndex: -1, result: []} //the initial seed of aggregation
)
.result

drop(3, ['a','b','c','d','e']) // ["d", "e"] 
```

记住 JavaScript 数组索引从 0 开始。

这里归约过程的初始(种子)值不是一个简单的数组或空字符串或数字 0，而是一个具有两个字段的对象:

```
{currentIndex: -1, result: []} 
```

请注意，aggregation (reduction)函数返回一个类似的对象。

`currentIndex`保存数组中项目的计数。

记录我们缩减过程的结果。

在还原结束时`currentIndex`等于数组的长度减一，result 包含 drop 操作的最终结果。

这个实现只遍历数组一次。

我们可以使用去结构化来使这个函数变得更短，并根据你的喜好增加或减少可读性:

```
drop = (howMany, arr) => arr.reduce(
 ({ currentIndex, result }, a) => 
  currentIndex + 1 >= howMany 
   ? { currentIndex: currentIndex + 1, result: result.concat([a]) }
   : { currentIndex: currentIndex + 1, result: result }
 , { currentIndex: -1, result: [] }
).result 
```

* * *

## 种子值

减少使用复杂对象作为种子值的想法非常强大。例如，我们可以通过遍历数组一次来同时计算数组中各项的和与积:

```
[1,2,3,4,5,6,7,8,9,10].reduce((acc, a) => {
  return {
    sum: acc.sum + a,
    product: acc.product * a
  }
}, {sum: 0, product: 1}) 
```

这里选择`{sum: 0, product: 1}`作为初始种子并不简单。`0`是求和运算的中性元素，`1`是乘积的中性元素。

减少空数组的结果等于减少的种子值。

```
[].reduce((acc, a) => {
  return {
    sum: acc.sum + a,
    product: acc.product * a
  }
}, {sum: 0, product: 1}) 
```

让我们更详细地研究 sum 和 product 函数的种子值的选择:

```
let sum     = arr => arr.reduce((acc, a) => acc + a, 0)
let product = arr => arr.reduce((acc, a) => acc * a, 1) 
```

这个想法是，必须选择种子值 I，以便对于我们的归约函数`f`和作为数组元素的每个`a`:

> # f(a，i) == f(i，a) == a

种子值是归约函数的中性元素。

例如，对于产品函数，其中`f = (acc, a) => acc * a`，种子值必须为 1，因此:

> 1 * a == a * 1 == a

## 管道

`pipe` function 接收一个函数列表，并将它们一个接一个地应用到它的输入中。通过使用`pipe`，我们可以避免定义一次性使用的临时局部变量:

```
function addTwoPlusOneOverSeven(a) {
  const b = 2 * a
  const c = b + 1
  const d = c / 7
  return c
}

// will become

function addTwoPlusOneOverSeven(a) {
  return pipe([
      x => x * 2
    , x => x + 1
    , x => x / 7
  ])(a)
} 
```

换句话说，更一般地说，pipe 通过组合其输入数组中的函数来创建一个新函数:

```
const addTwoPlusOneOverSeven = pipe([
    x => x * 2
  , x => x + 1
  , x => x / 7
]) 
```

使用 reduce 定义管道非常简单:

```
let pipe = arr => arr.reduce(
    (acc, next) => x => next(acc(x))
  , x => x
) 
```

注意种子值`x => x`。这是同一功能，是组成的中性元素。它类似于`sum`的`0`或`product`的`1`。

这里我们的归约函数是:`f = (acc, next) => x => next(acc(x))`

注意`acc`和【next】都是函数，`f`将它们一个接一个地组合在一起。

`id = x => x`是中性元素，因为对于我们能想到的每个函数`h`:

> f(id，h) == f(h，id) == h

```
pipe([
    x => x * 2
  , x => x + 1
  , x => x / 7
  , x => `((10 * 2) + 1) / 7 = ${x}`
])(10)

// "((10 * 2) + 1) / 7 = 3" 
```

* * *

## 移动平均线

最后，我想展示如何使用 reduce 实现有效的移动平均，以及一些基本的统计数据:

```
let movingAverage = (size, arr) => arr.reduce((acc, a) => {
  let currentWindow = acc.currentWindow.concat([a])
  currentWindow = currentWindow.length > size
    ? drop(1, currentWindow)
    : currentWindow
  return {
    currentWindow,
    result: currentWindow.length == size
      ? acc.result.concat([sum(currentWindow) / size])
      : acc.result
  }
}, {currentWindow: [], result: []})

let {result} = movingAverage(3, [2,5,6,4,1])
let expected = [sum([2,5,6])/3, sum([5,6,4])/3, sum([6,4,1])/3]

{result, expected}

// result = [4.333333333333333, 5, 3.6666666666666665] 
```

基本描述性统计一气呵成:

```
let stats = data => data.reduce( 
    ({count, sum, mean, vari, min, max}, x) => {
      const k = 1 / (count + 1)
      const mean_ = mean + k * (x - mean)
      const ssr_ = (count - 1) * vari + k * 
        count * (x - mean) * (x - mean)
      return {
          count: count + 1
        , sum: sum + x
        , mean: mean_
        , vari: ssr_ / Math.max(1, count)
        , min: isNaN(min) || x < min ? x : min
        , max: isNaN(max) || x > max ? x : max
      }
    }
  , {count: 0, sum: 0, mean: 0, vari: 0, min: NaN, max: NaN}
)

stats([3,4,2,2,4,3,2,2,4,5])

/*
{
  count: 10, 
  sum: 31, 
  mean: 3.1, 
  vari: 1.2111111111111112, 
  min: 2, 
  max: 5
}
*/ 
```

这里我使用 [Welford 方差算法](https://en.wikipedia.org/wiki/Algorithms_for_calculating_variance#Welford's_online_algorithm)来计算方差。该算法也适用于流。

我们需要对数组进行排序，以计算其他统计数据，如中位数或四分位数。

* * *

## 定义减少

现在，为了了解 reduce 如何在内部工作，让我们定义我们自己的版本 reduce 函数。

reduce 是递归的抽象。在每次迭代中，我们通过对数组中的当前元素调用归约函数`f`以及归约的最近一次迭代的结果来产生结果。

```
let reduce = (f, seed) => arr => {
  if(arr.length === 0){
    // result of reducing an empty array is the initial seed
    // the array is empty if it is the last iteration
    return seed 
  } else {
    const [a, ...tail] = arr
    const result = f(seed, a)

    // result is the initial seed of the next iteration
    return reduce(f, result)(tail)
  }
}

reduce((acc, a) => acc + a, 0)(oneToTen)

// 55 
```

或者我们可以用迭代来定义 reduce:

```
reduce = (f, seed) => arr => {
  if(arr.length == 0) {
    // result of reducing an empty array is the initial seed
    return seed 
  } else {
    let result = seed
    for(var i = 0; i < arr.length; i++) {
      const a = arr[i]
      result = f(result, a)
    }
    return result
  }
}

reduce((acc, a) => acc + a, 0)(oneToTen)

// 55 
```

我希望你同意我们使用递归的定义更优雅。它抓住了 reduce 的一些真相。它清楚地表明 reduce 是对数组中元素递归的抽象。

尽管迭代版本在 JavaScript 中更快，因为许多 JavaScript 引擎不支持[尾部调用优化](https://webkit.org/blog/6240/ecmascript-6-proper-tail-calls-in-webkit/)技术。

## 从右减少

标准`Array.prototype.reduce`从左到右缩小数组。这意味着它首先对种子值和数组的第一个元素应用归约运算，创建一个新的种子值，删除第一个元素，然后重复。

我们也可以从右到左减少数组:

```
let reduceRight = (f, seed) => arr => {
  if(arr.length === 0){
    // result of reducing an empty array is the initial seed
    return seed 
  } else {
    const [a, ...tail] = arr
    const result = reduceRight(f, seed)(tail)

    // first result is the seed, 
    // second result is f applied to the seed and 
    // the last element of the the array, ...
    return f(result, a)
  }
}

// 4 - (3 - (2 - (1 - 0))) = 2
let leftReduceResult  = [1,2,3,4].reduce((acc, a) => a - acc, 0)

// (((4 - 0) - 3) - 2) - 1 = -2
let rightReduceResult = reduceRight((acc, a) => a - acc, 0)([1,2,3,4])

console.log({leftReduceResult, rightReduceResult}) 
```

对于链表数据结构，从右到左的缩减特别有效。

ECMAScript 支持[array . prototype . reduce right](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduceRight):

```
[1,2,3,4].reduceRight((acc, a) => a - acc, 0)

// -2 
```

* * *

## 扫描

没有一篇关于 reduce 的文章是完整的，没有提到 scan。

`scan`返回一个数组，该数组包含归约中每一步的结果。当我们有效地处理无限流时，扫描在流处理中特别有用(检查 [RxJS 扫描](https://www.learnrxjs.io/operators/transformation/scan.html))。

```
let scan = (f, x0) => arr => arr.reduce(
  ({prev, result}, a) => {
    const current = f(prev, a);
    return {prev: current, result: result.concat([current])};
  }
  , {prev: x0, result: []}
).result

let sum_scan = scan(
  (total, a) => total + a
  , 0
)

sum_scan(oneToTen)

// [1, 3, 6, 10, 15, 21, 28, 36, 45, 55] 
```

注意，我们使用 reduce 来定义 scan，而 scan 产生的数组中的最后一项是 reduce 的结果:

```
last(scan(f, s, arr)) == reduce(f, s, arr) 
```

或者使用管道:

```
pipe([
  scan(f, s)
, last
]) == reduce(f, s) 
```

或者用数学符号表示:

> # Last scan = decrease

我希望我让你进入了减肥的禅意。