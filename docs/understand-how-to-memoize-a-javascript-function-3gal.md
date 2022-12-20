# 了解如何记忆 JavaScript 函数

> 原文：<https://dev.to/dhilipkmr/understand-how-to-memoize-a-javascript-function-3gal>

[![](img/0a6a49049c511f2cf6a9d2cb33035035.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kSM26oQ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ogu5bl1s1jiftg7d0j0g.png)

## 到底什么是记忆化？

记忆的基本功能是在再次接收到相同的输入集合时，返回先前计算的值，而无需重新计算。

因此，每当函数接收到相同的输入参数集时，它都会在其缓存变量中检查是否已经存在该变量的值，然后返回该值或进行重新计算。

[![](img/be21ef05fc4144a6a1a5bbb5f5a2988d.png)](https://i.giphy.com/media/fNlRJ7Gwr4Lba/giphy.gif)

*   这有助于减少**计算时间。**
*   **更快的渲染时间**

### 概述:

*   有一个求和函数将两个数相加。
*   我们创建自己的`memoization`函数。
*   使用`memoization`函数作为高阶函数，并创建一个输出函数。
*   当我们需要调用求和函数时，调用上面的输出函数代替。

[![](img/788808c9ec9a53e29249a3e593ee4fcf.png)](https://i.giphy.com/media/xT0xem7ZlZ2DOYqpG0/giphy.gif)

让我们开始吧。

函数`summation`是我们要记忆的函数。这是一个简单的函数，将两个数相加并返回结果。

```
// Function that sums two numbers
const summation = function (a, b) {
  return a + b;
} 
```

*   `memoize`函数将函数`fnToMemoize`作为单个参数，并返回一个可以被调用的`function`。
*   `memoizedCache`是我们缓存新结果的对象。
*   `constructPropertyFromArgs`用于根据我们传递的参数和函数创建唯一的属性名。我们将在下一节中详细讨论这一点。
*   `manageInsertion`用于在达到最大值时从缓存对象中删除属性。(默认长度:10)
*   首先我们检查属性是否出现在`memoizedCache`中，如果是，我们从`memoizedCache`返回结果，或者我们实际调用函数`fnToMemoize`并将结果存储在`memoizedCache`中。

```
//  `memoize` function  decides if it has to return cached value or call the summation function
const memoize = function (fnToMemoize) {
  const memoizedCache = {}                // A closeure Object
  return function(...args) {
    const propToCheck = constructPropertyFromArgs(fnToMemoize, args);
    if (!memoizedCache[propToCheck]) {
      memoizedCache[propToCheck] = fnToMemoize(...args);
    } else  {
      console.log('From Cache ');
    }
    return memoizedCache[propToCheck];
  }
} 
```

## 我们如何构造一个属性名？

这是至关重要的，因为不正确的命名可能会导致应用程序的意外行为。

`memoize`函数可以作为一个通用函数，通过它，我们可以记住位于同一作用域内的任何其他函数。因此，为了避免不当行为，我们需要为我们的函数取唯一的名字。

> 我们的属性名是由 **'|'** 分隔的函数名和参数的组合，T1 充当了一个**分隔符。**

### 我们为什么需要分隔符？

假设我们不使用分隔符，只连接字符串。

这里`add (fn, 1, 2, 3)`的属性名称为`fn123`。
并且`add (fn, 12, 3)`的房产名称也将是`fn123`。

因此`add(fn, 12,3)`的输出将是 6，这是根据之前的执行计算的。

[![](img/890e3a943b5860f823a8c9ed14935733.png)](https://i.giphy.com/media/25QbQdrFvFAMcPKq24/giphy.gif)T3】

```
// To create a Property name from the arguments passed to the function
const constructPropertyFromArgs = function (fnToMemoize, args) {
  let propToCheck = [];
  propToCheck = propToCheck.concat(fnToMemoize.name, args);
  return propToCheck.join('|'); // A delimiter to join args
} 
```

最后，我们将我们的`summation`函数传递给我们的`memoize`函数，后者返回一个存储在`memSummation`中的函数。

然后我们调用`memSummation`两次。

```
const memSummation = memoize(summation, 2);  // `memoize` is a HOC

console.log(memSummation(10, 50));
console.log(memSummation(10, 50)); 
```

输出:

第一个 console.log()在执行后返回输出，而第二个则从缓存中返回。

```
 "From Summation function"

 60

 "From Cache "

 60 
```

这种方法的局限性:

*   不处理匿名函数
*   应该小心选择分隔符，因为它对于与参数具有相同分隔符的字符串无效。
*   仅对纯函数有效
*   没办法控制空间复杂度。

一个考虑空间复杂性的例子在这个[博客](https://www.dhilipkmr.dev/blogs/memoization)中

点击找到密码笔链接

考虑空间复杂度 [codepen 示例](https://codepen.io/dhilipkmr/pen/PgeMxM?editors=0010)

查看我的其他帖子[这里](https://dev.to/dhilipkmr)

有趣的帖子别忘了关注我:)

[![](img/2dbdd6da7be64e4e10f72620cc9d0495.png)](https://i.giphy.com/media/fxsqOYnIMEefC/giphy.gif)

那都是乡亲:)