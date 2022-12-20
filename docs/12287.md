# 虫洞-4 种语言的数据收集备忘单和库

> 原文：<https://dev.to/jdsteinhauser/wormhole---data-collection-cheat-sheet-and-library-in-4-languages-56de>

在过去一年代码的[出现期间，我试图增强我对 Elixir 的了解，以及一般的函数式编程。有时候，我发现了一个我使用的大多数其他语言都没有的类似函数(一个非常明显的例子是](https://adventofcode.org) [`Enum.reduce_while`](https://hexdocs.pm/elixir/Enum.html#reduce_while/3) )，而其他时候，我在编写其他语言中经常使用的函数(Clojure 的 [`frequencies`](http://clojuredocs.org/clojure.core/frequencies) )会非常方便！).我最终决定咬紧牙关，尝试创建一个我在其他语言中经常使用的集合操作函数的列表，在我已经学习过/目前正在尝试学习的其他语言中实现它们，并发现我不想没有的新函数！

# 什么是虫洞？

是这个，真的:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ jdsteinhauser ](https://github.com/jdsteinhauser) / [虫洞](https://github.com/jdsteinhauser/wormhole)

### 我用不同语言实现的一些最常用的功能

<article class="markdown-body entry-content container-lg" itemprop="text">

# 虫孔

你有没有想过，“嘿，我希望这种语言有我喜欢的其他语言的能力？”进入虫洞。

## 动机

在 Code 2018 的[到来期间，我发现自己在 Elixir 中编写了我在 Clojure、F#或其他一些语言中使用过的相同函数。为了防止自己在将来这样做，我决定建立一个库来存放所有这些我知道或想学的跨语言的有用函数。](https://http://adventofcode.com/)

## 期望的功能

这些是我用过的功能，我想在多种语言中使用。其中一些已经在语言中实现了，所以我不再重复实现。每个实现都将列出用该语言实现的函数以及到它们的文档的链接。

*   `map`
*   `filter`
*   `reduce`
*   `reduce_while`
*   `chunk`
*   `chunk_by`
*   `juxt`
*   `min_by`
*   `max_by`
*   `frequencies`
*   `group_by`
*   `scan`
*   `inc`
*   `dec`
*   `zip` …

</article>

[View on GitHub](https://github.com/jdsteinhauser/wormhole)

我已经上瘾了。我喜欢学习新的语言。随着新语言的学习，你最终会发现你希望在其他语言中拥有的函数、类和概念。有时，这些函数被命名为不同的东西，当你在不同的语言之间切换时会感到困惑。我最终做了大量的数据收集操作，所以我决定从我最了解的开始，并从那里扩展开来！

# 我要找什么功能？

对于一个非详尽的列表，我希望至少有以下内容:

*   收藏基础:`map`、`filter`、`reduce`和`scan`
*   分块数据:`chunk`，`chunk_by`
*   常用统计:`min_by`、`max_by`、`group_by`、`frequencies`
*   其他海拉有用的东西:`reduce_while`、`juxt`、`identity`

# 我的目标语言是什么？

目前，我已经填补了我在 C#、Clojure 和 Elixir 中的函数空白。本周早些时候，我有了一个 F#解决方案，并且我已经开始查看 Ruby 函数的完整列表。然后...嗯，我也不完全确定！我想我会浏览 Rust、JavaScript、Java，可能还有 Kotlin 和 Python 3，看看我还能在所有这些语言中实现哪些方便的东西。

# 这些会部署到包管理器吗？

是...但不是现在。我需要将文档转换到合适的状态。我以前拉下过几个包裹，但是我从来没有把我的推到任何包裹上！我确信这最终会成为一篇博文。

# 当前备忘单

这里是我到目前为止针对的语言的总结，带有每个已经存在的或者我在 Wormhole 中实现的函数的文档链接。

| 功能 | C# | F# | Clojure | 长生不老药 |
| --- | --- | --- | --- | --- |
| `map` | [T2`Enumerable.Select`](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.select?view=netcore-2.2) | [T2`Seq.map`](https://msdn.microsoft.com/en-us/visualfsharpdocs/conceptual/seq.map%5b't,'u%5d-function-%5bfsharp%5d) | [T2`clojure.core/map`](http://clojuredocs.org/clojure.core/map) | [`Enum.map/2`](https://hexdocs.pm/elixir/Enum.html#map/2) ， [`Stream.map/2`](https://hexdocs.pm/elixir/Stream.html#map/2) |
| `filter` | [T2`Enumerable.Where`](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.where?view=netcore-2.2) | [T2`Seq.filter`](https://msdn.microsoft.com/en-us/visualfsharpdocs/conceptual/seq.filter%5b't%5d-function-%5bfsharp%5d) | [T2`clojure.core/filter`](http://clojuredocs.org/clojure.core/filter) | [`Enum.filter/2`](https://hexdocs.pm/elixir/Enum.html#filter/2) ， [`Stream.filter/2`](https://hexdocs.pm/elixir/Stream.html#filter/2) |
| `reduce` | [T2`Enumerable.Aggregate`](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.aggregate?view=netcore-2.2) | [T2`Seq.reduce`](https://msdn.microsoft.com/en-us/visualfsharpdocs/conceptual/seq.reduce%5b't%5d-function-%5bfsharp%5d) | [T2`clojure.core/reduce`](http://clojuredocs.org/clojure.core/reduce) | [T2`Enum.reduce`](https://hexdocs.pm/elixir/Enum.html#reduce/2) |
| `reduce_while` | `ReduceWhile` | `reduceWhile` | `reduce-while` | [T2`Enum.reduce_while/3`](https://hexdocs.pm/elixir/Enum.html#reduce_while/3) |
| `scan` | `Scan` | [T2`Seq.scan`](https://msdn.microsoft.com/en-us/visualfsharpdocs/conceptual/seq.scan%5b't,'state%5d-function-%5bfsharp%5d) | [T2`clojure.core/reductions`](http://clojuredocs.org/clojure.core/reductions) | [`Enum.scan`](https://hexdocs.pm/elixir/Enum.html#scan/2) ， [`Stream.scan`](https://hexdocs.pm/elixir/Stream.html#scan/2) |
| `chunk` | `Chunk` | `chunk` * | [T2`clojure.core/partition`](http://clojuredocs.org/clojure.core/partition) | [`Enum.chunk_every/4`](https://hexdocs.pm/elixir/Enum.html#chunk_every/4) ， [`Stream.chunk_every/4`](https://hexdocs.pm/elixir/Stream.html#chunk_every/4) |
| `chunk_by` | `ChunkBy` | `chunkBy` | [T2`clojure.core/partition-by`](http://clojuredocs.org/clojure.core/partition-by) | [`Enum.chunk_by/2`](https://hexdocs.pm/elixir/Enum.html#chunk_by/2) ， [`Stream.chunk_by/2`](https://hexdocs.pm/elixir/Stream.html#chunk_every/4) |
| `juxt` | `Juxt` | `juxt`、`juxt2`、`juxt3` | [T2`clojure.core/juxt`](http://clojuredocs.org/clojure.core/juxt) | `Wormhole.juxt/1` |
| `min_by` | `MinBy` | [T2`Seq.minBy`](https://msdn.microsoft.com/en-us/visualfsharpdocs/conceptual/seq.minby%5b't,'u%5d-function-%5bfsharp%5d) | `min-by` | [T2`Enum.min_by/3`](https://hexdocs.pm/elixir/Enum.html#min_by/3) |
| `max_by` | `MaxBy` | [T2`Seq.maxBy`](https://msdn.microsoft.com/en-us/visualfsharpdocs/conceptual/seq.maxby%5b't,'u%5d-function-%5bfsharp%5d) | `max-by` | [T2`Enum.max_by/3`](https://hexdocs.pm/elixir/Enum.html#max_by/3) |
| `frequencies` | `Frequencies` | `freqs` | [T2`clojure.core/frequencies`](http://clojuredocs.org/clojure.core/frequencies) | `Wormhole.freqs/1` |
| `group_by` | [T2`Enumerable.GroupBy`](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.groupby?view=netcore-2.2) | [T2`Seq.groupBy`](https://msdn.microsoft.com/en-us/visualfsharpdocs/conceptual/seq.groupby%5B't%2C'key%5D-function-%5Bfsharp%5D) | [T2`clojure.core/group-by`](http://clojuredocs.org/clojure.core/group-by) | [T2`Enum.group_by/3`](https://hexdocs.pm/elixir/Enum.html#group_by/3) |
| `identity` | `Identity` | [T2`Operators.id`](https://msdn.microsoft.com/en-us/visualfsharpdocs/conceptual/operators.id%5B't%5D-function-%5Bfsharp%5D) | [T2`clojure.core/identity`](http://clojuredocs.org/clojure.core/identity) | `Wormhole.identity/1` |

*   F#包含一个 [`Seq.windowed`](https://msdn.microsoft.com/en-us/visualfsharpdocs/conceptual/seq.windowed%5B't%5D-function-%5Bfsharp%5D) 函数，但是它一次只移动块中的一个元素。

# 这东西为什么有用？

有些函数是不言自明的，或者已经在其他几篇文章中讨论过了。我将介绍一些鲜为人知的方法，以及我个人认为它们有用的原因。

## 分块

我以前写过关于`chunk`和`chunk_by`的文章，但如果你错过了，可以看看我以前的文章！

[![jdsteinhauser image](img/16b5761aadeb7bd79ed19278aed29ef8.png)](/jdsteinhauser) [## 好吧，分手吧！使用分区/区块

### Jason stein Hauser 10 月 18 日 186 分钟阅读

#functional #programming #coding #algorithms](/jdsteinhauser/alright-break-it-up-using-partitionchunk-h15)

## 减少的同时

我承认这可能是一个不常用的案例。有时你不想缩减整个序列——只缩减到某一点。不幸的是，`reduce`通常是全有或全无。当你有一个潜在的无限系列的数据时，这实际上不起作用。然而，Elixir 的`reduce_while`帮助我保留了我的 [AoC 2018 Day 1 Part 2](https://adventofcode.com/2018/day/1) compact 的解决方案。我希望为它找到更多真实世界的用例...但它仍然是我发现的最喜欢的数据处理功能之一。

## Juxt

虽然我承认，乍一看，`juxt`没什么特别的。获取对相同参数进行操作的函数数组，然后返回接受该参数的单个函数，并返回对这些参数运行的每个函数的数组？为什么要用那个？

我之前已经将这个函数从 Clojure 移植到其他工作项目中。例如，我有一个*非常*大的数据集合(1MM+条目！)而且我负担不起对它们进行多次迭代。我使用`juxt`将我的分析函数组合在一起，这样我只需要迭代集合一次。

类似地，由于 Clojure 中的关键字可以被视为从带有该关键字的映射中检索值的函数(`(:foo {:foo 5 :bar 3})`返回`5`)，因此您可以编写几个关键字来访问映射集合中的数据，并以类似于表的格式返回结果。我在之前关于密集 Clojure 代码的文章中写过:

[![jdsteinhauser image](img/16b5761aadeb7bd79ed19278aed29ef8.png)](/jdsteinhauser) [## 紧凑代码的详细解释

### 杰森·斯坦豪泽 1818 年 5 月 21 日 8 分钟阅读

#showdev #clojure #productivity](/jdsteinhauser/a-verbose-explanation-of-compact-code-546g)

## 频率

因为有时候，你只需要一个直方图。在一个函数中提供！

# 结论

希望有人会发现这是有用的，无论是作为一个小抄或作为一个图书馆。在近期，我将研究 Ruby 和 Rust(按此顺序),看看我能预见的跨多种语言使用的其他方便的功能。我很快也会把虫洞作为一个包放在你最喜欢的包管理器中，并可能写下我喜欢/不喜欢每个包的事情。

祝您编码愉快，我很乐意听到您认为有用的其他通用数据操作函数！