# 一个疯狂的想法和概念证明

> 原文：<https://dev.to/gillchristian/a-crazy-idea-and-a-proof-of-concept-2oj7>

去年 12 月，我为一个想法创建了概念验证(PoC ),这个想法看起来有点疯狂，至少现在对我来说是这样。这是一个通过类型签名搜索 TypeScript 函数和方法的工具。

因为我对这个 PoC 很满意，所以我决定投入到这个项目中，致力于开发一个真正有用的版本。

我计划在这个 devlog 中分享我的经验，因为我想跟踪项目如何发展，我学到了什么，以及我在这个过程中发现了什么。此外，我想这将是有趣的，对其他人也有用。首先，我想分享一下我是如何想出这个主意的，我已经做了什么，还有什么是未来的。

<figure>

[![Crazy idea](img/81309904907e5a89f75fb945a9b69fba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tf0_758j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/dFJAhnT.jpg)

<figcaption>A crazy idea.</figcaption>

</figure>

## 疯狂的想法

我学习函数式编程已经有一段时间了。我从弗里斯比教授的函数式编程指南开始，然后尝试了不同的语言，Elm，Haskell，Reason，Clojure，Scheme。这是我喜欢的事情，也鼓励你去做。学习其他语言会给你新的方法和途径来解决问题，你可以把它带回到你所使用的语言中。

在和 Haskell 一起玩去年的[代码](https://adventofcode.com)的时候，我用了很多 [Hoogle](https://hoogle.haskell.org/) (是的，Hoogle，带 H)。

> Hoogle 是一个 Haskell API 搜索引擎，它允许您通过函数名或近似类型签名来搜索 Stackage 上的 Haskell 库。

Stackage 是 Haskell 的一个包管理器，保证包之间可以互相协作。

Hoogle 在开发过程中被证明是一个非常有用的工具。这就引出了一个问题，为什么没有打字稿这种东西？我每天都使用 TypeScript，我认为这是对现有工具的一个很好的补充。

我发现最接近的是 VS 代码中名字为的[开符号。](https://code.visualstudio.com/docs/editor/editingevolved#_open-symbol-by-name)

## 概念的证明

我把这个想法告诉了我的朋友佛朗哥，他比我务实得多，他告诉我“你为什么不建立一个概念验证，看看它是否有意义？”。

作为一个好的概念验证，它必须尽可能简单。一种从本地目录中的所有导出函数中提取类型签名并通过简单的 UI 搜索它们的方法。

这里可以找到[。目前你还不能用它做什么，文档是非常基本的。](https://github.com/gillchristian/ts-earch/tree/68b515b99076460e25e70c545ab6a6397100f77d)

[https://www.youtube.com/embed/Gf5irOLIFX8](https://www.youtube.com/embed/Gf5irOLIFX8)

顺便说一下，我应该把这个名字归功于佛朗哥。

## 开放式问题

开发 PoC 让我相信这是一个值得努力的想法。但是，老实说，它也留给我更多的问题而不是答案。

*   我应该使用实际的 TypeScript 类型签名进行搜索吗？还是应该想出一个更简单的 DSL？在 Haskell 中，这不是什么大问题。我们来对比一下两种语言中`compose`的签名。

```
compose :: (b -> c) -> (a -> b) -> a -> c 
```

Enter fullscreen mode Exit fullscreen mode

```
type Compose = <A, B, C>(f: (b: B) => C, g: (a: A) => B) => (a: A) => C 
```

Enter fullscreen mode Exit fullscreen mode

*   类型推断呢？假设我们有一个`Person`类型，和一个接受一个人的函数，定义为:

```
interface Person {
  name: string
}

const getName = (p: Person) => p.name 
```

Enter fullscreen mode Exit fullscreen mode

搜索`(x: { name: string }) => string`应该返回`getName`，即使类型隐式匹配。如何解决，我还不知道。

*   为搜索类型建立索引的最佳方式是什么？
*   搜索时如何权衡和匹配类型？

这些是我必须解决的几个悬而未决的问题。我相信在这个过程中会有更多的问题。

<figure>

[![The road ahead](img/5c1dee82b9eb9ec7ed4b6ba4c5002a8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--scDW5Gz8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/MGsrYzU.jpg)

<figcaption>The road ahead, definitely not a clear path.</figcaption>

</figure>

## 一步一个脚印

首先，有一些人们可以使用和玩耍的东西是有意义的。我的短期目标是做一些调整，用所有可用于搜索的[确定类型的](https://github.com/DefinitelyTyped/DefinitelyTyped)包部署`ts-earch`。

如果您有任何问题、建议或希望在项目上合作，请随时在 Twitter 上联系我。

敬请关注更多更新=D

* * *