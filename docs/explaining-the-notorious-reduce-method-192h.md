# 在 2 分钟内了解 reduce 方法

> 原文：<https://dev.to/veebuv/explaining-the-notorious-reduce-method-192h>

一点背景，我叫 Vaibhav——来自 Five2One。我从事 JS 已经将近 8-9 年了，Angular 1 days，基本上在 Udemy 上帮助建立/培训了 15000+人。为数百万人服务的代码，所以我的工作不仅仅是构建可伸缩的代码模式，而是真正关心 ALOT 的性能。

我喜欢做的事情之一是了解我使用的工具是如何内部优化或操作的，这有助于我学习。所以我想与人们分享 reduce 函数是如何工作的，这通常会让人们对 JS、编程或回调范式感到困惑。

所以让我们开始吧。(ps 这并不是针对所有边缘情况的超级优化，因为这将淡化解释它背后的原理，我鼓励你在评论中写下你对此的看法)

我们有一个简单的 forEach 函数，它接受一个集合/数组和一个回调函数

一个简单的 for 循环，调用一个函数，该函数通过集合/数组中的每个元素传递给它。

[![](img/bf288e6fd19fd2a6aab9b0524e842145.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OxmGg_-Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4dasd4uovzsj5lb8fh1i.png)

所以它的快速实现是这样的

[![](img/f6a02540bf6d7e1aa0d43ed658c7f82e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LA8dfunK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p6nrqjg3jtfmo59ckpkr.png)

那么我们为什么要做 forEach 呢？等一下...你会明白为什么。

(一秒钟后)

好的，让我们看看 reduce 函数的实现，但在此之前，让我们写下 reduce 函数的规格:

*   接受一个集合
*   接受一个回调，该回调与累积值和迭代中集合中的当前项有关
*   接受一个初始值

唷，那是一些事情。

在我们深入细节之前，这里有一个快速实现

[![](img/07bcceaa6bc1882de12bd0f7dbf2099a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oqu5E-5g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x7439wx7cjj2j4spcsys.png)

reduce 函数中的累加器是一个保存“总数”的值，或者是我们试图得到的任何东西的浓缩，它可以是一个对象，一个数字，一个字符串，任何符合要求的东西。

所以，`accum=initialiser`，我们将累积值设置为我们提供给函数的初始化器，如果它存在的话

然后，瞧，我们使用我们的 forEach，在集合中循环，除非回调中发生了一些独特的事情。

我们说，如果在第一次运行中没有定义`accum`，这意味着如果没有定义初始值，那么继续将集合中的第一个值赋给`accum`变量，并退出循环，否则照常进行，并使用累积值和当前迭代值进行回调。

回调的返回值被赋回累加器，记住它是`accumulator`它的工作是在遍历集合时不断压缩值。

这再简单不过了

```
 const number = reduce([1, 2], (accum, val) => {
  accum = accum + val;
  return accum
}, 5)

console.log('number', number); // 8 
```

Enter fullscreen mode Exit fullscreen mode

希望这对你有所帮助！:)

在 LinkedIn || [Twitter](https://twitter.com/veebuv) 上关注我，会有更多文章