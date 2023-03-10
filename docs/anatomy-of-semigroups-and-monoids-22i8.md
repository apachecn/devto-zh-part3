# 半群和幺半群的剖析

> 原文：<https://dev.to/mmenestret/anatomy-of-semigroups-and-monoids-22i8>

[查看我博客上的文章[这里](http://geekocephale.com/blog/)

我将尝试在解剖图谱中，将我最近在一系列文章中经常解释的函数式编程的基本概念进行分组。

这里的想法是为需要解释的人提供一个位置，并通过尽我所能解释它们来增加我自己对这些主题的理解。我会努力让读者感受到一种直觉，一种对概念的感觉，而不是我的解释的完美、严格的正确性。

*   第一部分:[剖析函数式编程](https://dev.to/mmenestret/anatomy-of-functional-programming-1bpg)
*   第二部分:[解析一个代数](https://dev.to/mmenestret/anatomy-of-an-algebra-3cd9)
*   第三部分:[解剖一个铅字类](https://dev.to/mmenestret/anatomy-of-a-type-class-440j)
*   第四部分:[半群和幺半群的解剖](https://dev.to/mmenestret/anatomy-of-semigroups-and-monoids-22i8)
*   第五部分:[函子剖析与范畴理论](https://dev.to/mmenestret/anatomy-of-functors-and-category-theory-2gf0)
*   第 6 部分:剖析无标签的最终编码——即将到来！

# 何为*半群*？

## 一般定义

*半群*(和*幺半群*，你稍后会看到)是一个复杂的词，代表一个**真正的**简单概念。
我们将快速介绍*半群*，并且我们将解释更长的*幺半群*，因为它们是强相关的。

*维基百科的*定义是:

> 在数学中，*半群*是由一个集合和一个结合的二元运算组成的代数结构。

好吧，这听起来有点抽象，让我们试着用编程术语来重新表述它:

在编程的上下文中，*半群*由两部分组成:

1.  一种类型`A`
2.  将两个类型为`A`的值组合成一个类型为`A`的值的关联操作，我们称之为`combine`
    *   这将是一个类型签名为`(A, A) => A`的*函数*
    *   这是关联的，意味着将元素组合在一起的顺序(决定放入括号的位置)并不重要
        *   `combine(combine(a1, a2), a3) == combine(a1, combine(a2, a3))`具有`a1`、`a2`、`a3`类型的值`A`

然后说 **`A`在`combine`** 下形成一个*半群*。

## 一些例子

### 加法下的整数

*   类型:`Int`
*   操作:`+`

的确，

*   `+`这里的类型是:`(Int, Int) => Int`
*   `+`是联想式的`(20 + 20) + 2 == 20 + (20 + 2)`

整数在加法下形成一个*半群*。

### 布尔下或

*   类型:`Boolean`
*   操作:`||`

的确，

*   `||`这里的类型是:`(Boolean, Boolean) => Boolean`
*   `||`是联想式的`(true || false) || true == true || (false || true)`

布尔在 OR 下形成一个*半群*。

### 列表下列表串联

*   类型:`List[A]`
*   操作:`++`

的确，

*   `++`这里的类型是:`(List[A], List[A]) => List[A]`
*   `++`是联想式的`(List(1, 2) ++ List(3, 4)) ++ List(5, 6) == List(1, 2) ++ (List(3, 4) ++ List(5, 6))`

### 更多例子！

*   乘法运算中的整数
*   AND 下的布尔值
*   串联中的字符串
*   多得多。

我们现在将探索*幺半群*，因为它们是*半群*的“升级”版本。

# 什么是*幺半群*？

## 一般定义

给定了半群的定义，*幺半群*的定义相当简单:

> 在数学中，*幺半群*是一种代数结构，由一个集合、一个结合的二元运算和一个单位元组成。

这意味着一个*幺半群*是一个*半群*加上一个单位元。

用我们的编程术语来说:

在编程的上下文中，一个*幺半群*由两部分组成:

1.  一个*半群*:
    *   一种类型`A`
    *   将两个类型为`A`的值组合成一个类型为`A`的值的关联操作，我们称之为`combine`
2.  类型为`A`的标识元素，姑且称之为`id`，它必须遵守以下规则:
    *   `combine(a, id) == a`带有`a`类型的值`A`
    *   `combine(id, a) == a`带有`a`类型的值`A`

然后说 **`A`在`combine`下与单位元`id`** 形成了*幺半群*。

## 一些例子

我们可以在这里以我们的*半群*为例，并添加它们各自的单位元:

*   加法运算中的整数
    *   带有身份元素`0`:
        *   `42 + 0 = 42`
        *   `0 + 42 = 42`
*   或下的布尔运算
    *   带有身份元素`false`:
        *   `true || false == true`，`false || true == true`
        *   `false || false == false`
*   列表串联下的列表
    *   带有身份元素`Nil`(空列表):
        *   `List(1, 2, 3) ++ Nil == List(1, 2, 3)`
        *   `Nil ++ List(1, 2, 3) == List(1, 2, 3)`

每当你的*半群*的类型和`combine`运算有了一个单位元，它就有了一个*幺半群*。

但是要小心，有一些*半群*不是*幺半群*:

元组在 first 下形成一个*半群*(返回元组的第一个元素)。

*   类型:`Tuple2[A, A]`
*   操作:`first` ( `def first[A](t: Tuple2[A, A]): A = t._1`)

的确，

*   `first`这里的类型是:`Tuple2[A, A] => A`
*   `first`与`A`类型的`a1`、`a2`、`a3`值关联`first(Tuple2(first(Tuple2(a1, a2)), a3)) == first(Tuple2(a1, first(Tuple2(a2, a3))))`

但是无法提供类型为`A`的 identity 元素`id`,因此:

*   `first(Tuple2(id, a)) == a`和`first(Tuple2(a, id)) == a`带有`a`类型的值`A`

# 到底是为了什么？

*幺半群*是一个函数式编程构造，**体现了将“事物”组合在一起**的概念，通常是为了将“事物”简化为一个“事物”。假设组合操作是关联的，它可以被**并行化**。

这是一笔大交易。

举个简单的例子，当你知道你的类型`A`在`combine`下形成一个*幺半群*并带有身份`id`时，这就是你可以做的，绝对无所畏惧:

*   你有一个很大很大很大的`A`列表，你想把它简化成一个`A`
*   您有一个由 N 个节点和一个主节点组成的集群
*   你把你的巨大的列表分成 N 个子列表
*   您将每个子列表分发到集群的一个节点上
*   每个节点通过将其元素 2 减 2 减少到 1 个最终元素来减少它自己子列表
*   它们将结果发送回主节点
*   主节点只有 N 个中间结果要向下`combine`(按照与子列表相同的顺序，记住，这些中间结果是从关联性中产生的！)到最后的结果

多亏了*幺半群*，你成功地、几乎免费地并行化了一个庞大列表的归约过程。

听起来耳熟吗？这就是 fork-join 操作在 Spark 上的天真工作方式！谢谢 *monoids* ！

# 如何在 Scala 中对它们进行编码？

*半群*和*幺半群*被编码为[类型类](https://dev.to/mmenestret/anatomy-of-a-type-class-440j)。

我们将通过一个简单的实现示例，你不应该像那样手工完成，因为我们要做的一切都是由令人敬畏的 FP 库提供的，如 [Cats](https://github.com/typelevel/cats) 或 [Scalaz](https://github.com/scalaz/scalaz) 。

这里是我们的两个类型类:

```
trait Semigroup[S] {
    def combine(s1: S, s2: S): S
}

trait Monoid[M] extends Semigroup[M] {
    val id: M
} 
```

这是我的业务领域建模:

```
type ItemId = Int
case class Sale(items: List[ItemId], totalPrice: Double) 
```

我希望能够将我全年的销售额合并成一笔大的、统一的销售额。

让我们通过定义来为`Sale`定义一个*幺半群*类型的类实例:

*   `id`为空`Sale`，不包含项目 id，0 为`totalPrice`
*   `combine`作为项目 id 列表的串联和`totalPrice`的添加

```
implicit val saleMonoid: Monoid[Sale] = new Monoid[Sale] {
    override val id: Sale                          = Sale(List.empty[ItemId], 0)
    override def combine(s1: Sale, s2: Sale): Sale = Sale(s1.items ++ s2.items, s1.totalPrice + s2.totalPrice)
} 
```

然后我可以使用许多现有的工具，通用函数，利用他们正在处理的类型是*幺半群*的实例这一事实。

`combineAll`(这也是由*猫*或*斯卡拉兹*提供的)是其中之一，并允许，一般来说，免费将我所有的销售结合在一起！

```
def combineAll[A](as: List[A])(implicit M: Monoid[A]): A = {
    def accumulate(accumulator: A, remaining: List[A]): A = remaining match {
        case Nil          ⇒ accumulator
        case head :: tail ⇒ accumulate(M.combine(accumulator, head), tail)
    }
    accumulate(M.id, as)
}

val sales2018: List[Sale] = List(Sale(List(0), 32), Sale(List(1), 10))
val totalSale: Sale       = combineAll(sales2018) // Sale(List(0, 1),42) 
```

**注意:**这里，为了简单起见，我没有用`foldLeft`实现`combineAll`，所以我不必解释`foldLeft`，但是你应该知道我的`accumulate`内部函数**是** `foldLeft`，而`combineAll`实际上应该这样实现:

```
def combineAll[A](as: List[A])(implicit M: Monoid[A]): A = as.foldLeft(M.id)(M.combine) 
```

给你！

# 更多材料

如果你想继续深入，可以在我的 [FP 资源列表](https://github.com/mmenestret/fp-ressources)中找到一些有趣的东西，特别是:

*   [Scala 与 Cats -半群和幺半群章节](https://underscore.io/books/scala-with-cats/)
*   [为什么 Spark 不能折叠左:幺半群和结合律](https://parkergordon.io/2017/04/03/why-spark-cant-foldleft/)
*   [猫文档](https://typelevel.org/cats/typeclasses/semigroup.html)
*   如果你需要更多，请告诉我

# 结论

总而言之，我们看到:

*   半群和半群**幺半群**有多简单，它们的关系有多密切
*   我们看到了半群和半群*的例子*
*   我们了解到这些 FP 结构在现实生活中是多么有用
*   最后，我们展示了它们是如何在 *Scala* 中被编码的，并通过主要的 FP 函数库对你可以用它做什么有了一点了解

我会尽量保持博客更新。如果有任何我应该纠正的补充、不精确或错误，或者如果您需要更多的解释，请随时通过 Twitter 或邮件联系我！