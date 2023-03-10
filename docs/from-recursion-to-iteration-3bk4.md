# 从递归到迭代

> 原文：<https://dev.to/pbouillon/from-recursion-to-iteration-3bk4>

大家好！

在这篇文章中，我们将看到如何从一个简单的递归解决方案到它的迭代版本。

# 定义

**递归**基本上就是当一个元素调用自己的时候。使用递归解决问题的目的是将问题分解成更小的版本，更容易解决。

有很多递归类型(尾式、创成式等。)并且所有这些都或多或少更容易理解和/或实现；然而，几乎每个算法都可以使用递归或迭代来找到相同的结果。问题是有时从一种方式转换到另一种方式可能很难。

# 练习

让我们看一个简单的例子。

```
Given `l` an array of integers and `i` an integer, return the number of occurences of `i` in `l`. 
```

Enter fullscreen mode Exit fullscreen mode

## 递归求解

这个问题没什么大不了的，很容易用递归解决。

我们将需要:
-一个*基础用例*:一个将停止我们的递归以避免无限循环的条件；
-处理:我们将如何处理和加工给定的论点；
-递归调用:改变我们的参数来处理问题的下一部分。

这里那些部分似乎相当明显:
-基本情况:如果列表中没有更多的元素，我们可以停止；
-处理:如果第一个元素是我们正在寻找的数字，我们将增加出现次数的计数器；
-递归调用:我们将继续遍历列表的尾部。

*Nb*:
T5】如果你不熟悉一个列表中的`head`和`tail`，这里有一个表示:

```
[   1,   2, 3, 4, 5, 6 ]
| head |     tail      | 
```

Enter fullscreen mode Exit fullscreen mode

### 实现

```
 def rec_occurrence(l: List[Int], i: Int): Int = {
        // base case
        if (l == Nil) {
            0
        }
        // recursion
        else if (l.head == i) {
            1 + rec_occurrence(l.tail, i)
        }
        else {
            rec_occurrence(l.tail, i)
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 递归解(bis)

我们之前的解决方案工作得非常好，但不会像现在这样带我们去任何地方:我们必须首先把它改成**尾递归**。尾递归是指递归算法的最后一次调用是递归本身。

在这个例子中，我们有这样的东西:

```
 1 + rec_occurrence(l.tail, i) 
```

Enter fullscreen mode Exit fullscreen mode

而不仅仅是函数调用。

### 实现

为了解决这个问题，我们将使用一个累加器，一个作为参数保存的计数器。

我们要构建一个嵌套函数，作为第一个函数工作，总线使用这个参数。然后 main 函数将调用我们的嵌套函数。

所有这些导致:

```
 def rec_acc_occurrences(l: List[Int], i: Int): Int = {

        def lambda_occurrence(l:List[Int], i:Int, acc:Int): Int = {
            // base case
            if (l == Nil) {
                acc
            }
            // recursion
            else {
                if (l.head == i) {
                    lambda_occurrence(l.tail, i, acc + 1)
                } else {
                    lambda_occurrence(l.tail, i, acc)
                }
            }
        }

        rec_acc_occurrences(l, i, 0)
    } 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们最后调用的只是函数:

```
lambda_occurrence(l.tail, i, acc + 1)
// or
lambda_occurrence(l.tail, i, acc) 
```

Enter fullscreen mode Exit fullscreen mode

问题解决了！

## (最终迭代解)

从我们制作的最后一个函数`rec_acc_occurrences`，我们可以构建我们的递归实现。

在 Scala 中，我们首先需要复制给定的参数，以便以后使用和修改它们。

然后，我们将使用一个`while`循环来放置我们的逻辑。这个`while`的中断条件将避免我们陷入无限循环:基本情况条件。然后，在这个循环的末尾，我们将像在递归调用中一样更新我们的参数，以覆盖列表中的所有元素。最后，我们要做的就是返回最终结果。

### 实现

```
 def iter_occurrence(l: List[Int], i: Int): Int = {
        // parameters copy
        var m = l
        var j = i
        var acc = 0

        // base case condition 
        while (m != Nil) {
            // logic
            if (m.head == j) acc += 1
            // parameters update
            m = m.tail
        }
        // base case return
        acc
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们都很好！我们从递归解到迭代解，增加了一些额外的步骤。在这个例子中，经历这个过程可能看起来有点多，但是不管你的递归算法是什么，它都将工作，并且不需要“感觉”解决你的问题的迭代方式。

感谢阅读，希望这能有所帮助！