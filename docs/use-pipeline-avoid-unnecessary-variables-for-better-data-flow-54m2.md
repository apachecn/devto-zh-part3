# 如何通过移除变量来简化代码

> 原文：<https://dev.to/babak/use-pipeline-avoid-unnecessary-variables-for-better-data-flow-54m2>

你有没有发现自己在做一些看起来类似于这种模式的事情？

```
const userInfoFromStorage = localStorage.getItem('userInfo')
const userInfoParsed = JSON.parse(userInfoFromStorage)
const userInfoClearedCache = { ...userInfoParsed, cache: {} }  
return userInfoClearedCache 
```

大量一次性使用的临时变量。我以前也这样。我想出了创造性的方法来命名这些变量，以“自我记录”正在发生的事情。但这通常是个坏主意，以下是一些原因:

*   你创造的变量越多，你就越有可能出现拼写错误。
*   对数据执行的操作被大量的语法干扰所包围。
*   变量名不能代替注释。
*   命名不必要的变量是不必要的精神负担。

```
There are only two hard things in Computer Science: cache invalidation and naming things.

-- Phil Karlton 
```

但是我们应该做什么呢？很明显，这不是一个容易处理的代码:

```
return {
  ...( 
    JSON.parse( 
      localStorage.getItem(
        'userInfo'
      )
    )
  ),
  cache: {}
} 
```

呀！😲

虽然很难看，但上面的代码确实表明，将这种转换表达为一系列不带变量的函数调用是可能的。一个函数的输出是下一个函数的输入，依此类推。我们希望*在函数*之间传输值。一种解决方案是编写一个函数，允许我们进行这种抽象。那么我们如何做到这一点呢？

这里有一个使用`reduce` :
的解决方案

```
const pipeline = ( initialValue, ...fns ) => (
  fns.reduce( 
    ( val, fn ) => fn( val ), 
    initialValue 
  )
) 
```

如您所见，这个函数使用一个`initialValue`并使用`reduce`将一个函数的输出输入到下一个函数的输入中。如果你不确定我为什么在这里使用 reduce，我的 [reduce 教程](https://dev.to/babak/why-you-should-use-reduce-instead-of-loops----part-i-5dfa)的第一部分可能会有帮助。现在我们可以像这样重构原始代码:

```
 return pipeline(
  // get user information stored as a string in local storage
  localStorage.getItem('userInfo'),
  // parse the string
  JSON.parse,
  // reset the cache to an empty obj
  obj => ({ ...obj, cache: {} })
) 
```

这不是干净多了吗？很容易看出数据是如何从上到下流动的。

对于这种风格，一些开发人员可能会遇到两个常见的挑战:

1.  类型。如何键入一个`pipeline`函数？
2.  日志记录(用于调试)。如何记录管道链之间的数据？

我们将在此解决这些问题:

# 打字版

有一种方法可以使用自引用类型来表达这个函数，但是这很复杂，需要另外写一篇文章。对于那些好奇的人来说，这大概是这种类型的样子:

[https://github . com/babakness/soul train/blob/v 0 . 0 . 43/src/type/_ experiments . ts # L18](https://github.com/babakness/soultrain/blob/v0.0.43/src/type/_experiements.ts#L18)

或者，我们可以简单地使用 TypeScript 的函数重载来表达类型应该如何发展。例如:

```
function pipeline<A,B>( a: A,  ab: (a: A) => B ): B
function pipeline<A,B,C>( a: A,  ab: (a: A) => B, bc: (a: B) => C ): C
function pipeline<A,B,C,D>( a: A, ab: (a: A) => B, bc: (a: B) => C, cd: (c: C) => D):D
// and so on 
```

这就是`pipeline`在我的图书馆灵魂列车中的工作方式。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [巴巴克尼斯](https://github.com/babakness) / [灵魂列车](https://github.com/babakness/soultrain)

### Soultrain 是一个用 Typescript 编写的紧凑函数库。

<article class="markdown-body entry-content container-lg" itemprop="text">

请注意，这个库仍然是实验性的，将会有突破性的变化。如果您在另一个实验项目中尝试这个，最好将其锁定到一个特定的版本。

# 安装

`npm install soultrain`

# 灵魂列车

Soultrain 是一个用 Typescript 编写的紧凑函数库。它的灵感来自 Ramda 和基于代数数据类型的容器式编程。

# 动机

Ramda 是一个很棒的工具，但是，它依赖 JS DOC 注释规范来提供类型数据。虽然这很好，但 Typescript 提供了更好的静态类型。因此，这个库旨在提供具有更好静态类型的通用功能工具。

# 概观

这一部分将在以后完成。从高层次来看，该库提供了智能的`curry`、`pipe`和`pipeline`函数，具有良好的类型支持。它目前提供两个单子，一个`Maybe`和一个`Transduce`。前者的工作类似于其他可能的图书馆，但是，它提供了…

</article>

[View on GitHub](https://github.com/babakness/soultrain)

类型化版本的好处是让编译器仔细检查代码。下面是一个 JavaScript 版本无法捕捉的示例:

[![](img/4d8dc03a8ec7791a6533943fe4d6a85d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K6Wpjzfh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qnm6vket3cyuahxan7k5.png)

编译器注意到`roo`不是由`processor.process`返回的对象的属性

# 记录/调试

要记录管道中函数之间的数据流，可以简单地使用一个日志函数，该函数记录并返回它接收到的数据。对于类型，可以这样写:

```
const log = <A>(data: A): A => (console.log(data),data) 
```

或者，要记录消息，可以这样定义日志:

```
const noteLog = (note: unknown) => (
  <A>(data: A): A => ( console.log(note, data), data )
) 
```

可以这样使用:

```
return pipeline(
  // get user information stored as a string in local storage
  localStorage.getItem('userInfo'),
  // parse the string
  JSON.parse,
  // log the data,
  noteLog('data before cache removal'),
  // reset the cache to an empty obj
  obj => ({ ...obj, cache: {} })
) 
```

作为对读者的一个练习，我们也可以创建一个有条件地触发调试器的函数。

# 功能编程

`pipe`和`compose`的兄弟`pipeline`函数将邀请你编写更多的函数。它也将向你展示谄媚是多么强大；甚至通知您函数参数的顺序。

为了说明这一点，请注意我们的管道示例中的箭头函数。`obj`不是一次性变量吗？我们使用它的唯一目的是作为我们数据的占位符...只需向右移动几个字符！

事实证明，通过确保我们的源数据是最后一个参数，并使用 curried 函数，我们可以进一步简化代码。下面我写了如何用普通的 JS 来表达这一点——注意，我们想要覆盖传入对象的键。它将是第二个参数——因此从左向右移动，用第一个参数中的对象覆盖第二个对象`obj2`。

因此，我们没有把`obj2`放在对象展开的右边，而是颠倒顺序，把它放在左边:

```
const mergeLeft = curry( 
  // spread the second parameter first
  ( obj1, obj2 ) => ({ ...obj2, ...obj1 })
) 
```

如果你对`curry`不熟悉，它基本上修改了我们的函数，使它可以像这样写的一样使用:

```
const mergeLeft = obj1 => obj2 => ({ ...obj2, ...obj1 }) 
```

现在我们的解决方案变成:

```
return pipeline(
  // get user information stored as a string in local storage
  localStorage.getItem('userInfo'),
  // parse the string
  JSON.parse,
  // log the data,
  noteLog('data before cache removal'),
  // reset the cache to an empty obj
  mergeLeft({ cache: {} })
) 
```

管道鼓励我们编写小的、可重用的函数来执行特定的任务，并将源数据作为最后一个参数。这类似于 UNIX 或 Linux 终端上的命令。这是一个已经存在很长时间的函数式编程概念。就像一个伟大的经典，这是一个原则，证明自己更好地与时间。

# ES-下一个

函数之间的管道数据是创建一次性变量的一个很好的替代方法，一次性变量只用于将数据从一个函数传递到另一个函数。许多编程语言认识到这一点，并有一个操作符来执行这项任务。对于 JavaScript，有提议的管道操作符

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ tc39 ](https://github.com/tc39) / [提议-管道-运营商](https://github.com/tc39/proposal-pipeline-operator)

### 向 JavaScript 添加简单但有用的管道操作符的建议。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 下一个提议:管道运营商

这个提案引入了一个类似于[F #](https://en.wikibooks.org/wiki/F_Sharp_Programming/Higher_Order_Functions#The_.7C.3E_Operator)[OCaml](http://caml.inria.fr/pub/docs/manual-ocaml/libref/Pervasives.html#VAL%28%7C%3E%29)[elixin](https://hexdocs.pm/elixir/Kernel.html#%7C%3E/2)[Elm](https://package.elm-lang.org/packages/elm/core/latest/Basics#%7C%3E)[Julia](https://docs.julialang.org/en/v1/base/base/#Base.:%7C%3E)、 [Hack](https://docs.hhvm.com/hack/expressions-and-operators/pipe) 和 [LiveScript](http://livescript.net/#piping) 的新操作符`|>`，以及 UNIX 管道和 [Haskell](https://hackage.haskell.org/package/base-4.12.0.0/docs/Data-Function.html#v:-38-) 的`&`。这是一种向后兼容的方式，以一种可读的、功能性的方式来简化链式函数调用，并为扩展内置原型提供了一种实用的替代方法。

* * *

**<g-emoji class="g-emoji" alias="warning" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a0.png">⚠</g-emoji> 警告**:管道语法细节**目前未解决**。有两个竞争方案正在考虑中。本自述文件是一个最低限度的建议，涵盖了管道运营商的基本功能。它的功能就像一个稻草人，用来比较竞争方案的利弊。

这些建议如下:

*   **F#管道** : [**讲解人**](https://github.com/valtech-nyc/proposal-fsharp-pipelines/blob/master/README.md) + [**规格**](https://valtech-nyc.github.io/proposal-fsharp-pipelines/)
*   **智能管道** : [**讲解人**](https://github.com/js-choi/proposal-smart-pipelines/blob/master/readme.md) + [**规格**](https://jschoi.org/18/es-smart-pipelines/spec)

两者的巴别塔插件已经开始收集反馈。

另见 [**对 TC39**](https://docs.google.com/presentation/d/1eFFRK1wLIazIuK0F6fY974OIDvvWXS890XAMB59PUBA/edit#slide=id.p) 的最新演示以及 [**最近的 GitHub 问题**](https://github.com/tc39/proposal-pipeline-operator/issues?utf8=%E2%9C%93&q=is%3Aissue+sort%3Aupdated-desc+) …

</article>

[View on GitHub](https://github.com/tc39/proposal-pipeline-operator)

这将允许我们像这样重写我们最初的例子:

```
return (
 // get user information stored as a string in local storage
 localStorage.getItem('userInfo'),
  // parse the string
  |> JSON.parse
  // reset the cache to an empty obj
  |> obj => ({ ...obj, cache: {} })
) 
```

在此之前，我们可以受益于今天使用的`pipeline`函数，以避免不必要的变量声明。