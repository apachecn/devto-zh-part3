# 知道什么时候折叠它们

> 原文：<https://dev.to/deciduously/know-when-to-fold-em-1466>

你有喜欢的功能吗？我知道。真的是函数一族，但是今天我要说的是 *folds* 。

我将用 Haskell 来举例说明，但是这个概念不是 Haskell 特有的，你也不需要非常熟悉 Haskell 来阅读例子。在第一部分中，我解释了什么是折叠，在第二部分中，我们根据折叠重新实现了一些库函数。

## 什么是折叠？

在主流语言中，折叠并不是一个不常见的概念——如果你觉得舒服，就跳过这一节。如果不是，那么了解它们是如何工作的会有所帮助。

在纯函数范式中，我们获取一个值集合并确保对该集合的每个成员做一些事情的方式是递归地使用该集合。也就是说，我们将把整个集合传递给某种函数，它将进行某种处理。在函数的末尾，它会再次调用自己，用列表中的一小部分作为参数，这部分我们还没有通过函数处理。它会一次又一次地这样做，用集合中越来越小的部分来调用自己，直到整个事情都被处理完。很简单。`fold`是一种特定类型的递归函数，它接受一个数据结构、某种类型的集合和用于每个成员的函数——特别是每次迭代中集合的第一个元素。在列表被完全清空后，它最终只产生一个值。

我发现在 Haskell 中谈论类型比用英语更容易。下面是`foldr` :
的类型签名

```
foldr :: (a -> r -> r) -> r -> [a] -> r 
```

Enter fullscreen mode Exit fullscreen mode

如果你茫然地看着这个没关系，这通常是解开类型签名的第一步。不过，它们的工作方式都是一样的，所以我们可以自己走过去。我们知道这是一个带三个参数的函数，因为最终所有的值都是一个值——所以编译器在处理这个函数以到达最后的`r`时会期望三位信息。类型签名中的括号如您所料——第一部分被分组，表示它是一个类型为`a -> r -> r`的参数，而不是三个独立的参数。第二个未知类型通常用`b`表示——我用`r`表示它是我们的返回类型。如果你去网上查这个，你可能会看到一个`b`。什么类型不重要，什么都有可能。第二种类型的占位符甚至可以是另一个`a`，而且经常是这样，但是它没有*来保证函数的正确性，所以我们使用不同的字母。*

首先是我们的处理功能。这本身就是一个带有两个参数的函数。它接受我们的`[a]`的单个元素，或者一个`a`类型的列表，以及我们要返回的类型的一些值，并返回一个具有我们期望的返回类型的新值。下一个参数是返回类型单个实例——可以说是“目的地”。我们知道我们将从这个文件夹中得到一个单一的值，我们有一个函数，它获取一个单元格和我们当前的运行结果，并返回给我们新的结果，所以我们可以在下一次运行递归时删除这个单元格。不过，在第一次运行时，我们需要存放计算结果的地方，所以`foldr`要求一个容器作为第二个类型为`r`的参数，以应用结果。我们传入的这个初始值将在每次运行函数时被转换，并最终成为返回的值。

如果这些都太抽象了，这里有一个看起来更熟悉的简单例子——让我们将一些基本的添加内容合并到一个集合中:

```
nums :: [Int]
nums = [1, 2, 3, 4, 5]

addEmUp ns :: [a] -> r
addEmUp ns = foldr (+) 0 ns 
```

Enter fullscreen mode Exit fullscreen mode

那就少了很多噪音。在这个例子中，调用`addEmUp nums`将产生`15 :: Int`。首先，我定义了一个`[Int]`——一列`Int`——称为`nums`。然后我创建了一个函数`addEmUp`，它实际上是一个特定函数`fold`的别名——注意，它除了指定 fold 使用哪些参数之外，什么也不做。这就是为什么`addEmUp`的类型签名要简单得多——它只接受`[a]`集合，在本例中是`nums`。所以我们的`a`就是`Int`。第一个参数，处理器，是`(+)`——加法运算符。运算符是函数，这个函数接受两个值并产生第三个值。来对比一下我们预期的类型:`a -> r -> r`。在这种情况下,`a`是`Int`,我们希望在末尾有一个`Int`,所以我们也可以用那个类型替换`r`。如果你在一个`Int`上加一个`Int`，你瞧，一个`Int`就会弹出来。所以我们的处理器，加法，有类型`Int -> Int -> Int`，这符合！如果`a`和`r`或者任何两个未指明的类型相同，完全没问题，我们只是注意到它们没有*有*存在。

