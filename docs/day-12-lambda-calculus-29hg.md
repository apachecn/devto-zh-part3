# λ演算

> 原文：<https://dev.to/gillchristian/day-12-lambda-calculus-29hg>

我认为在 [#100DaysOfFP](https://twitter.com/gillchristian/status/1080979563700727808) 的一些日子里，写一些比推文更长的东西更有意义。所以，我会时不时地在 [dev.to](https://dev.to) 上发帖，而不仅仅是推文。😄

本周，代尔夫特哈斯克尔研究小组再次启动。我去年参加了，但没有像我希望的那样专注，所以我决定今年再试一次。

学习小组的目标是从基本原理开始阅读 [Haskell 编程的一些章节，然后一起完成练习。](http://haskellbook.com/)

我这个系列的目标是分享这一章中的一些东西。不仅仅是一篇博客，这是一个笔记的集合，可以作为本章的总结。

## 追根溯源

> 即使是最伟大的数学家，那些我们会放入我们的伟大数学家神话中的数学家，也不得不做大量的跑腿工作，以便最终找到答案。
> 
> ― Daniel Tammett

这本书的第一章没有直接介绍 Haskell 和编程，而是向我们介绍了λ演算。为什么？因为它是函数式编程的基础，也是 Haskell 的基础。

λ演算是“20 世纪 30 年代由阿隆佐·邱奇发明的一种计算模型。[...]函数式编程语言都是基于λ
演算 <sup id="fnref1">[1](#fn1)</sup> 。

**函数式编程**:“一种依赖于以数学函数为模型的函数的计算机编程范例[...]程序是表达式的组合”。

**表达式**:“包括具体的值、变量，也包括函数”。

**函数**:是“应用于一个参数或输入的表达式，一旦应用，可以*化简*或*求值*”。它们定义了“一组可能的输入和一组可能的输出之间的关系”。

## λ表达式

在 lambda 演算中，我们有:表达式、变量和抽象。其中表达式是一个超集，因为它们可以是变量、抽象或者它们的组合。

**变量**:函数潜在输入的名称。

**抽象**:函数。lambda 术语由头部(lamda)和主体组成，适用于论点。

**自变量**:一个值。

**应用**:对一个参数应用 lambda 函数。

```
λx.x 
```

Enter fullscreen mode Exit fullscreen mode

让我们来分解一下 <sup id="fnref2">[2](#fn2)</sup> :

```
λ x . x
^─┬─^
  └────── extent of the head of the lambda.

λ x . x
  ^────── the single parameter of the
          function. This binds any
          variables with the same name
          in the body of the function.

λ x . x
      ^── body, the expression the lambda
          returns when applied. This is a
          bound variable. 
```

Enter fullscreen mode Exit fullscreen mode

lambda 演算中的函数是匿名的。但是我们仍然称它们为抽象，因为它们概括了一个问题及其解决方案。

**阿尔法等价** :

```
λx.x
λd.d
λz.z 
```

Enter fullscreen mode Exit fullscreen mode

## β还原

“当我们将一个函数应用于一个参数时，我们用输入表达式替换抽象体内所有绑定变量的实例。您还消除了抽象的头部，因为它的唯一目的是绑定一个变量。” <sup id="fnref3">[3](#fn3)</sup>

```
(λx.x) 2
[x ∶= 2]
2 
```

Enter fullscreen mode Exit fullscreen mode

参数可以是任何东西，甚至是其他 lamda:

```
(λx.x)(λy.y)
[x ∶= (λy.y)]
λy.y 
```

Enter fullscreen mode Exit fullscreen mode

在 lambda 演算中的应用是*左关联* :

```
(λx.x)(λy.y)z
[x := (λy.y)]
(λy.y)z
[y := z]
z 
```

Enter fullscreen mode Exit fullscreen mode

一些变量可能是*自由的*，意味着它们在头脑中没有束缚:

```
λx.xy 
```

Enter fullscreen mode Exit fullscreen mode

如果我们应用这样的λ:

```
(λx.xy)z
[x := z]
zy 
```

Enter fullscreen mode Exit fullscreen mode

## 多重论证

每个 lambda 只能绑定一个参数，并且只能接受一个参数。需要多个参数的函数有多个嵌套的头。当应用一次并消除第一个(**最左边的**)头时，应用下一个，以此类推" <sup id="fnref4">[4](#fn4)</sup> 。这叫*逢迎*。

```
λxy.xy === λx.(λy.xy) 
```

Enter fullscreen mode Exit fullscreen mode

**贝塔范式**:当你不能再贝塔化简项的时候。

```
λx.x    -- beta reduced, no arguments to apply

(λx.x)z -- not beta reduced, `z` hasn't been applied 
```

Enter fullscreen mode Exit fullscreen mode

**组合子**:没有自由变量的 lambda 项。

```
λxy.x       -- Combinator
λxyz.xz(yz) -- Combinator

λy.x        -- Not combinator
λx.xz       -- Not combinator 
```

Enter fullscreen mode Exit fullscreen mode

**发散**:从未还原到正常形式的λ被称为*发散*。

```
(λx.xx)(λx.xx)
[x := (λx.xx)]
(λx.xx)(λx.xx)
[x := (λx.xx)]
(λx.xx)(λx.xx)
[x := (λx.xx)]
(λx.xx)(λx.xx)
... 
```

Enter fullscreen mode Exit fullscreen mode

在编程中，分歧的术语不会产生实际的答案，至少不会产生有意义的答案。

## 但是为什么呢？

第一次读完这一章后，我并不清楚所有这些如何应用于 Haskell，或者一般的函数式编程。

原来，减少表达式的过程(beta reduction)就是 Haskell 代码求值的方式。

下面的λ表达式简化为`z`

```
(λabc.cba)yz(λwv.w)
[a := y]
(λbc.cby)z(λwv.w)
[b := z]
(λc.czy)(λwv.w)
[c := (λwv.w)]
(λwv.w)zy
[w := z]
(λv.z)y
[v := y]
z 
```

Enter fullscreen mode Exit fullscreen mode

这相当于 Haskell 中的:

```
-- y = 1
-- z = 2

(\a b c -> c b a) 1 2 (\w v -> w)
(\b c -> c b 1) 2 (\w v -> w)
(\c -> c 2 1) (\w v -> w)
(\w v -> w) 2 1
(\v -> 2) 1
2 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在 GHCi 中运行它，我们会得到相同的结果:

```
λ> (\a b c -> c b a) 1 2 (\w v -> w)
2 
```

Enter fullscreen mode Exit fullscreen mode

* * *

1.  [Haskell 编程来自第一原理](http://haskellbook.com/)，第 2 页。 [↩](#fnref1)

2.  [Haskell 编程来自第一原理](http://haskellbook.com/)，第 6 页。 [↩](#fnref2)

3.  [Haskell 编程来自第一原理](http://haskellbook.com/)，第 7 页。 [↩](#fnref3)

4.  [Haskell 编程来自第一原理](http://haskellbook.com/)，第 10 页。 [↩](#fnref4)