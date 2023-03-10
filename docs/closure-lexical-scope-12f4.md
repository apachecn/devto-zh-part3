# 闭包和词法范围

> 原文：<https://dev.to/stephencweiss/closure-lexical-scope-12f4>

当我们说 JS 是词汇作用域的时候，我们的意思是函数将在它被定义*的上下文*中访问变量，而不是在它被*调用*的上下文中访问变量(只要那些细节是相关的——这是垃圾收集优化)。

假设我们想要创建一个递增函数来跟踪我们调用它的次数。

这是可行的…但前提是我们在全局内存中跟踪 num

```
num = 0
function increment(num) {
  return num + 1
}
increment(num)
increment(num)
console.log(num) // 2; 
```

如果我们使用闭包概念呢？

```
function count() {
  let currentCount = 0
  let random = 'primed for garbage collection'
  function increment() {
    currentCount += 1
    console.log(currentCount)
    return currentCount
  }
  return increment
} 
```

如果我现在将`count`的结果赋给一个变量名`currentPlace`，我将能够在任何时候记录我被调用了多少次。

我们来走一遍这个:`const currentPlace = count()`。

当我们计算 count 时，返回的是什么？一个功能`increment`。注意，这个函数没有被调用。

如果你看一下*什么是* `currentPlace`，你会发现它是一个函数——但是如果你调用它，你可以得到当前位置。

但是等等——我们返回`count`的时候，不是只有返回函数`increment`吗？不完全是。通过它，Javascript 存储了定义`increment`的本地环境。

这意味着即使`count`已经从调用栈中弹出，我们仍然可以通过 Javascript 维护的`increment`的范围访问`currentCount`。

这一切意味着什么？

```
const currentPlace = count()
currentPlace() // 1
currentPlace() // 2
currentPlace() // 3 
```

这里有一个用 Chrome 开发工具进行实践和调试的视频

CodeSmiths 的创始人 Will Sentance 将词法范围称为函数的背包(而不是更通俗的“闭包”)。

我喜欢这个有两个原因:

1.  闭包是词法范围变得相关的过程，但就我们正在讨论的数据而言，闭包是相当不精确的
2.  由于词法作用域是在定义函数时就伴随着函数的，所以很容易认为它是绑在后面的。

也就是说——如果你看了这个片段，你会注意到 Chrome 把 scope 称为`Closure`，所以它是任何适合你的东西。

最后一点:我一直在学习 Will 的课程，关于 FrontendMasters 的 [Javascript:最难的部分](https://frontendmasters.com/courses/javascript-hard-parts/)，我不得不说我印象非常深刻。特别是，他对闭包和高阶函数的解释比我见过的大多数解释都要好得多。