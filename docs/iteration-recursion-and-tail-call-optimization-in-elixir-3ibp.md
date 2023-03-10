# Elixir 中的迭代、递归和尾调用优化

> 原文：<https://dev.to/appsignal/iteration-recursion-and-tail-call-optimization-in-elixir-3ibp>

Elixir 使用递归来遍历列表等数据结构。虽然大部分都隐藏在`Enum`类的高层抽象中，但在某些情况下，自己编写递归函数会更有性能。在这一集《炼丹术》中，我们将试着找到一些这样的例子。

一路上，我们将学习体递归和尾递归函数。虽然后者经常被吹捧为更快，但我们也将看看不一定如此的情况。让我们开始吧！

## 枚举

Elixir 在`Enum`模块上提供了枚举集合的函数。

```
def sum_numbers1(list) do
  list
  |> Enum.filter(&is_number/1)
  |> Enum.reduce(0, fn n, sum -> n + sum end)
end 
```

此示例获取一个列表，并返回该列表中所有数字的总和。因为输入列表中可能有非数字项，所以它使用`Enum.filter/2`只选择那些`is_number/1`返回 true 的项。之后，剩余的值通过`Enum.reduce/3`相加在一起。

```
sum_numbers1([1, 2, "three", 4, %{}]) # => 7 
```

虽然这个解决方案有效，但是它在整个列表上迭代两次以得到结果。为了提高性能，我们可以选择编写一个递归函数来一次性完成这个任务。

## 递归

递归函数可以在一次运行中完成这两项任务(删除非数字项并将其余项相加)。它将为列表中的每一项调用自己，并使用模式匹配来决定如何处理每一项。

```
# 1
def sum_numbers2([head | tail]) when is_number(head) do
  sum_numbers2(tail) + head
end

# 2
def sum_numbers2([_head | tail]) do
  sum_numbers2(tail)
end

# 3
def sum_numbers2([]) do
  0
end 
```

这个解决方案不使用`Enum`模块提供的函数，而是自己调用，直到列表为空。它使用三个功能头:

1.  当列表中的第一项(头部*)是一个数字时，我们将调用列表其余部分的`sum_numbers2/1`(除了头部之外的任何部分，调用尾部*)。这个调用返回一个数字，我们将添加我们当前的头。**
***   当头部是*而不是*一个数字时，我们将通过仅用尾部调用`sum_numbers2/1`函数来删除它。*   当列表为空时，我们将返回 0。**

```
sum_numbers2([1, 2, "three", 4, %{}]) # => 7 
```

当用`[1, 2, "three", 4, %{}]`调用该函数时，按照以下顺序重复调用`sum_numbers2/1`函数以得到结果:

*   `sum_numbers2([1, 2, "three", 4, %{}])`
*   `sum_numbers2([2, "three", 4, %{}]) + 1`
*   `sum_numbers2(["three", 4, %{}]) + 1 + 2`
*   `sum_numbers2([4, %{}]) + 1 + 2`
*   `sum_numbers2([%{}]) + 1 + 2 + 4`
*   `sum_numbers2([]) + 1 + 2 + 4`
*   `0 + 1 + 2 + 4`

这个列表显示了这个函数是如何被简化的，直到我们得到一个结果，这个结果显示这个函数被调用了六次。每个项目一次，最后空列表再加一次。

