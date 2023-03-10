# 为什么应该使用 reduce 而不是 loops -第一部分

> 原文：<https://dev.to/babak/why-you-should-use-reduce-instead-of-loops----part-i-5dfa>

下面是一个常见的场景:您希望遍历列表中的所有项目来生成新数据。在本文中，我们将讨论`reduce`,以及在这种情况下，如何以及为什么您可能想要使用它来代替像`for-of`或`while`这样的循环结构。例如 JavaScript 和 TypeScript。首先，让我们比较一下当在代码中发现它们时，它们各自告诉了你什么:

# 减少

Reduce 是关于数据转换的。一眼看去，当你看到一个`reduce`，它传达了五个关键的东西

1.  该数据将被转换成另一种类型
2.  最终数据将是什么类型
3.  转换函数的输入输出是什么
4.  这里不会出现副作用
5.  这里不会发生突变

那得多多沟通啊！

# 循环往复

循环是通用的构造。他们没有传达任何类型的转变正在发生。实际上，任何事情都可能在循环中发生，这是公平的游戏。改数据，不改数据，向外太空发射火箭...随便啦！

* * *

# 给我减减！

你可能对方法`Array.prototype.reduce`很熟悉。然而在 JavaScript 中，你可能会处理许多可迭代的东西，而不仅仅是数组。可迭代的例子包括字符串、映射和异步流！

我将写下一个通用的 reduce 作为`for-of`循环的抽象。它不仅可以处理数组，还可以处理 JavaScript 中任何可迭代的内容。另外，我将同时写下一个 TypeScript 版本和一个纯 JS 版本。

这里是打字稿版本。它是打字的，所以你可以用它获得所有的智能感知。

```
type Reducer<V, D> = ( acc: V, item: D, count: number ) => V

function reduce<V, D>(
    initialValue: V,
    reducer: Reducer<V, D>,
    data: Iterable<D>,
  ): V {
    let acc = initialValue
    let count = 0
    for ( const item of data ) {
      acc = reducer( acc, item, count++ )
    }
    return acc
} 
```

这里是普通的旧 JS 版本。

```
function reduce(
    initialValue,
    reducer,
    data,
  ) {
    let acc = initialValue
    let count = 0
    for ( const item of data ) {
      acc = reducer( acc, item, count++ )
    }
    return acc
} 
```

如你所见，我们的迭代器 reduce 只是对`for-of`循环的抽象。这也是对变异的一种抽象——我们的 reduce 实现做了对数据的初始值进行变异的脏活。

那么，它是如何工作的呢？

| 参数 | 描述 |
| --- | --- |
| `initialValue` | 首先，设置初始值，它将匹配最终类型。这意味着如果你将 initialValue 设置为`0`，那么返回的类型将是一个数字。如果设置为`[]`，最终类型将是数组。 |
| `reducer` | 一个接受两个参数的回调函数。

*   The first parameter is called "accumulator". Calling our callback function for the first time will set the accumulator to our `initialValue`. After that, it will be the value returned by our reducer callback function the last time it was called.
*   The second parameter will be set to the next iteration of iterable item. So, in the case of strings, it will start with the first character in the string, move to the second, third, and so on.
*   Finally, the third parameter is to iterate the current position of our iterable. On the first call, the value will be 0, then 1, and so on.

 |
| `data` | 这是我们想要处理的数据 |

现在让我们用`for`循环和`reduce`来解决一些问题

## 写一个函数，返回一个字符串中最长单词的长度。

首先，循环的方式

```
 function longestWordLength( str ) {
  const words = split( /\W+/g )
  let longestLength = 0
  for ( const word of words ) {
    longestLength = Math.max( longestLength, word.length )
  }
  return longestLength
} 
```

现在让我们看看如何使用 reduce 来实现这一点。首先，我们需要写下我们的减速器。

```
const longestWordLengthReducer = ( longestLength, word ) => {
  return Math.max( longestLength, word.length )
} 
```

然后，我们通过声明初始值、缩减量和数据来提供我们的解决方案。

```
const longestWordLength = str => reduce( 
    0, 
    longestWordLengthReducer, 
    str.split( /\W+/g )
) 
```

注意`reduce` API 如何让我们能够快速理解这个函数将做什么。我们马上知道`initialValue`被设置为一个数字。所以我们知道最终数据类型是一个数字。当然，在 JS 中一切皆有可能，但是使用 TypeScript 版本将有助于确保这一点。

还要注意，我们已经将循环的“业务逻辑”提取到一个单独的、可测试的函数中，这部分是关于如何在给定前一个单词长度的情况下找到最大的单词。

使用 reduce，我们通过将 reduce 函数与一个 reducer 和一个将字符串拆分成单词的函数结合起来，解决了我们的问题。我们不需要明确地写一个循环。我们可以轻松地更换零件来解决不同的问题。

对于 for-of 循环，我们反复考虑解决方案。

使用 reduce，我们以声明方式考虑解决方案*。我们正在编写更易维护的代码。*

# 表现

更新:感谢 Krzysztof Miemiec，我能够在循环实现中捕捉到一个错误。结果事实上不分上下。

让我们消除一些关于 reduce 性能的误解。这种编程不仅更容易维护，而且速度也一样快，甚至更快！我们这里的`reduce`只是对`for-of`循环的抽象。这里您可以看到两次不同运行的基准测试结果。非常接近。

[![](img/d5d022ba7c571d6b7d70c45e19c673ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oWlq42A2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e6but81bhg632lg5ex2e.png)
[![](img/9885896c6def34f01853ce8e1b986771.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6utCMrGP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0u1296wnm4ag53declrv.png)

<figure>

<figcaption>举个例子
</figcaption>

</figure>

一般来说，编写重用的和测试良好的函数更安全。我们的功能是集中的——所以如果我们改进它们，我们的整个应用程序也会随之改进。函数式编程促进了代码的重用。

因此，使用我们这里的例子，考虑如果在未来的某个时刻，我们找到一种更快的方法来确定两个值中较大的一个，而不是`Math.max`。如果我们这样做了，那么组成这个函数的所有函数也会受益。

# 敬请期待

在下一篇文章中，我们将进一步发展这些想法。请继续关注，订阅，并在 Twitter @ babak ness 上找到我。