我们的第二个争论只是一个`0`——一个`Int`。我们刚刚决定这是一个完美的`r`类型，所以第二个参数作为我们返回类型的初始化器是有意义的。那就只剩下`[a]`了。谢天谢地，我们原封不动地保留了这部分类型，并把它作为参数传递给`addEmUp`！对于这个简单的例子，这个`foldr`的完全限定类型是:`(Int -> Int -> Int) -> Int -> [Int] -> Int`。就是一堆`Int`的。

当 Haskell 进行评估时，它将从完整的集合开始。当我们第一次运行时，处理器将获取第一个单元格，然后寻找我们的累计结果。我们还没有做任何事情，所以它只是`0`——我们在第二个论点中告诉它了。第一个值是`1`。我们的累加器加到我们的基值上就是`1`。然后，我们复发！只是这一次我们已经处理了一个，所以我们再次调用这个相同的函数，但只是在集合的其余部分，并使用我们新创建的`1`作为累加器，而不是基值`0` :

```
foldr (+) 0 [1, 2, 3, 4, 5]
foldr (+) 1 [2, 3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

看到那里发生了什么吗？我们处理了一个并丢弃了它，所以我们的收集变得更短，我们有一个运行总数。展开:

```
 foldr (+) 3 [3, 4, 5]
= foldr (+) 6 [4, 5]
= foldr (+) 10 [5]
= foldr (+) 15 []
= 15 
```

Enter fullscreen mode Exit fullscreen mode

当一个递归函数试图在一个空列表上递归时，它知道自己完成了，并返回最终值——在本例中是`15`。我们成功地实现了无循环迭代！相反，我们在`[1 + 2 + 3 + 4 + 5]`中加入了一个操作。这几乎就像我们用运算符从右到左一步一步地替换逗号一样。通过这种方式，我们能够一次又一次地重用同一个函数，同时只根据上一次运行的输出改变我们传入的内容。递归，哟。

如果这听起来非常低效，那么一直用非常相似的值调用一个又一个函数，是的。为了减少这种开销，Haskell 执行了一种叫做“[尾调用](https://en.wikipedia.org/wiki/Tail_call)优化”的东西，我在这里就不详细介绍了，但本质上是说，如果函数在“尾位置”递归，这意味着它是要执行的函数的最后一部分，而不是为每个后续调用分配一个新的堆栈帧，它能够重用同一个堆栈帧并替换新的 val，然后就像`GOTO`风格那样向上跳转执行。如果你不熟悉堆栈框架，我们已经超出了这篇文章的范围——这不是我们所需要的知识，但是总体来说很有趣，如果你想在 anger 中使用函数式语言，理解这一点很重要。在玩具程序中，优雅的函数式解决方案通常是好的，但是当你的应用程序扩展时，它会开始引起问题，允许更混合风格的语言通常会建议你在这一点上退回到更命令式的模式。根据经验，一个好的老式`for`循环在处理大量数据时会比使用`forEach`或类似工具的一行程序表现得更好——有时好几个数量级。在 Haskell 中，处理这些性能问题还涉及其他类型的模式，因为没有什么`for`循环可言。我建议你四处转转！

这个例子可以重写:`addEmUp = foldr (+) 0` -如果参数是定义和参数列表中的最后一个术语，那么可以删除它。这个过程在 lambda 微积分行话中被称为 [eta 缩减](https://en.wikipedia.org/wiki/Lambda_calculus#%CE%B7-conversion)。相反，编译器将该定义视为一个期待多一个值的 curried 函数。如果用那个值调用它，它将完全评估表达式。

## 天下一折

为了说明这个函数有多有用，让我们根据文件夹重写一些其他常见的标准函数。

现在，`foldl'`在这里可能更合适——我不会纠结于什么时候选择哪一个。Haskell Wiki 有更多的信息，为了一致起见，这些都是正确的文件夹。

如果你已经安装了`ghc`，你可以跟随`ghci`，或者如果你喜欢把这些放在一个文件中:

```
-- stdFolds.hs
module StdFolds where

-- the functions! 
```

Enter fullscreen mode Exit fullscreen mode

然后在 REPL 用`:l stdFolds.hs`加载该模块。

### 或

