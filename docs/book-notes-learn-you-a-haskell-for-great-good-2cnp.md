# 读书笔记:为了更好的学习 Haskell！

> 原文：<https://dev.to/nestedsoftware/book-notes-learn-you-a-haskell-for-great-good-2cnp>

在过去的几周里，我看了这本书[为了更好的学习你的 Haskell！Miran Lipovač。我很好奇，但也有点害怕学习 Haskell。随意细读一下，Haskell 代码看起来不太像我们许多人习惯的 Java、JavaScript、C#、Python、Ruby 等代码。像*函子*、*幺半群*和*幺半群*这样的术语会给人一种非常复杂的印象。](http://learnyouahaskell.com/)

幸运的是我偶然发现了 Miran 的教程。这绝对是对哈斯克尔最友好的介绍。虽然这本书并不完美——没有什么是完美的——但我发现它在介绍 Haskell 背后的核心概念方面非常通俗易懂。

这些笔记并不全面——它们只是对我来说很突出的东西的一种大脑转储，或者是有趣的，有用的，或者是棘手的。我还包括了一些我自己的想法、观察和代码示例。一如既往，欢迎讨论！

*LYAHFGG！*可以在网上免费获得，也可以从官方网站购买电子书。二手[印刷版](https://www.amazon.com/Learn-You-Haskell-Great-Good/dp/1593272839/ref=sr_1_1?ie=UTF8&qid=1543472622&sr=8-1&keywords=learn+you+a+haskell)也可以在亚马逊买到。

*LYAHFGG！*有 14 个章节的平面结构，但我倾向于将其分为三大部分:

1.  第 1-7 章:类型和类型类介绍；模式匹配；递归；高阶函数；模块
2.  第 8-10 章:创建我们自己的类型和类型类；输入输出；解决问题
3.  第 11-14 章:幺半群；函子；适用函子；单子；拉链

我发现前两部分相当容易理解，但是在我第一次尝试的时候，当我读到关于函子和单子的章节(第 11 章和第 12 章)时，我就失去了动力。我离开了一段时间，后来又回来了，决心这次要坚持到底。第二次试的时候，还不算太差。我只需要慢慢来，认真细致地完成每一件事。

## 第一部分

这些早期的章节是关于开始。Miran 做了一项伟大的工作，以一种温和的方式直接进入 Haskell 代码，避免了令人生畏的理论或符号。我们将学习函数、模式匹配和条件逻辑。

### 递归和高阶函数

还有递归函数的介绍和高阶函数的神圣三位一体，`map`、`filter`、`fold`(有些语言也叫`reduce`)。

### 模式匹配

对我来说，模式匹配是这本书最不寻常的地方。因为 Haskell 中的值是不可变的，所以有可能将一个值与它最初的构造方式相匹配！这个特性在 Haskell 中用得很多。

例如，我们可以定义一个自定义列表类型，并使用它来创建一个由值 3、4 和 5 组成的列表，如下所示:

```
Prelude> data List a = EmptyList | Cons a (List a) deriving (Show, Read, Eq)
Prelude> items = Cons 3 (Cons 4 (Cons 5 EmptyList)) 
```

我们可以如下模式匹配来获得列表中的第二项:

```
Prelude> secondItem (Cons first (Cons second rest)) = second
Prelude> secondItem items
4 
```

### 100%纯度

简介中提到 Haskell 中的所有函数都是*纯*。然而，人们很容易忽略这一点的重要性。这意味着函数永远不会有任何直接的副作用。如果一个函数看起来好像在做 I/O，不要被愚弄，它不是——至少不是直接的！

相反，这些函数返回*动作*。我们可以把这些想象成描述预期副作用的数据结构。当 Haskell 运行时执行一个动作时，它将实际执行 I/O，但它是作为一个单独的步骤来完成的。我认为这一点值得强调。我觉得这是 Haskell 最与众不同的一面。

### 懒评

Haskell 的另一个非常不寻常的核心特征是*懒惰*。在 Haskell 中，一个函数的计算只能满足`main`动作的需求(至少在默认情况下)。这意味着我们可以编写没有基本用例的函数，就像下面这样:

```
Prelude> recurseForever n = n  : recurseForever (n+1)
Prelude> print $ take 3 $ recurseForever 5
[5,6,7] 
```

> 我们可以省略 [ghci](https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/ghci.html) 中的`print`，结果是一样的。 [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) 会自动为我们制作一个打印动作。

为了满足`print`返回的动作，我们需要从`recurseForever`获取 3 个项目。一旦我们有了这些项目，评估就停止了。如果我们调用一个函数，但是它的结果从来没有被一个动作实际使用过，那么这个函数调用根本不会被求值。

当我们在 Haskell 中调用一个函数时，我们不会像预期的那样直接得到调用的最终结果。相反，我们得到了一个未赋值的表达式，有时被称为“T2”。当 Haskell 运行时执行由`main`产生的动作时，对 thunks 的评估由 Haskell 运行时驱动。

### 逢迎

同样值得注意的是，在 Haskell 中，所有的函数都是自动*化的*。一个看似带三个参数的函数，实际上带一个参数，返回一个带一个参数的函数，最终返回一个带一个参数的函数！

当参数返回时，这些函数中的每一个都捕获从封闭范围传入的参数。因此，我认为熟悉来自另一种语言如 JavaScript 或 Python 的*闭包*可能会有所帮助。

Haskell 中的 Currying 允许用非常简洁的*点自由*符号编写代码。这也意味着参数可以部分地应用于一个函数，而不需要首先将其包装在 lambda 中。

> 无指针符号可能很好，但它也可能被误用，使代码更难理解。将*一切*不加选择地转换成点自由形式是一种反模式，应该避免。

在下面的代码中，`2`部分应用于乘法函数`(*)`。`map`然后通过将列表中的每一项作为乘法运算的第二个参数来完成任务:

```
Prelude> print $ take 5 $ map (*2) [0..]
[0,2,4,6,8] 
```

### 作文

Currying 使得组合函数变得相当容易，即生成一个将一堆函数组合在一起的函数。为了构造函数，我们使用高阶函数`.`。这里有一个例子，展示了如何使用组合将前面的例子快速包装成一个函数:

```
Prelude> composed = print . take 5 . map (*2)
Prelude> composed [0..]
[0,2,4,6,8] 
```

### 类型变量

Haskell 使得创建参数化类型变得容易。这些类似于 C++中的模板或 Java 中的泛型。

### 式推断

Haskell 非常酷的一点是它使用了类型推断。这意味着我们不必到处显式定义类型。在许多情况下，编译器可以从代码的使用方式中为我们找出答案。除了 repl 之外，这个特性让 Haskell 感觉更像 JavaScript 或 Python，而不是典型的静态类型语言。

## 第二部分

本书的这一部分包括创建定制类型和类型类(*接口*是 Java 和 C++等语言中的类似概念)。还讨论了 Haskell 中 I/O 的工作方式。最后，解决了几个问题，一个 RPN 计算器和一个寻路算法。

### 输入输出

这里介绍了*动作*的概念。基本上`main`产生一个动作——它可以是几个其他动作的组合。Haskell 运行时然后实际执行这个动作。发生的其他事情都源于完成这个动作所需的函数的评估。

### 类型和类型类

对我来说，对类型和类型类的详细讨论是本书这一部分最重要的部分。特别是 Miran 提到 Haskell 中的值构造函数也只是函数。例如，`Just 3`中的`Just`是一个函数。我第一次读的时候没有注意到这一点，后来在`State`单子的讨论中变得有点困惑。

同样，记住函数是 Haskell 中的一等公民是很有用的，因此值构造函数可以包含函数以及任何其他值。

记录语法是我发现容易混淆的另一个领域。记住记录语法只是常规值构造函数周围的语法糖是有帮助的。它会自动添加产生所需值的函数。

为了说明上述观点，我创建了一个小例子。`TypeWithFunctions`是包含两个函数作为 a 值的数据类型。`Val`是值构造器。函数`getF1`提取第一个函数，`getF2`从`TypeWithFunctions`值:
中提取第二个函数

```
Prelude> data TypeWithFunctions = Val (Int->Int) (Int->Int)
Prelude> getF1 (Val f _) p = f p
Prelude> getF2 (Val _ f) p = f p
Prelude> vwf = Val (\x->x+1) (\x->x*2)
Prelude> getF1 vwf 3
4
Prelude> getF2 vwf 3
6 
```

或者，我们可以使用记录语法来实现相同的结果。在这里，我们使用记录语法创建自定义的`TypeWithFunctions`。Haskell 将自动创建函数`getF1`和`getF2`来返回它们相应的值(也是函数)。下面的代码相当于前面的例子:

```
Prelude> data TypeWithFunctions = Val { getF1 :: Int->Int, getF2 :: Int->Int }
Prelude> vwf = Val {getF1 = \x->x+1, getF2 = \x->x*2}
Prelude> getF1 vwf 3
4
Prelude> getF2 vwf 3
6 
```

另一个有趣的想法是，值构造函数可以引用自己的类型，这让我们可以构建递归数据结构。例如:

```
data Tree a = EmptyTree | Node a (Tree a) (Tree a) deriving (Show, Read, Eq) 
```

这里的`Node`值构造函数有三个参数:一个类型为`a`的值，它表示当前节点的值，以及两个类型为`Tree a`的值，它们指向更多的树！这些树将把它们自己解析成`EmptyTree`值，或者它们将成为进一步的节点，从它们分支出两个以上的树。这就是如何在 Haskell 中实现二叉树。

## 第三部分

这是这本书内容最丰富的部分。它涵盖了幺半群，以及函子，适用函子和幺。

最后一章展示了如何使用 *zipper* 来遍历数据结构。

### 类型构造函数的分部应用

关于类型类，在关于 [`newtype`](http://learnyouahaskell.com/functors-applicative-functors-and-monoids#the-newtype-keyword) 的章节中提到了一个巧妙的技巧。正如我们可以部分应用函数一样，我们也可以部分应用类型构造函数。在这里，我比那本书更详细地解决了这个问题。让我们从`Functor`类型类的定义开始:

```
class Functor f where  
    fmap :: (a -> b) -> f a -> f b 
```

我们在这里可以看到,`f`必须是具有单一类型参数的类型。

假设我们有一个表示一对值的元组，并且这对值中的每个值可能是不同的类型。我们试着把这个元组做成一个函子。

```
Prelude> newtype Pair s n = Pair (s, n) deriving Show
Prelude> Pair ("hello", 3)
Pair ("hello", 3) 
```

由于元组被参数化为两种类型`s`和`n`，我们不能直接使用它来实现`Functor`类型类。然而，我们可以将其类型部分绑定到单个参数，这样`fmap`就可以自由地操作元组中的其他值。下面我们将`s`(元组中第一个值的类型)部分应用到`Pair`。结果是一个需要多一个类型参数的类型。因此，我们可以为这个类型实现`Functor`type class:

```
Prelude> instance Functor (Pair s) where fmap f (Pair(x,y)) = Pair(x, f y)
Prelude> fmap (+3) (Pair("hello", 1))
Pair ("hello", 4) 
```

如果我们想映射元组中的第一个值而不是第二个值，我们该怎么做？这就是技巧发挥作用的地方。我们可以颠倒值构造函数中类型参数的顺序。这允许我们映射元组中的第一个值:

```
Prelude> newtype Pair s n = Pair (n, s) deriving Show -- flipped order in value constructor
Prelude> Pair (3, "hello")
Pair (3, "hello")
Prelude> instance Functor (Pair s) where fmap f (Pair(x,y)) = Pair(f x, y)
Prelude> fmap (+3) (Pair(1, "hello"))
Pair (4, "hello") 
```

### 声名狼藉的`>>=`函数和`do`符号

`do`符号在本书第 9 章的 I/O 上下文中介绍过。这里我们了解到`do`语法只是返回单子的表达式的语法糖。

I/O 动作恰好是单子的一种类型，但是`do`语法可以用来*顺序地*将操作任何单子的函数链接在一起。

让我们来看一个动作`multWithLog`，它产生一个叫做`WWriter`的单子。我们将避免 Haskell 中内置的`Writer`,在这个例子中使用我们自己的:

```
import Control.Monad (liftM, ap)

main = print $ runWriter $ multWithLog

multWithLog = do
    a <- logNumber 3
    b <- logNumber 5
    c <- logNumber 8
    tell ["Let's multiply these numbers"]
    return (a * b * c)

tell xs = WWriter ((), xs)

logNumber n = WWriter (n, ["Got number: " ++ show n])

newtype WWriter logs result = WWriter { runWriter :: (result, logs) }

instance (Monoid w) => Functor (WWriter w) where
    fmap = liftM

instance (Monoid w) => Applicative (WWriter w) where
    pure = return
    (<*>) = ap

instance (Monoid w) => Monad (WWriter w) where
    return result = WWriter (result, mempty)
    (WWriter (r, l)) >>= f = let (WWriter (r', l')) = f r in WWriter (r', l <> l') 
```

> 当 *LYAHFGG！写了*，我认为`Monad` typeclass 没有显式扩展`Applicative`。现在它做到了，所以如果我们想把一个类型转换成单子，我们也必须为它实现`Functor`和`Applicative`。事实证明，使用`liftM`和`ap`很容易做到这一点。

运行这段代码的结果看起来*有点像预期的*:

```
C:\Dev\haskell>ghc writer_example.hs
[1 of 1] Compiling Main             ( writer_example.hs, writer_example.o )
Linking writer_example.exe ...

C:\Dev\haskell>writer_example.exe
(120,["Got number: 3","Got number: 5","Got number: 8","Let's multiply these numbers"]) 
```

很容易想象这段代码相当于下面的 JavaScript:

```
console.log(multWithLog())

const multWithLog = () => {
    a = logNumber(3)
    b = logNumber(5)
    c = logNumber(8)
    console.log("Let's multiply these numbers")
    return a * b * c
}

const logNumber = n => {
    console.log("Got number: " + n)
    return n
} 
```

但事实并非如此:我们不能在 Haskell 中直接进行 I/O。`do`符号可以很容易地转换成对`bind`也就是`>>=`的调用。`multWithLog`中的 Haskell `do`符号代码可以重写如下:

```
multWithLog = logNumber 3 >>=
  \a -> logNumber 5 >>=
    \b -> logNumber 8 >>=
      \c -> tell ["Let's multiply these numbers"] >>=
        \_ -> return (a * b * c) 
```

这是怎么回事？为了让它更清楚，我已经尽可能地将这个例子翻译成了下面的 JavaScript】

```
const multWithLog = () => {
  const w = chain (logNumber(3), a =>
    chain(logNumber(5), b =>
      chain(logNumber(8), c =>
        chain(tell(["Let's multiply these numbers"]), _ =>
          monad(a*b*c)))))

  return w
}

const Writer = function (result, logs) {
  this.result = result
  this.logs = logs
}

// equivalent of Haskell "return"
const monad = n => new Writer(n, [])

//equivalent of Haskell ">>="
const chain = (writer, f) => {
  const r = writer.result
  const l = writer.logs
  const newWriter = f(r)
  return new Writer(newWriter.result, l.concat(newWriter.logs))
}

const logNumber = n => new Writer(n, ["Got number: " + n])

const tell = logs => new Writer([], logs)

console.log(multWithLog()) 
```

> 在 Haskell 中，`>>=`函数被称为 *bind* ，但在这里我将其命名为`chain`，因为 JavaScript 有自己的、不相关的`bind`函数。Haskell 还使用(名不副实的)`return`函数将值放入最小一元上下文中。当然`return`是保留的，所以我调用了这个函数`monad`。

现在所有的 Javascript 函数都是纯函数，就像 Haskell 代码一样，获取`w`不会产生任何副作用。结果只是一个`Writer`对象:

```
C:\Dev\js\fp>node monad_writer.js
Writer {
  result: 120,
  logs:
   [ 'Got number: 3',
     'Got number: 5',
     'Got number: 8',
     'Let\'s multiply these numbers' ] } 
```

我们使我们所有的函数变得纯粹，但是我们也可以清楚地看到在这段 JavaScript 代码中可怕的*回调地狱*的出现:我们将一个回调传递给`chain`，在这个回调中，我们做另一个*链*来接受另一个回调，等等。更糟糕的是，因为我们需要参数`a`、`b`、`c`等。为了在每个嵌套的作用域中可见，回调必须保持内联。它们不能简单地提取到单独的命名函数中。这相当混乱，我认为这说明了为什么 Haskell 引入了`do`语法。

所有这些的结果似乎是，我们可以扭曲 Haskell，使其看起来像日常的过程代码！😊我们这样做是以更高的复杂性为代价的。当然，我们可以用语法糖掩盖一些复杂性，但它仍然存在。

> 我相信这种 Haskell 代码确实会给程序员带来更大的精神负担，让他们去完成在命令式语言中非常简单的任务。如果我没理解错的话，交换条件是我们得到了纯洁。我不相信这种权衡总是值得的。我确信在某些情况下，它确实提供了显著的好处，但对我来说，这不是我们应该一直追求的目标。
> 
> 我能够理解将*业务逻辑*分离成纯函数并让 I/O 代码调用这些函数的价值。对我来说，不太清楚的是每段代码的价值，这些代码执行 I/O，返回一个稍后执行的动作。
> 
> 至少，我认为更高级别的间接使得这样的 Haskell 代码更难维护。事实上，这还不是故事的结尾。 *LYAHFGG！*不包括 monad 变形金刚，它增加了一个额外的间接层！

### 函数*作为*函子、应用和单子

虽然术语*幺半群*、*函子*、*适用的*和*幺半群*可能听起来陌生而复杂，但在很大程度上，这本书很好地揭示了它们的奥秘。首先，我们学习如何将简单类型如`Maybe`、`Either`和列表看作函子、应用函子和单子。从这个意义上说，它们只不过是容器类型，允许我们以一种标准化的、可预测的方式将映射应用到它们包含的值。

当我发现函数本身的概念`(->) r`可以被视为函子、应用函子和单子时，事情变得有点棘手。这本书没有详细展示推导过程，所以我最终自己完成了更详细的工作。对我来说，这是整个经历中最具挑战性的部分。

以下是所有的实现:

```
instance Functor ((->) r) where  
    fmap = (.)  

instance Applicative ((->) r) where  
    pure x = (\_ -> x)  
    f <*> g = \x -> f x (g x)  

instance Monad ((->) r) where  
    return x = \_ -> x  
    g >>= f = \x -> f (g x) x 
```

这里的想法是，函数成为值的上下文或容器。就像我们可以从`Just 3`中提取`3`一样，我们可以通过调用函数`(->) r`来提取一个值。

当一切都结束后，函数的`fmap`(又名`<$>`)被实现为函数组合。`<*>`原来是一个我不熟悉的相当奇怪的函数。我查了一下，它显然叫做一个 [S 组合子](https://en.wikipedia.org/wiki/SKI_combinator_calculus)。最后一个，看起来很熟悉，不是吗？事实上，这是我们的 S 组合子，参数翻转过来了！

```
Prelude> f <*> g = \x -> f x (g x)
Prelude> a = \x->(\y->x+y)
Prelude> b = \x->x*2
Prelude> resultingF = a <*> b
Prelude> resultingF 12
36
Prelude> g >>= f = \x -> f (g x) x
Prelude> resultingF = b >>= a
Prelude> resultingF 12
36 
```

对于函数，我们也可以将`<*>`实现为:

```
Prelude> (<*>) = flip (>>=) 
```

有趣的是，虽然`(->) r`的这些结果很有趣，但我不认为它们在现实世界的编程问题中出现得多。然而，我确实认为努力开发对 Haskell 这方面的理解是值得的。首先，它清楚地表明了 Haskell 是如何正交的，以及 Haskell 中的核心函数是如何重要的。从这个意义上说，认识到函数可以作为这些类型类的实例来实现是很重要的。

> 事实上，列表和函数都可以以多种方式实现为单子(当我们想要为同一底层类型创建给定类型类的多个实现时，可以使用`newtype`，例如，参见 [ZipList](http://learnyouahaskell.com/functors-applicative-functors-and-monoids#the-newtype-keyword) )。

我认为函数可以是函子、应用程序和单子的这个主题可以放在它自己的章节里。就目前的情况而言，它在关于函子、应用和单子的章节中单独讨论。当我在阅读时，没有什么可以强调的，这是比周围的材料更难消化的东西，我几乎错过了它。我记得当时我正得意洋洋地看书，突然问道:“等等，什么？”😊

### 单子>适用子>函子

事实证明，当我们从函子，到应用函子，到单子，我们得到越来越强大的结构。如果我们已经为一个给定的类型实现了`Monad` typeclass，那么我们可以用它来实现仿函数和适用仿函数 typeclasses。

我不确定这在 *LYAHFGG 中的呈现方式！*再清楚不过了。我发现[来自](https://en.wikibooks.org/wiki/Haskell/Applicative_functors#A_sliding_scale_of_power)[哈斯克尔维基百科](https://en.wikibooks.org/wiki/Haskell)的这个解释既清晰又简洁:

> 函子、应用和单子用法的日常差异是由这三种映射函数的类型所决定的。当你从 fmap 转移到(< * >)再到(> > =)，你获得了力量、多功能性和控制力，代价是对结果的保证。

我已经展示了一个关于`WWriter`的例子，它展示了一旦我们实现了`Monad` typeclass，我们如何免费获得`Functor`和`Applicative`。下面是另一个*状态*单子的工作示例。我把它叫做`SState`，以区别于内置的`State`类型:

```
import System.Random

import Control.Applicative
import Control.Monad (liftM, ap)

main = print $ runState threeCoins (mkStdGen 33)

threeCoins :: SState StdGen (Bool, Bool, Bool)
threeCoins = do
    a <- randomSt
    b <- randomSt
    c <- randomSt
    return (a,b,c)

randomSt :: (RandomGen g, Random a) => SState g a  
randomSt = SState random

newtype SState s a = SState { runState :: s -> (a,s) }  

instance Functor (SState s) where
    fmap = liftM

instance Applicative (SState s) where
    pure = return
    (<*>) = ap

instance Monad (SState s) where  
    return x = SState $ \s -> (x,s)  
    (SState h) >>= f = SState $ \s -> let (a, newState) = h s  
                                          (SState g) = f a  
                                      in  g newState 
```

> 注意`SState`只是一个函数的包装器。这让我第一次遇到它的时候吓了一跳，而且我不认为它在*里被直接提到了！*在此之前。这就是为什么我在本文前面更详细地讨论了`TypeWithFunctions`。

让我们编译并运行它:

```
C:\Dev\haskell>ghc random_state.hs
[1 of 1] Compiling Main             ( random_state.hs, random_state.o )
Linking random_state.exe ...

C:\Dev\haskell>random_state.exe
((True,False,True),680029187 2103410263) 
```

下面是`liftM`和`ap`的实现:

```
liftM :: (Monad m) => (a -> b) -> m a -> m b  
liftM f m = m >>= (\x -> return (f x))

ap :: (Monad m) => m (a -> b) -> m a -> m b  
ap mf m = mf >>= \f -> m >>= \x -> return (f x) 
```

### 法则

对于三大类型类中的每一个，`Functor`、`Applicative`和`Monad`，除了类型定义之外，还有在实现它们时应该遵循的规则。这些被称为函子、应用和单子的*定律*。Haskell 并不强制执行这些法律，所以有可能以不符合这些法律的方式实现这些 typeclasses。然而，这些规则应该遵守。否则，使用给定 typeclass 的程序员可能会遇到意想不到的行为。

*LYAHFGG！*倾向于在例子之间穿插这些规律。我明白这本书的目标是关注实际应用，而不是理论或阐述，但我确实觉得这有点令人困惑。这里是所有的类型类和相关的法律都在一个地方: