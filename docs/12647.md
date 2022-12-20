# 懒惰、渴望和贪婪的评价

> 原文：<https://dev.to/drbearhands/lazy-eager-and-greedy-evaluation-43pf>

如果你正在进入(纯粹的)函数式编程，你可能会遇到术语“懒惰求值”，或者“急切求值”。

### 它们是什么

简而言之，在延迟求值的语言中，只有在需要时才计算值。

一个例子

```
foo :: [Int]
foo = replicate 5 1

bar :: Int
bar = head foo

main = putStrLn $ show bar -- print the value of bar 
```

Enter fullscreen mode Exit fullscreen mode

在这个程序中，`foo`，不直接求值。相反，运行时“记得”它在 *thunk* 中等于`replicate 5 1`。它同样在 thunk 中存储了`bar`和`main`的 thunk。

一旦`main`试图获得`bar`的值，我们知道`bar`等于`head foo`，并且`head`匹配模式`value : _`。

考虑到这一点，我们展开`foo`，得到`bar = head (replicate 5 1)`。由于`replicate 5 1`与期望的模式`value : _`不匹配，我们扩展它。`replicate`被递归地定义为:

```
replicate times value =
  if times == 0
    then []
    else value : replicate (times - 1) value 
```

Enter fullscreen mode Exit fullscreen mode

通过评估 replicate 的一次迭代，我们得到`bar = head (1 : replicate (5 - 1) 1)`。这个参数头匹配`value : _`。我们现在可以断定`bar = 1`。

在这种情况下，惰性评估使我们不必评估列表`foo`的剩余部分。

相比之下， *eager* evaluation 会在需要值之前完全计算出值。

我似乎再也找不到关于*贪婪*求值的参考文献了，所以不要把这当成福音，但贪婪求值是当一个值的参数已知时就计算它。命令式语言一般会被贪婪地评价。

急切求值和贪婪求值看起来非常相似，但是急切求值允许首先创建大量要求值的值，而贪婪求值会立即对它们求值。前者对于创建并发任务非常有用。

在这篇文章的剩余部分，我将忽略贪婪的评价。

### 严格 vs 不严格

与其说是懒惰或渴望的语言，不如说是*严格*和*非严格*语言。
严格意味着一个值只能被评估或者不被评估(静态)，而非严格语言也允许值被部分评估。如你所料，严格的语言对应热切的求值，非严格的语言对应懒惰的求值。

但是为什么严格和非严格是一种语言比懒惰和渴望更好的属性呢？简单地说，如何评估程序从根本上说是编译器的属性，编译器负责将程序变成可以运行的东西，而语言只定义其构造的属性。

不过，我在这里有点迂腐。

### 与纯度的关系

懒评要求纯洁。有了副作用，改变评估顺序可能会改变程序的结果。

然而，纯度不需要惰性评估。我听 Haskell 的人说过，懒惰是伟大的，因为它加强了纯洁，所以任何引入副作用的诱惑都被平息了。对我来说，这听起来你并不是真的想要纯洁，但我跑题了。

### 懒惰的优点

我不太相信非严格语言，所以我将引用 [@jvanbruegge](https://dev.to/jvanbruegge) 的[评论](https://dev.to/jvanbruegge/comment/8k8c)来提供一个更中立的观点。

> 它使得很多算法更容易表达，并且允许你把你的算法当作你遍历的数据结构(在我看来这很酷)。也没有懒惰，一些平常琐碎的身份不成立。
> 
> 比如这个:`head . fmap f = f . head`。如果 f 在列表的第二个元素下，左边的会下，右边的不会下。在懒惰的语言中两者都不会。

### 严的优点

因为严格语言有更简单的值状态，它们变得更容易推理。特别是，内存管理和并发性会变得更加复杂。例如，如果我们有

```
let 
  x = ...
  y = ...
in
  f(x, y) 
```

Enter fullscreen mode Exit fullscreen mode

假设`y`不包含`x`，在纯语言中，我们可以同时计算`x`和`y`(没有副作用使得纯函数对于数据依赖来说是无序安全的)。

然而，在非严格的情况下，`x`或`y`的扩展可能不会终止，即使`f(x, y)`会终止，或者它们可能比必要的长得多。