# fp-ts 入门:类别

> 原文：<https://dev.to/gcanti/getting-started-with-fp-ts-category-4c9a>

在上一篇文章中，我们看到了函数式编程中使用的一些基本抽象: [Eq](https://dev.to/gcanti/getting-started-with-fp-ts-setoid-39f3) 、 [Ord](https://dev.to/gcanti/getting-started-with-fp-ts-ord-5f1e) 、[半群](https://dev.to/gcanti/getting-started-with-fp-ts-semigroup-2mf7)和[幺半群](https://dev.to/gcanti/getting-started-with-fp-ts-monoid-ja0)。

在接下来的文章中，我们将探索一些使函数式编程更加有趣的高级抽象。

历史上， [fp-ts](https://github.com/gcanti/fp-ts) 中包含的第一个高级抽象是`Functor`，但是在我们谈论函子之前，我们需要了解一些关于**类别**的知识，因为函子是建立在它们之上的。

函数式编程的基石是**复合**。但是这到底意味着什么呢？什么时候我们可以说两个东西*缀*？什么时候我们可以说《T4》写得很好呢？

我们需要一个关于复合的正式定义。这就是分类的意义所在。

> 类别抓住了构图的本质。

# 类别

类别的定义有点长，所以我将把它分为两部分:

*   第一个是技术性的(首先我们需要定义它的组成部分)
*   第二部分将包含我们最感兴趣的内容:组成的概念

### 第一部分(定义)

一个类别是一对`(Objects, Morphisms)`其中:

*   `Objects`是**对象**的集合
*   `Morphisms`是对象之间的**同态**(或箭头)的集合

**注**。这里的术语“对象”与 OOP 无关，你可以把对象看作是你无法检查的黑盒，或者甚至是某种形式的变体的辅助占位符。

每个态射`f`都有一个源对象`A`和一个目标对象`B`，其中`A`和`B`在`Objects`中。

我们写`f: A ⟼ B`，我们说“f 是从 A 到 B 的态射”。

### 第二部分(作文)

有一个名为“composition”的操作`∘`，必须满足以下属性

*   (**态射的合成**)每当`f: A ⟼ B`和`g: B ⟼ C`是`Morphisms`中的两个态射，那么在`Morphisms`中必定存在第三个态射`g ∘ f: A ⟼ C`，它是`f`和`g`的*合成*
*   (**结合律**)如果`f: A ⟼ B`、`g: B ⟼ C`和`h: C ⟼ D`则`h ∘ (g ∘ f) = (h ∘ g) ∘ f`
*   (**恒等**)对于每个对象`X`，存在一个态射`identity: X ⟼ X`称为*恒等态射*对于`X`，这样对于每个态射`f: A ⟼ X`和每个态射`g: X ⟼ B`，我们有`identity ∘ f = f`和`g ∘ identity` = g

**例子**

[![a simple category](img/cf026494969a7979e41ba2a1cc78471e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m_VM8n97--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Category_SVG.svg/1920px-Category_SVG.svg.png)

<center>(source: [category on wikipedia.org](https://en.wikipedia.org/wiki/Category_(mathematics)))</center>

这个范畴相当简单，只有三个对象和六个态射(1 个 <sub>A</sub> ，1 个 <sub>B</sub> ，1 个 <sub>C</sub> 是`A`、`B`、`C`的身份态射)。

## 分类为编程语言

类别可以被解释为一种**类型的编程语言**的简化模型，其中:

*   对象是**类型**
*   态射是**函数**
*   `∘`是通常的**功能组成**

图表

[![a simple programming language](img/cf026494969a7979e41ba2a1cc78471e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m_VM8n97--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Category_SVG.svg/1920px-Category_SVG.svg.png)

可以解释为一种相当简单的、原始的编程语言，只有三种类型和少量函数。

例如:

*   `A = string`
*   `B = number`
*   `C = boolean`
*   `f = string => number`
*   `g = number => boolean`
*   `g ∘ f = string => boolean`

实现可能类似于

```
function f(s: string): number {
  return s.length
}

function g(n: number): boolean {
  return n > 2
}

// h = g ∘ f
function h(s: string): boolean {
  return g(f(s))
} 
```

Enter fullscreen mode Exit fullscreen mode

## 为打字稿的一个类别

我们可以定义一个名为 *TS* 的类别，作为类型脚本语言的模型，其中:

*   **对象**是所有的打字稿类型:`string`、`number`、`Array<string>`，...
*   **态射**都是打字稿函数:`(a: A) => B`，`(b: B) => C`，...其中`A`、`B`、`C`、...是类型脚本类型
*   **同一性态射**都被编码成一个单一的多态函数`const identity = <A>(a: A): A => a`
*   **态射的复合**是通常的函数复合(它是结合的)

作为打字稿的模型， *TS* 威力似乎太有限:没有循环，没有`if` s，*几乎*什么都没有...尽管如此，这个简化的模型对于我们的主要目的来说已经足够丰富了:推理一个定义良好的组合概念。

## 作文的中心问题

在 *TS* 中，只要`B = C`T5，我们就可以构造两个通用函数`f: (a: A) => B`和`g: (c: C) => D`

```
function compose<A, B, C>(g: (b: B) => C, f: (a: A) => B): (a: A) => C {
  return a => g(f(a))
} 
```

Enter fullscreen mode Exit fullscreen mode

但是如果`B != C`呢？我们怎样才能*组成*这样的函数呢？我们应该放弃吗？

在下一篇文章中，我们将看到在什么条件下这样的构图是可能的。我们将讨论**函子**。

TLDR:函数式编程是关于组合的