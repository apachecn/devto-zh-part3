# 要避免的另一个 GOTO

> 原文：<https://dev.to/qm3ster/another-goto-to-avoid-4nkn>

# 另一个 GOTO 隐藏在你的代码中

或者，用 clickbait 术语来说:

> 你的编程是结构化的吗？答案可能会让你大吃一惊！

只是一个我经常看到人们错过的花絮，而且不止一次地引起麻烦。

**TL；如果你开始重构，你最好完成它。
**TL；博士 2** :事实上，对一些人来说“递归是它自己的奖励”意味着它通常是“聪明的代码”，因此应该是生产中的最后手段。
**TL；DR 3**:*dev . to 上没有剧透标签，所以这个在帖子末尾。***

**注意**:为了广泛的可访问性，示例将使用 JavaScript，但这适用于大多数语言。

一个常见的现代“最佳实践”重构是避免`switch` fallthrough，特别是当它不是别名，但是一些动作是在 fallthrough 之前执行的时候。
我们最后从这里开始:

```
function theFunction(operation, value) {
  switch (type) {
    case "bigIncrease":
    case "bigIncreaseAlias":
      value *= 2
    case "smallIncrease":
      value += 1
      break
    default:
      throw new TypeError(`Unknown operation ${operation}`)
  }
  return value
} 
```

对此:

```
function theFunction(operation, value) {
  switch (type) {
    case "bigIncrease":
    case "bigIncreaseAlias":
      value *= 2
      value = theFunction("smallIncrease", value)
      return value
    case "smallIncrease":
      value += 1
      return value
    default:
      throw new TypeError(`Unknown operation ${operation}`)
  } 
```

这变成了一个递归函数，但至少我们不再有“坏”的错误，对吗？
另一个改进可测试性的方法，特别是关于确定哪些功能未被使用，以及通常查找引用的工具，是将开关盒分成多个功能。

```
const operations = {
  bigIncrease: value => theFunction("smallIncrease", value * 2),
  bigIncreaseAlias: value => operations.bigIncrease(value),
  smallIncrease: value => value + 1
}
function theFunction(operation, value) {
  const fn = operations[operation]
  if (!fn) throw new TypeError(`Unknown operation ${operation}`)
  return fn(value)
} 
```

JK，那个别名换个方式解决比较好:

```
const bigIncrease = value => theFunction("smallIncrease", value * 2)
const operations = {
  bigIncrease,
  bigIncreaseAlias: bigIncrease,
  smallIncrease: value => value + 1
}
function theFunction(operation, value) {
  const fn = operations[operation]
  if (!fn) throw new TypeError(`Unknown operation ${operation}`)
  return fn(value)
} 
```

在这一点上，精明的读者会完全喜欢

> 哇，你应该完全内联该调用，而不是在字符串上有“动态调度”，伙计。字符串名变了怎么办？！这对工具不好，而且浪费性能。

读者当然是对的，我们以此结束:

```
const bigIncrease = value => operations.smallIncrease(value * 2)
const operations = {
  bigIncrease,
  bigIncreaseAlias: bigIncrease,
  smallIncrease: value => value + 1
}
function theFunction(operation, value) {
  const fn = operations[operation]
  if (!fn) throw new TypeError(`Unknown operation ${operation}`)
  return fn(value)
} 
```

## 提问

这是好代码吗？我们结束了吗？

[https://www.youtube.com/embed/WljpG7sH3r4](https://www.youtube.com/embed/WljpG7sH3r4)

嗯，在我看来我们不是。

考虑一下如果我们将抽象命名的`.smallIncrease`函数的定义改为`value => value + 2`会发生什么！这对谦逊的`operation.bigIncreaseAlias`的影响将是灾难性的！一切都毁了！
怎么会这样？(不，这篇文章不是关于单元测试的重要性。)我们太勤奋了！
我们所做的是依靠`smallIncrease`函数。不是为了它的实际语义“*响应动态`"smallIncrease"`命令*需要做什么”，而是为了它的内部实现细节。
我们怎样才能做得更好？我们如何避免这样的悲剧重演？
首先，我们可以避免过分滥用 DRY 原则，以避免晦涩的代码:

```
const bigIncrease = value => value * 2 + 1
const operations = {
  bigIncrease,
  bigIncreaseAlias: bigIncrease,
  smallIncrease: value => value + 1
}
function theFunction(operation, value) {
  const fn = operations[operation]
  if (!fn) throw new TypeError(`Unknown operation ${operation}`)
  return fn(value)
} 
```

看，妈妈，我刚刚救了 22 个角色！/讽刺

在现实生活中，共享功能可能更长、更复杂。做什么？
简单，使用更好的命名和开闭原则。换句话说，编写只做一件事的函数，这些函数你永远也不需要编辑，只有在它们变得无用时才删除。
在我们的例子中，它看起来像这样:

```
const plusOne = value => value + 1

const bigIncrease = value => plusOne(value * 2)
const operations = {
  bigIncrease,
  bigIncreaseAlias: bigIncrease,
  smallIncrease: plusOne
}
function theFunction(operation, value) {
  const fn = operations[operation]
  if (!fn) throw new TypeError(`Unknown operation ${operation}`)
  return fn(value)
} 
```

如果有人把**编辑成**的话，他们可能会无可救药。
和`operations`现在只是一个声明性的字符串到本地可用函数的映射，不需要很长的内部实现，一眼就能看清楚。
换句话说，当你的代码定义形成一个非循环图时，你会过得很愉快。

**免责声明**:请温柔一点，我以前从来没有在任何地方写过任何东西。

如约:
**TL；DR 3** :递归定义还是递归。
对心智模型没有好处的时候就要避免。