我们从`Or`开始。我们想要一个可以“或”列表的函数——也就是说，如果它的任何成员是`True`，则返回`True`，否则返回`False`。我们函数的类型很简单:

```
myOr :: [Bool] -> Bool 
```

Enter fullscreen mode Exit fullscreen mode

我们传入一个`Bool`列表，只想要回一个。这意味着我们的累加器参数应该只是一个`Bool`。对于列表中的每一个元素，我们都希望与这个累加器进行比较，并用合适的值替换它——内置的`(||)`操作符会做到这一点！如果两边都是`False`，我们将只保留一个`False`，否则累加器将以`True`结束——当与任何其他布尔值
进行比较时，*将总是*返回`True`

```
myOr :: [Bool] -> Bool
myOr = foldr (||) False 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 eta 缩减，就像上面一样——我们的数据结构本身从定义中删除了。示例输出:

```
*StdFolds> let a = [True, False, True]
*StdFolds> myOr a
True
*StdFolds> let b = [False, False, False]
*StdFolds> myOr b
False 
```

Enter fullscreen mode Exit fullscreen mode

### 任何

现在让我们编写一个函数来检查给定的`[a]`中的任意`a`是否满足谓词`a -> Bool` :

```
myAny :: (a -> Bool) -> [a] -> Bool 
```

Enter fullscreen mode Exit fullscreen mode

我们实际上执行的功能与`myOr`几乎相同——唯一的不同是，我们不是仅仅使用集合中的元素，而是通过谓词传递它。我们可以用`(||)`操作符:
组成我们的谓词

```
myAny :: (a -> Bool) -> [a] -> Bool
myAny f = foldr ((||) . f) False 
```

Enter fullscreen mode Exit fullscreen mode

示例输出:

```
*StdFolds> let a = [0, 1, 2]
*StdFolds> myAny (== 3) a
False
*StdFolds> myAny (== 1) a
True
*StdFolds> 
```

Enter fullscreen mode Exit fullscreen mode

不过，我们其实可以做得更好。我们已经删除了 eta 减少的集合，但是使用超级方便的`flip`函数我们可以重新排列，所以我们的`f`也在最后:

```
myAny :: (a -> Bool) -> [a] -> Bool
myAny f = flip foldr False ((||) . f) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以通过简单地使用函数组合来完全放弃它:

```
myAny :: (a -> Bool) -> [a] -> Bool
myAny = flip foldr False . ((||) .) 
```

Enter fullscreen mode Exit fullscreen mode

这个版本和我们的第一次尝试是一样的，只是不杂乱。

### [元素](#elem)

`elem`函数只是`any`的一个特例，有一个特定的谓词。因为我们已经重新安排了`myAny`在最后的位置取函数，所以我们可以使用:

```
myElem' :: Eq a => a -> [a] -> Bool
myElem' = myAny.(==) 
```

Enter fullscreen mode Exit fullscreen mode

我们已经对参数进行了 eta 缩减，但是我们仍然可以对它应用`Eq`的 typeclass 约束。

示例输出:

```
*StdFolds> let a = [0, 1, 2]
*StdFolds> myElem 3 a
False
*StdFolds> myElem 1 a
True 
```

Enter fullscreen mode Exit fullscreen mode

不使用`myAny`我们只是使用同一个函数体:

```
myElem :: Eq a => a -> [a] -> Bool
myElem = flip foldr False . ((||) .) . (==) 
```

Enter fullscreen mode Exit fullscreen mode

### 地图

我们将使用类似的模式来构建我们自己的`map`。这有一个稍微不同的签名——给定一个从`a`到`b`的函数，我们想从`[b]` :
得到一个`[a]`

```
myMap :: (a -> b) -> [a] -> [b] 
```

Enter fullscreen mode Exit fullscreen mode

与`myAny`的不同之处在于，我们不是仅仅累加到一个`Boolean`中，而是想要构造一个新的列表。我们可以使用列表构造操作符`(:)` :
，而不是用`(||)`来比较累加器

```
myMap :: (a -> b) -> [a] -> [b]
myMap = flip foldr [] . ((:) .) 
```

Enter fullscreen mode Exit fullscreen mode

不然也是一回事！示例输出:

```
*StdFolds> let a = [0, 1, 2]
*StdFolds> let f = (* 2)
*StdFolds> myMap f a
[0,2,4]
*StdFolds> 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个[要点](https://gist.github.com/deciduously/8b2babeb07782c046aa0dab9f1392634)。