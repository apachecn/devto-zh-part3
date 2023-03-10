# 代数剖析

> 原文：<https://dev.to/mmenestret/anatomy-of-an-algebra-3cd9>

[查看我博客上的文章[这里](http://geekocephale.com/blog/)

我将尝试在解剖图谱中，将我最近在一系列文章中经常解释的函数式编程的基本概念进行分组。

这里的想法是为需要解释的人提供一个位置，并通过尽我所能解释它们来增加我自己对这些主题的理解。我会努力让读者感受到一种直觉，一种对概念的感觉，而不是我的解释的完美、严格的正确性。

*   第一部分:[剖析函数式编程](https://dev.to/mmenestret/anatomy-of-functional-programming-1bpg)
*   第二部分:[解析一个代数](https://dev.to/mmenestret/anatomy-of-an-algebra-3cd9)
*   第三部分:[解剖一个铅字类](https://dev.to/mmenestret/anatomy-of-a-type-class-440j)
*   第四部分:[半群和幺半群的解剖](https://dev.to/mmenestret/anatomy-of-semigroups-and-monoids-22i8)
*   第五部分:[函子剖析与范畴理论](https://dev.to/mmenestret/anatomy-of-functors-and-category-theory-2gf0)
*   第 6 部分:剖析无标签的最终编码——即将到来！

# 什么是代数？

函数式程序员倾向于谈论很多关于*代数*的东西，所以理解什么是*代数*是一个很好的起点。
一个简单的定义是(来自*维基百科*):

> 最普遍的形式是，代数是对数学符号和操作这些符号的规则的研究

那么用更简单的术语来重新表述它，它是一个由以下各项组成的系统:

*   **符号**，物品或“东西”
*   对那些“东西”的操作
*   **关于那些操作的属性和规则**

一个简单的*代数*例子是:

*   符号:整数
*   运算:加法和乘法
*   [属性和规则](https://en.wikipedia.org/wiki/Integer#Algebraic_properties):
    *   闭包:两次运算的结果本身是整数
    *   关联性:`a + (b + c) = (a + b) + c`和`a × (b × c) = (a × b) × c`
    *   交换性:`a + b = b + a`和`a × b = b × a`
    *   身份元素:`a + 0 = a`和`a * 1 = a`
    *   诸如此类(如果你想看其余的，请点击链接)

# 和 FP 有什么关系？

## 领域建模

在对业务领域建模时，在函数式编程中，[我们将数据与行为](https://dev.to/mmenestret/anatomy-of-a-type-class-440j)严格分离(参见*数据/行为关系*部分)。

为此:

*   我们在一边创造类型
*   操纵这些类型的纯函数
*   这些功能必须遵守一些严格的业务逻辑

这没让你想起什么吗？

我们操纵*代数*！

*   **符号**:我们的业务类型
*   **运营**:我们的业务职能作用于这些类型
*   **属性和规则**:我们在这些功能中实现的业务逻辑，以及我们的*基于属性的测试*
    *   我们不会在这里讨论这些，但是给你一个思路，这些不是单元测试，而是基于功能属性的测试，它们必须*总是*验证(例如:“几笔销售的组合不能导致负价格”)，同时根据测试框架生成的大量或多或少的随机值进行测试

### 具体例子

为了更清楚，让我们举一个虚拟的例子。

假设我们必须生成销售报告，我们可能会对我们的领域建模如下:

```
type Amount = Int
type Price = Int

final class ID(val value: Int) extends AnyVal
final case class Item(id: ID, price: Price)
final case class Sales(items: List[(ID, Amount)], totalPrice: Price)

trait SalesOperations {
    def combineAll(sales: List[Sales]): Sales
    def generateReport(sales: Sales): String
} 
```

在这种情况下:

*   **符号** : `Amount`、`Price`、`ID`、`Item`、`Sales`等。(我们的业务类型)
*   **操作** : `combineAll`，`generateReport`(对这些类型的操作)
*   **属性和规则**:这里我们还没有实现的业务逻辑和测试

差不多就是这样！

它经常发生，有几个类型的共同行为。我们希望能够设计出可以在这些不同类型上运行的函数，并根据它们的输入类型拥有预期的行为。那些函数据说是多态的 T2。

这是我们用 [*型类*](https://dev.to/mmenestret/anatomy-of-a-type-class-440j) 解决的问题。

## 什么是 ADT？

*ADT* 代表*代数数据类型*。嗯，我们谈了一些关于*代数*的内容，但是我们还没有谈到*类型*。

### 什么是类型？

一个*类型*或者一个*数据类型*，仅仅是数据的一个分类，一组*值*或者*居住者*，我们用它们来告诉编译器我们打算如何使用数据，这样它就可以检查我们是否遵守了规则(这就是*类型检查*)。

*   `Boolean`是对这两个值进行分类的*类型*:`true`，`false`
*   `String`是一个*类型*，代表无限的居民，所有可能的字符组合
*   `Option[A]`是一个*类型*，表示类型`A`的所有值(用`Some`包装)+值`None`

    *   `Option`的居民数量等于类型`A`+1(`None`值)的居民数量
    *   `Option[Boolean]`有三个居民:`Some(true)`、`Some(false)`、`None`
*   `Unit`是只有一个居民的*类型*，值:`()`

*   `Noting`是一个没有成员的*类型*(无法创建类型为`Nothing`的值)

### 那么什么是代数数据类型呢？

下面是*维基百科*对*代数数据类型*的定义:

> 在计算机程序设计中，尤其是函数式程序设计和类型理论中，*代数数据类型*是一种复合类型，即由其他类型组合而成的类型。

我们称这些新类型为*ADT*,因为我们通过组合现有类型来创建它们...你猜怎么着？我们再次使用一个*代数*！

这就是代数是这样的:

*   **符号**:现有类型:原始类型，其他现有 ADT，...(`Sales`，`Boolean`，`String`，`Int`，...)
*   **运算** : *求和*和*乘积*(我们会看到它们是什么意思)
*   **性质和规律**:关于这些*和*和*积*运算的一些性质和规律(我们也会看到它们是什么意思)

### 总和

*sum* 是通过将它们各自的值“相加”在一起来组合的动作。

你可以把它看作一种定义: *sum 类型*的**值只能是这个或那个类型**的值(这里的**或**是关键直觉)。

在 *Scala* 中，通常使用:

*   *封存的特征*及其实例为*案例类*或*案例对象*

或者更少:

*   `Either[A, B]`型

这样，当你声明:

```
sealed trait CoinSide
case object Heads extends CoinSide
case object Tails extends CoinSide 
```

您正在创建一个 *ADT* `CoinSide`，它是一个 *sum 类型*(使用 *sum* 操作创建)，有且只有两个可能的值(或居住者)`Heads`或`Tails`。

使用
也可以达到同样的效果

```
type CoinSide = Either[Heads, Tails] 
```

其可能的值是，`Left(Heads)`或`Right(Tails)`。

这只是同一事物的两种不同编码。

### 求和属性

*和*操作也有属性。

我不会在这里详细讨论，但是(这些例子同样适用于*密封特征* +它们的实现而不是`Either`类型):

*   一个*总和类型*的值的数量是其组成类型成员的值的*总和*(就像你假设的整数加法一样)

    *   `Boolean`有**两个**居民:`true`和`false`
    *   `Either[Boolean, Boolean]`哪一个是*和*两个类型`Boolean`有**四个居民**:

        *   `Left(true)`
        *   `Left(false)`
        *   `Right(true)`
        *   `Right(false)`
    *   `Either[Boolean, Either[Boolean, Boolean]]]`是`Boolean`和`Boolean`的一个`Boolean`和另一个*和类型*之间的一个*和类型*

        *   你猜怎么着？它有 2 + (2 + 2)个值！
*   它有一个*标识*元素，该元素是没有任何值的`Nothing`类型

    *   `Either[Boolean, Nothing]`是具有*和*身份的`Boolean`的*和类型*。因为没有办法创建一个`Nothing`类型的值，它不存在，所以没有办法构造一个`Right`，它只有**两个值**:

        *   `Left(true)`
        *   `Left(false)`
*   它是联想的，`Either[Boolean, Either[Boolean, Boolean]]]`与`Either[Either[Boolean, Boolean]],Boolean]`相同，可以枚举值，你会看到(嗯*同构*，我们有相同的*【表现力】*与两者的表象，但姑且说它们是相同的)！

*   诸如此类(如果你想继续潜水，我会在最后给你更多的材料)

### 产品

*乘积*是通过将两种或多种类型各自的值“相乘”而将它们组合在一起的动作。

你可以把它看作是定义一个*产品类型*的**值是这个和那个类型**值的组合(这里的**和**是关键的直觉)。

在 *Scala* 中，通常使用:

*   *案例类别*

或者更少

*   *元组*

这样，当你声明:

```
case class TwoCoinsAndABoolean(fst: CoinSide, snd: CoinSide, b: Boolean)
// or
type TwoCoinsAndABoolean = (CoinSide, CoinSide, Boolean) 
```

这只是同一事物的两种不同编码。

您正在创建一个 *ADT* `TwoCoinsAndABoolean`，它是一个*产品类型*(使用*产品*操作创建)，其成员的值的数量相乘。

在我们的例子中，有 8 个值(2 * 2 * 2):

*   `TwoCoinsAndABoolean(Heads, Heads, true)`或`(Heads, Heads, true)`
*   `TwoCoinsAndABoolean(Heads, Tails, true)`或`(Heads, Tails, true)`
*   `TwoCoinsAndABoolean(Tails, Heads, true)`或`(Tails, Heads, true)`
*   `TwoCoinsAndABoolean(Tails, Tails, true)`或`(Tails, Tails, true)`
*   `TwoCoinsAndABoolean(Heads, Heads, false)`或`(Heads, Heads, false)`
*   `TwoCoinsAndABoolean(Heads, Tails, false)`或`(Heads, Tails, false)`
*   `TwoCoinsAndABoolean(Tails, Heads, false)`或`(Tails, Heads, false)`
*   `TwoCoinsAndABoolean(Tails, Tails, false)`或`(Tails, Tails, false)`

您可以观察到*产品类型*值是它们的*构成类型*值的笛卡尔乘积！

### 产品属性

*产品*操作也有属性。

我不会在这里详细讨论，但是(这些例子同样适用于*案例类*而不是*元组*):

*   一个*乘积类型*的值的数量是它的组合成员的值的数量的*乘积*(就像你假设的整数乘法一样)

    *   `Boolean`有**两个**居民:`true`和`false`
    *   `(Boolean, Boolean)`是两种类型`Boolean`中的一种*产品类型*有**四个值**:

        *   `(true, true)`
        *   `(true, false)`
        *   `(false, true)`
        *   `(false, false)`
    *   `(Boolean, (Boolean, Boolean)`是`Boolean`和`Boolean`的一个`Boolean`和另一个*产品类型*之间的一个*产品类型*

        *   你猜怎么着？它有 2 * (2 * 2)个值！
*   它有一个*身份*，这是著名的`Unit`类型，只有一个居民，值`()`

    *   `(Boolean, Unit)`是带有*产品*标识的`Boolean`的*产品类型*。它有两个值:

        *   `(true, ())`
        *   `(false, ())`
*   它是联想的，`(Boolean, (Boolean, Boolean)`与`((Boolean, Boolean),Boolean)`相同，可以枚举值，你会看到(嗯*同构*，我们有相同的*【表现力】*与两者的表象，但姑且说它们是相同的)！

*   诸如此类(如果你想继续潜水，我会在最后给你更多的材料)

### 混合类型

当然，正如我们在一些例子中看到的，*ADT*可以是其他*ADT*的组合，比如*乘积之和*、*乘积之和*、*乘积之和*等等。

# 更多材料

如果你想继续深入，可以在我的 [FP 资源列表](https://github.com/mmenestret/fp-ressources)中找到一些有趣的东西，特别是:

*   [功能和反应域建模](https://www.manning.com/books/functional-and-reactive-domain-modeling)(一本关于使用*代数*的功能域建模的好书)
*   [Scala 中的类型种类](https://kubuszok.com/compiled/kinds-of-types-in-scala/)
*   为什么函数式程序员总是谈论代数？

# 结论

所有这些离题的内容只是为了向您展示，**创建新的复合数据类型，就像我们在纯 FP 中所做的那样，是通过使用代数**来完成的。

这就是为什么这些复合类型被称为*代数数据类型*:)。

总而言之，我们学到了:

*   什么是代数
*   代数是如何以一种简洁的方式用于领域建模的，以及它是如何适用于纯函数式编程方法的
*   什么是*型*
*   如何创建新的*数据类型*是通过使用由*类型*组成的*代数*来完成的，以及为什么创建的类型被称为*代数数据类型*

我会尽量保持博客更新。如果有任何我应该纠正的补充、不精确或错误，或者如果您需要更多的解释，请随时通过 Twitter 或邮件联系我！