因为函数在每次迭代中都调用自己，所以列表中的每一项都会导致一个[堆栈帧](https://en.wikipedia.org/wiki/Call_stack#STACK-FRAME)被添加到调用堆栈中。这是因为每一项都需要调用一个函数，这个函数需要被执行来获得结果。将堆栈帧添加到调用堆栈需要分配一些内存，并且会导致一些开销。

## 尾部递归和尾部调用优化

为了将内存占用保持在最低水平，一些语言——比如 Erlang 和 Elixir——实现了尾调用优化。通过对代码进行少量的重写，我们可以防止添加堆栈帧和分配内存。

```
# 1
def sum_numbers3(list) do
  do_sum_numbers3(list, 0)
end

# 2
defp do_sum_numbers3([head | tail], sum) when is_number(head) do
  do_sum_numbers3(tail, sum + head)
end

# 3
defp do_sum_numbers3([_head | tail], sum) do
  do_sum_numbers3(tail, sum)
end

# 4
defp do_sum_numbers3([], sum) do
  sum
end 
```

这个例子是前面函数的另一个实现。然而，这个例子是尾递归的，这意味着它不需要在继续之前等待对自身的调用。它通过保存一个累加器(`sum`)来保存当前值，而不是堆叠函数调用。

1.  `sum_numbers3/1`函数是公共函数，它用传递的列表和一个`0`的累加器调用私有的`do_sum_numbers3/2`函数。
2.  与前面的例子非常相似，`do_sum_numbers3/2`的第一个函数 head 匹配带有数字头*的列表*。它用列表的*尾*和添加到累加器的*头*来调用自己。
3.  当*头*不是一个数时，第三个函数头通过用*尾*和当前累加器调用自己来丢弃它，不改变任何东西。
4.  最后，exit 子句返回累加器。

通过在函数结束时调用自己，并传递带有所有计算值的累加器，Erlang VM 可以执行一个称为尾调用优化的技巧，这允许它避免添加新的堆栈帧。相反，它通过跳回到函数的开头来使用当前帧。

尾部调用优化允许函数调用自己，而不会遇到堆栈问题。

```
def sleep, do: sleep 
```

此示例实现了一个不断调用自身的函数。如果没有尾部调用优化，这个函数会产生堆栈错误。

## 哪个更快？

我们已经编写了同一个函数的三个实现。第一个使用了`Enum.filter/2`和`Enum.reduce/3`对列表进行过滤和求和，并对列表进行了两次迭代。为了解决这个问题，我们使用了一个递归函数一次性完成，并使用尾调用优化进一步改进了这个函数。

```
letters = Enum.map(?a..?z, fn x -> <<x::utf8>> end)
numbers = Enum.to_list(0..10_000)
list = Enum.shuffle(letters ++ numbers)

Benchee.run(
  %{
    "Enum.filter/2 and Enum.reduce/3" => fn -> Recursion.sum_numbers1(list) end,
    "Body-recursive" => fn -> Recursion.sum_numbers2(list) end,
    "Tail-recursive" => fn -> Recursion.sum_numbers3(list) end
  },
  time: 10,
  memory_time: 2
) 
```

为了对我们的解决方案进行基准测试，我们将使用 [Benchee](https://github.com/PragTob/benchee) 。我们将为每个函数准备一个混排的字符串和数字列表，进行十秒钟的求和。

```
Name                                      ips        average  deviation         median         99th %
Tail-recursive                       107.35 K        9.32 μs    ±21.39%           9 μs          14 μs
Body-recursive                        55.65 K       17.97 μs   ±662.77%          14 μs          34 μs
Enum.filter/2 and Enum.reduce/3       20.86 K       47.94 μs    ±51.46%          46 μs          85 μs

Comparison:
Tail-recursive                       107.35 K
Body-recursive                        55.65 K - 1.93x slower
Enum.filter/2 and Enum.reduce/3       20.86 K - 5.15x slower

Memory usage statistics:

Name                               Memory usage
Tail-recursive                              0 B
Body-recursive                              0 B
Enum.filter/2 and Enum.reduce/3         16064 B 
```

在这台机器上的测试中，我们看到体递归版本的速度几乎是使用`Enum.filter/2`和`Enum.reduce/3`的原始实现的三倍。尾递归版本的速度几乎是体递归版本的两倍。

## 尾调用递归总是更快吗？

虽然基准测试的结果看起来很有说服力，但是尾递归并不总是比体递归快。事实上，这是关于 Erlang 性能的 7 个神话之一。

虽然尾部递归调用通常对于列表缩减更快——就像我们之前看到的例子一样——但是体递归函数在某些情况下可能更快。在映射一个列表时，这种情况经常发生，在这种情况下，函数接受一个列表并返回一个列表，而不会将其简化为单个值。

```
def double_numbers1(list) do
  list
  |> Enum.filter(&is_number/1)
  |> Enum.map(fn n -> n * 2 end)
end

def double_numbers2([]) do
  []
end

def double_numbers2([head | tail]) when is_number(head) do
  [head * 2 | double_numbers2(tail)]
end

def double_numbers2([_head | tail]) do
  double_numbers2(tail)
end

def double_numbers3(list) do
  list
  |> do_double_numbers3([])
  |> Enum.reverse()
end

defp do_double_numbers3([], acc) do
  acc
end

defp do_double_numbers3([head | tail], acc) when is_number(head) do
  do_double_numbers3(tail, [head * 2 | acc])
end

defp do_double_numbers3([_head | tail], acc) do
  do_double_numbers3(tail, acc)
end 
```

这个例子很像我们之前看到的例子，但是不是将所有的数字加在一起并减少到一个数字，`double_numbers/1`函数将所有的数字加倍并在一个列表中返回它们。

像以前一样，我们有三个实现。第一个使用`Enum.filter/2`和`Enum.map/2`在列表上迭代两次，第二个是体递归，最后一个是尾递归。在返回之前，尾递归函数需要反转列表，因为值被预先加到累加器上。

**注意**:查看[示例项目](https://github.com/jeffkreeftmeijer/elixir-recursion-example)，比较更多可能的实现，比如列表理解和不反转列表的尾部递归版本。

```
Name                                   ips        average  deviation         median         99th %
body-recursive                     36.86 K       27.13 μs    ±39.82%          26 μs          47 μs
tail-recursive                     27.46 K       36.42 μs  ±1176.74%          27 μs          80 μs
Enum.filter/2 and Enum.map/2       12.62 K       79.25 μs   ±194.81%          62 μs         186 μs

Comparison:
body-recursive                     36.86 K
tail-recursive                     27.46 K - 1.34x slower
Enum.filter/2 and Enum.map/2       12.62 K - 2.92x slower

Memory usage statistics:

Name                            Memory usage
body-recursive                      15.64 KB
tail-recursive                      20.09 KB - 1.28x memory usage
Enum.filter/2 and Enum.map/2        31.33 KB - 2.00x memory usage 
```

当对每个实现运行基准测试时，我们可以看到体递归版本在这种情况下是最快的。在尾递归版本中，必须反转列表，这抵消了尾调用优化在这种特定情况下增加的改进性能。

## 一如既往，视情况而定

这就结束了我们对 Elixir 中递归的研究。作为经验法则；如果在返回结果之前不需要反转结果，尾递归函数会更快。这是因为这需要对整个列表进行另一次迭代。尾部递归函数通常在减少列表方面更快，就像我们的第一个例子。

哪种方法最好取决于具体情况。对于任务关键型循环，编写一个基准来衡量哪个解决方案更快通常是您的最佳选择，因为哪一个会执行得更好并不明显。

要了解更多关于递归的知识，也一定要阅读可用方法的概述以及何时使用这些方法。如果你喜欢这篇文章，不要忘记[订阅邮件列表](https://blog.appsignal.com/elixir-alchemy)，如果你想阅读更多的炼丹术！**