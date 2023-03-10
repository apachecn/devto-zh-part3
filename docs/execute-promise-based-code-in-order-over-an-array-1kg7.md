# 在数组上按顺序执行基于承诺的代码

> 原文：<https://dev.to/4nduril/execute-promise-based-code-in-order-over-an-array-1kg7>

### 问题

我最近遇到了一个问题:我有一个输入数据的列表(一个数组),想要对列表中的每一项执行一个函数。

没问题，你说，拿`Array.prototype.map`，就是干这个的。**但是**正在讨论的函数返回一个承诺，我希望只有当所有这些承诺都被解决时，我才能继续程序流。

没问题，你说，包在`Promise.all`里，就是这个用的。**但是**这个功能很贵。代价如此之高，以至于它产生了一个子进程(整个代码都在我的计算机上的 NodeJS 中运行),这个子进程占用了太多的 CPU 资源，以至于当我的输入列表超过几个元素时，我的计算机就会死机。

这是因为实际上，所有繁重的子进程几乎是并行启动的。实际上，他们是按顺序开始的，但是下一个不会等待上一个完成。

### 第一个方案

所以我需要的是一种方法来遍历数组，执行当前元素的函数，*等待*直到承诺解决，*然后*转到下一个元素，用它调用函数。这意味着`map`不会工作，因为我无法控制执行流程。所以我将不得不建造我自己的`map`。在我使用它的时候，我会把它实现得更好一些，作为一个独立的函数，首先使用映射器函数，然后使用数据数组:

```
 const sequentialMap = fn =>
  function innerSequentialMap([head, ...tail]) {
    if (!head) {
      return Promise.resolve([])
    }
    return fn(head).then(headResult =>
      innerSequentialMap(tail).then(tailResult => [headResult, ...tailResult])
    )
  } 
```

Enter fullscreen mode Exit fullscreen mode

那么，这是什么？它采用应该应用于数组中所有值的函数`fn`,并返回一个新函数。这个新函数需要一个数组作为输入。您会看到函数被简化了，因为它只接受一个参数，当所有参数都被提供时，真正的执行就开始了。例如，这允许我们使用映射函数“预加载”`sequentialMap`，并在不同的输入数据上重用它:

```
// preloading
const mapWithHeavyComputations = sequentialMap(heavyAsyncComputation)

// execution
const result = mapWithHeavyComputations([…]) 
```

Enter fullscreen mode Exit fullscreen mode

但是在这种情况下，currying 支持(或简化)另一种技术:递归。

当一个函数反复调用自己时，我们说它是递归的。递归在功能上等同于命令式编程中的循环。只要编程语言允许这两种方式，您就可以将其中一种重构为另一种。至少我是这么认为的。

我在这里使用了递归函数，因为我想不出在循环中等待承诺解析的方法。我如何使用`.then()`并在`then`的中跳转到下一个迭代步骤*？*

不管怎样，让我们更深入地研究一下代码。在内部函数或第二个函数的主体中，首先我定义了一个条件来终止递归:我检查第一个元素是否为 falsy，如果是 falsy，我就返回一个解析为空数组的承诺。这是因为函数的主路径将数据作为包装在承诺中的数组返回。因此，如果我们在终止时返回相同类型的数据，那么所有的数据都会很好地组合在一起。

接下来，如果我们没有终止(这意味着给定列表的第一个元素是真的),我们就对它应用 mapper 函数。这将返回一个承诺，我们等待它与`.then`解决。一旦它解决了，整个事情变得有点神奇，但不会太多。

我们接下来要做的是建立一个嵌套的承诺。通常情况下，当你处理承诺并想将几个函数应用于内在价值时，你会建立一个“承诺链”:

```
const result = firstPromise
  .then(doSomethingWithIt)
  .then(doSomthingElseAfterThat)
  … 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里的问题是，为了构建最终结果(映射数组)，我们需要来自第一个解析承诺的结果，然后还需要来自所有其他承诺的结果值，这些结果值不是根据彼此的计算的*，而是独立于*。**

 *所以我们用两个特性来解决这个问题:嵌套作用域和承诺扁平化(有人说是单子吗？).

首先是嵌套作用域:当我们在一个函数中定义一个函数时，内部函数可以访问不是在它自身中而是在外部函数(外部或周围作用域)中定义的变量:

```
function outer(arg1) {
  const outerValue = arg1 + 42

  function inner() {
    return outerValue + 23
  }

  console.log(inner())
}

outer(666) // logs 731 
```

Enter fullscreen mode Exit fullscreen mode

承诺扁平化本质上意味着，如果你有一个价值的承诺，就好像你只有一个价值的承诺。

```
 const p2 = Promise.resolve(Promise.resolve(1))
const p1 = Promise.resolve(1)

p2.then(console.log) // logs 1
p1.then(console.log) // logs 1 
```

Enter fullscreen mode Exit fullscreen mode

回想一下，下面是我们正在讨论的代码的样子:

```
 return fn(head).then(headResult =>
    sequentialMapInternal(tail).then(tailResult => [headResult, ...tailResult])
  ) 
```

Enter fullscreen mode Exit fullscreen mode

我们将`headResult`保留在范围内，然后通过再次递归调用内部函数来生成下一个承诺，但是使用了一个没有第一个元素的更短的列表。我们用`.then`再次等待最终结果，然后我们才构建我们的结果数组。

这是通过在`headResult`后扩展`tailResult`来实现的:我们知道我们从调用`fn(head)`中得到一个值，但是我们从调用`sequentialMapInternal(tail)`中得到一个值列表。因此，使用 spread 操作符，我们可以得到一个很好的结果值平面数组。

注意，第一个`then`中的函数将`headResult`作为参数，立即返回下一个承诺(-chain)。这基本上就是我们使用承诺扁平化的地方。`.then`返回一个承诺本身，现在我们在里面返回一个承诺。但结果将看起来像一个普通的承诺——看不到嵌套。

### 更好的方法

虽然这工作得很好，当我现在调用我的脚本时，我的计算机仍然可用，但所有这些嵌套的`then`看起来并不那么好。当我们拥有异步函数时，我们可以解决这个问题:

```
const sequentialMap = fn =>
  async function innerSequentialMap([head, ...tail]) {
    if (!head) {
      return Promise.resolve([])
    }
    const headResult = await fn(head)
    const tailResult = await innerSequentialMap(tail)
    return [headResult, ...tailResult]
  } 
```

Enter fullscreen mode Exit fullscreen mode

是的，这样好多了。现在，执行被暂停，直到`headResult`出现，然后再次暂停，直到`tailResult`出现，只有这样，我们才构建结果数组并完成。

### 最短的路

等等。我刚才说我可以用`await`暂停执行吗？这难道不是在一个循环中也能工作吗？

```
const loopVersion = fn =>
  async list => {
    const result = []
    for (const elem of list) {
      result.push(await fn(elem))
    }
    return result
  } 
```

Enter fullscreen mode Exit fullscreen mode

看，这就是发生在像我这样过于深入函数式编程范例的人身上的事情。是的，你通常应该避免循环，因为它们不是声明性的，你最终会告诉机器(和你的同事)不是你想要发生的事情，而是你想要如何发生。也就是说，一般来说，没有好的实践。但在这种情况下，这正是我们想要的:给出一个如何执行我们代码的分步模式。优化资源使用。*