# ...和(几乎)所有的单子:状态单子

> 原文：<https://dev.to/riccardo_cardin/and-monads-for-almost-all-the-state-monad-2im1>

*原贴于:[大泥球](http://rcardin.github.io/)T3】*

本文开始了一个在开发人员社区中非常热门的主题系列:函数式编程。在细节上，我们将重点关注*单子*。来自范畴理论的这个非常复杂的对象在函数式编程中非常重要，在这种范式中没有它很难编程。它的主要目的是简化纯函数的组成，即不能有任何*副作用*的数学函数。我们从一个经典开始:*状态单子*。顾名思义，使用这个对象，我们将能够有效地管理程序的状态，而不会破坏范例的主要原则之一，*不变性*。

免责声明:我不会描述函数式编程的基础知识，比如不变性和引用透明性。我提供的材料的主要目的是确定理解基本单子所需的概念和推理。

我会用 Scala，因为我不知道 Haskell(但是，它在我的 todo 列表中)，也因为它是我热爱的语言(来自 Java 生态系统)。你可以同意，也可以不同意我。我不介意；)

## 上下文

首先，我们需要一个有某种状态的程序在执行过程中发生变异。作为一个例子，让我们建立一个股票投资组合的简单模型。股票投资组合是将股票名称与所拥有的一些股票相关联的映射。

```
type Stocks = Map[String, Double] 
```

在我们对现实的描述中，客户可以对她的股票投资组合进行三种操作:买入更多的股票；出售拥有的股票；得到拥有的股票数量。我们知道，在函数式编程中对象是不可变的，这三个函数不能对它们的输入有任何副作用。因此，所有函数还必须返回更新后的银行帐户。

```
// Buys an amount (dollars) of the stock with given name. Returns the number
// of purchased stocks.
def buy(name: String, amount: Double, portfolio: Stocks): (Double, Stocks) = {
  val purchased = amount / Prices(name)
  val owned = portfolio(name)
  (purchased, portfolio + (name -> (owned + purchased)))
}
// Sells a quantity of stocks of the given name. Returns the amount of
// dollars earned by the selling operation.
def sell(name: String, quantity: Double, portfolio: Stocks): (Double, Stocks) = {
  val revenue = quantity * Prices(name)
  val owned = portfolio(name)
  (revenue, portfolio + (name -> (owned - quantity)))
}
// Returns the quantity of stocks owned for name.
def get(name: String, portfolio: Stocks): Double = portfolio(name) 
```

我们想象生活在一个充满有趣和独角兽的彩色世界，所以我们的投资组合总是包含一些给定名称的股票。

## 问题

既然我们已经定义了我们的基本功能，我们想要创建另一个功能，将它们组合起来创建一个新的用例:

1.  出售给定公司的所有股票
2.  利用出售的收入，购买另一家公司的股票
3.  返回所拥有的第一类股票的数量和新购买股票的数量

仅仅使用函数`get`、`sell`和`buy`，我们可以如下开发新函数。

```
def move(from: String, to: String, portfolio: Stocks): ((Double, Double), Stocks) = {
    val originallyOwned = get(from)(portfolio)
    val (revenue, newPortfolio) = sell(from, originallyOwned)(portfolio)
    val (purchased, veryNewPortfolio) = buy(to, revenue)(newPortfolio)
    ((originallyOwned, purchased), veryNewPortfolio)
  } 
```

尽管上面的代码很简单，但毫无疑问，你会注意到我们必须手动将更新股票组合的正确引用传递给算法的每一步。这种编程非常繁琐并且**容易出错**。

我们需要一种机制来以更智能的方式组合处理应用程序状态的函数。最好是根本不用担心将应用程序的状态从一个函数调用传递到另一个函数调用。

> 像这样的尴尬几乎总是一些等待被发现的缺失抽象的迹象。

状态单子以这种方式帮助我们。

[![Show me the code!](img/c208a24b58288a21d25354f412b9bf45.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2B2BraNP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/2n7wbj.jpg)

## 通过单子的路径

引用本书"[学你一个 Haskell 大有好处！](http://learnyouahaskell.com/)

> 我们会说，有状态计算是一个函数，它接受某个状态并返回一个值以及某个新状态。

我们的`buy`和`sell`函数已经在这个意义上运行了。它们返回一个值和一个新的状态实例。然而，函数`get`只返回一个值，不更新状态。没问题。很容易改变它的定义，让函数返回一个值和一个状态:使用相同的输入状态。所以函数`get`的定义变成如下。

```
def get(name: String, portfolio: Stocks): (Double, Stocks) = (portfolio(name), portfolio) 
```

上面引用的有状态计算的类型是`S => (A, S)`，其中`S`是状态，`A`是函数执行产生的值。这种类型的功能被称为*状态转换*的*状态动作*。因此，我们可以将`buy`、`sell`和`get`函数重写如下。

首先，我们用一些*curry*将输入分成两组，并隔离状态。

```
def buy(name: String, amount: Double)(portfolio: Stocks): (Double, Stocks)
def sell(name: String, quantity: Double)(portfolio: Stocks): (Double, Stocks)
def get(name: String)(portfolio: Stocks): (Double, Stocks) 
```

如果我们只将第一组输入传递给这些函数，我们将精确地获得一组类型为`S => (A, S)`的函数。

```
def buyPartial(name: String, amount: Double): Stocks => (Double, Stocks) = 
  buy(name, amount)
// And so on... 
```

下一步是消除所有的谄媚。它不优雅，我们不喜欢它的语法；)

```
def buy(name: String, amount: Double): Stocks => (Double, Stocks) = portfolio => {
  val purchased = amount / Prices(name)
  val owned = portfolio(name)
  (purchased, portfolio + (name -> (owned + purchased)))
}
// And so on... 
```

只是为了提高我们三个函数的可读性，让我们定义下面的*类型别名*。

```
type Transaction[+A] = Stocks => (A, Stocks) 
```

我们的三个函数总是返回一个`Double`作为输出。然而，为了更加灵活，我们需要类型参数`A`来让`Stocks`类型上的函数返回任何值。

有了新的类型别名，我们的函数变成了下面这样。

```
def buy(name: String, amount: Double): Transaction[Double] = portfolio => {
  val purchased = amount / Prices(name)
  val owned = portfolio(name)
  (purchased, portfolio + (name -> (owned + purchased)))
}
def sell(name: String, quantity: Double): Transaction[Double] = portfolio => {
  val revenue = quantity * Prices(name)
  val owned = portfolio(name)
  (revenue, portfolio + (name -> (owned - quantity)))
}
def get(name: String): Transaction[Double] = portfolio => {
  (portfolio(name), portfolio)
}

// And finally...
def move(from: String, to: String): Transaction[(Double, Double)] = portfolio => {
  val (originallyOwned, _) = get(from)(portfolio)
  val (revenue, newPortfolio) = sell(from, originallyOwned)(portfolio)
  val (purchased, veryNewPortfolio) = buy(to, revenue)(newPortfolio)
  ((originallyOwned, purchased), veryNewPortfolio)
} 
```

唉，形势白白改善了。我们现在需要的是一种更好的方法来组合我们的函数，我们可以通过状态单子的定义完成最后一步:定义`map`和`flatMap`函数。

[![Do we have some improvements?](img/505c8a4d07545dbf5a705967ed3c55a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xi1kt8Ym--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/2n7wt5.jpg)

## 最后一步:组合函数

> 编程的力量在于组合，即组合不同操作以获得目标结果的能力。

构建状态单子的最后一步是定义一组函数，让我们灵活地组合状态，即`Transaction[+A]`实例。

我们需要的第一个函数是`map`。正如您可能已经知道的，`map`函数允许将一个函数应用到包含在一个通用数据结构中的类型，获得它的一个新版本。

在我们的例子中，`map`函数用于转换`Transaction[B]`中的`Transaction[A]`。

```
def map[A, B](tr: Transaction[A])(f: A => B): Transaction[B] = portfolio =>
  (a, newPortfolio) = tr(portfolio)
  (f(a), newPortfolio) 
```

然而，`map`函数的问题是它倾向于累积泛型类型。例如，对一个`Transaction[_]`、`map(/*... */)(a => map(/*...*/)(/*...*/)`重复应用`map`的结果就是`Transaction[Transaction[_]]`。因此，我们需要一个行为类似于`map`的函数，但是能够*展平*累积类型:函数`flatMap`。`flatMap`函数不是把从`A`到`B`的函数作为参数，而是把从`A`到`Transaction[B]`的函数作为参数。

记住`type Transaction[+A] = Stocks => (A, Stocks)`，`flatMap`函数确实是这样定义的。

```
def flatMap[A,B](tr: Transaction[A])(f: A => Transaction[B]): Transaction[B] = portfolio =>
  (a, newPortfolio) = tr(portfolio)
  f(a)(newPortfolio) 
```

很好。我们错过的最后一步是定义一个函数，让*将`A`类型的值*提升到`Transaction[A]`类型。可以把这个函数想象成面向对象编程中的工厂方法。

```
def apply[A](value: A): Transaction[A] = portfolio => (A, portfolio) 
```

使用我们刚刚定义的*组合子*，我们可以更干净地重写`move`方法。

```
def move(from: String, to: String): Transaction[(Double, Double)] =
  flatMap(get(from))(
    originallyOwned => flatMap(sell(from, originallyOwned))(
      revenue => map(buy(to, revenue))(
        purchased => (originallyOwned, purchased)
      )
    )
  ) 
```

`map`和`flatMap`函数的存在允许我们进一步提高`move`函数代码的可读性，只需应用*的语法糖用于理解*构造。使用`for...yield`语法，函数如下。

```
def move(from: String, to: String): Transaction[(Double, Double)] =
  for {
    originallyOwned <- get(from)
    revenue <- sell(from, originallyOwned)
    purchased <- buy(to, revenue)
  } yield {
    (originallyOwned, purchased)
  } 
```

哇！现在，代码看起来像是好的命令式代码，但是它保留了函数式代码的所有显著特征。*超级航母！！！*

## 总结归纳

是时候将上述概念抽象到我们的单子构造中了。首先，我们需要一个单子的类型。我们的股票示例使用类型`Stocks`来存储应用程序的状态。我们需要用一个通用类型变量`S`来代替它。`Transaction[A]`摘要到`State[S, A]`类型。

```
type State[S, A] = S => (A, S)
// And so...
type Transaction[A] = State[Stocks, A] 
```

一旦我们定义了`State[S, A]`类型，`unit`、`map`和`flatMap`函数如下。

```
def unit[S, A](a: A): State[S, A] = state => {
  (a, state)
}
def map[S, A, B](sm: State[S, A])(f: A => B): State[S, B] = state => {
  val (value, newState) = sm(state)
  (f(value), newState)
}
def flatMap[S, A, B](sm: State[S, A])(f: A => State[S, B]): State[S, B] = state => {
  val (value, newState) = sm(state)
  f(value)(newState)
} 
```

这就是我们的州单子！厉害！此外，关于类型`State`的类型`Transaction`的定义允许我们保持我们的业务功能的代码不变。不需要改变。

如果您愿意，可以为类型`State`定义一个`trait`。如果是这样的话，函数`unit`、`map`和`flatMap`就变成了特征的方法，而不是单独的函数。

[![Welcome to the state monad](img/94cf03d4ae4056c7cc2e299b12b17eee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sGyWQJbD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/2n7xa4.jpg)

### 多此一举

Scala 语言有很多好的函数库。Scalaz 和 T2 猫都有自己的状态单子实现。所以，以上只是为了更深入地理解状态单子概念的一个练习，但是还没有准备好投入生产使用；)

## 结论

在我们的旅程中，我们从一个需要共享某种状态的应用程序开始。许多函数修改这种状态。遵循函数式编程约束，我们试图避免使用可变状态。使用 curryfication 和一阶函数，我们构建了一个框架，允许我们在不同的函数之间共享状态。然后，我们引入了一些*组合子*，使得组合前面函数的过程更加自然，并且不容易出错。

我们最终满意的是状态单子的定义。

我在这篇文章中使用的代码可以在我的 GitHub 库中找到， [state-monad-example](https://github.com/rcardin/state-monad-example)

## 参考文献

*   [第六章:纯功能状态。Scala 中的函数式编程，p .丘萨诺，r .比雅纳松，2014，曼宁出版社](https://www.manning.com/books/functional-programming-in-scala)
*   [用 Scala 1/2 对状态单子去雾](http://patricknoir.blogspot.com/2014/12/demistify-state-monad-with-scala-12.html)
*   [学习 Scalaz——有品味的状态计算](http://eed3si9n.com/learning-scalaz/State.html)