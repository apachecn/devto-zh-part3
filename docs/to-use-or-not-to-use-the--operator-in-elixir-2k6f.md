# 在 Elixir 中使用或不使用++运算符

> 原文：<https://dev.to/jmbejar/to-use-or-not-to-use-the--operator-in-elixir-2k6f>

*原载于 WyeWorks 博客:[https://www . WyeWorks . com/blog/2019/03/01/to-use-or-not-to-use-the-++-operator-in-elixir/](https://www.wyeworks.com/blog/2019/03/01/to-use-or-not-to-use-the-++-operator-in-elixir/)*

你可能已经被警告过在 *Elixir* 中使用`++`操作符连接列表的风险。事实上，一个很好的建议可以直接在这个方法的[官方文档中找到:](https://hexdocs.pm/elixir/Kernel.html#++/2)

> `a ++ b`的复杂度和`length(a)`成正比，所以要避免重复追加到任意长度的列表，比如`list ++ [item]`。而是考虑通过`[item | rest]`前置，然后反转。

在本文的其余部分，我们将深入一个例子，试图解释使用`++`可能会有什么问题，同时也检查我们是否可以按照文档中的建议通过重构来改进。剧透:生活没那么容易😅。

## 问题

假设我们需要实现一个函数，该函数接收一个整数列表并返回一个新列表，该列表包含每个数字的 n 倍，其中 T2 等于该整数的实际值。我们还希望在结果列表中保留这些数字的原始顺序。看看下面的例子:

```
my_func([1]) # it returns [1]
my_func([1,2]) # it returns [1,2,2]
my_func([1,3,2]) # it returns [1,3,3,3,2,2] 
```

## 我们第一次天真的实现

这个问题的一个非常简单的解决方案是使用`Enum.reduce/3`复制每个数字，并将复制的值连接到累积列表。

```
def number_dups_list(numbers) do
  Enum.reduce(numbers, [], fn n, acc ->
    acc ++ List.duplicate(n, n)
  end)
end 
```

让我们通过用一个大的整数列表运行这个函数来看看它是如何执行的。下面是我们的第一个测试(使用`:timer.tc`快速测量运行代码所用的时间):

```
iex> :timer.tc(fn -> number_dups_list(List.duplicate(1,100_000)) end)
{30785140, .... } 
```

在运行了几次以获取一些样本之后，我注意到这需要大约 3 秒钟(在我的机器上)。那相当慢；可能还有改进的余地。

## 让我们遵循文档中的建议

如果我们仔细观察我们之前测试的执行，其中输入是一个包含许多`1`的列表，我们看到我们的实现重复使用了`++`。事实上，这里的关键因素是操作符左边的列表随着每次迭代变得越来越大。同时，右边的列表总是`[1]`。正如文档所述，整体性能取决于该操作符左侧列表的长度。

让我们看看如果颠倒顺序会发生什么(然后使用`Enum.reverse`重新排序)。

```
def number_dups_list(numbers) do
  Enum.reduce(numbers, [], fn n, acc ->
    List.duplicate(n, n) ++ acc
  end)
  |> Enum.reverse
end 
```

让我们再运行一次，看看我们是否增强了性能:

```
iex> :timer.tc(fn -> number_dups_list(List.duplicate(1,100_000)) end)
{11570, .... } 
```

在我的机器上，这将在大约 11 毫秒后执行**！这是一个巨大的差异！列表越大，与前一种方法的差异就越大。**

目前为止一切正常，但是...我们能肯定在所有情况下都会有更好的表现吗？很有可能，我们还没有脱离险境。让我们使用不同的输入再次运行我们的函数。

```
iex()> :timer.tc(fn -> number_dups_list([10_000_000, 20_000_000]) end)
{2638101, .... } 
```

你可能已经注意到这个问题了！我们现在使用的是一个相当短的列表，但是数字是非常大的数字。因此，我们又一次处于这样的位置，即`++`操作符左边的列表总是一个大列表，导致函数的执行速度变慢(大约 2 秒)。

有趣的是，对于这个新列表，我们以前的实现工作得更好一些(不到 1.5 秒)。只有在执行的第一阶段，`++`操作符左边的列表是一个短列表(一个空列表)。这些结果表明，在这里反转是有区别的；对于这种类型的输入，前面的实现(不需要反转)是更可取的。

在这一点上，我们希望开始以一种更稳健的方式来衡量性能。我们将使用 [Benchee](https://github.com/PragTob/benchee) 为我们处理所有的基准测试工作。参见下面的基准测试(假设这个函数包含在同一个模块中，注意我们现在对每个实现使用不同的函数名)。

```
def run_benchmarks do
  inputs = %{
    "long list with low values" => List.duplicate(1,100_000),
    "short list with high values" => [10_000_000, 20_000_000]
  }

  Benchee.run %{
    "Using ++" =>
      fn(list) -> number_dups_list1(list) end,
    "Using ++ and reverse"  =>
      fn(list) -> number_dups_list2(list) end,
  }, time: 20, warmup: 5, inputs: inputs
end 
```

在我的计算机上运行这段代码会得到以下输出:

```
##### With input long list with low values #####
Name                           ips        average  deviation         median         99th %
Using ++ and reverse        123.53      0.00810 s    ±18.33%      0.00824 s       0.0127 s
Using ++                    0.0366        27.35 s     ±0.00%        27.35 s        27.35 s

Comparison:
Using ++ and reverse        123.53
Using ++                    0.0366 - 3378.87x slower

##### With input short list with high values #####
Name                           ips        average  deviation         median         99th %
Using ++                      0.93         1.07 s    ±41.40%         1.00 s         2.38 s
Using ++ and reverse          0.50         2.01 s    ±29.48%         2.01 s         3.48 s

Comparison:
Using ++                      0.93
Using ++ and reverse          0.50 - 1.88x slower 
```

这证实了我们之前所看到的，特别是我们的第一个实现使用一个简短的大数列表会更快。正如我们之前提到的，在长列表的情况下，不同之处在于调用`Enum.reverse`(您可以通过注释掉这一部分并检查基准测试结果现在是否非常相似来容易地验证这一点，即使函数不会产生完全相同的结果)。

## 左边的列表有什么问题？

对这个问题的进一步解释需要一篇自己的博客文章😀。

这么说吧，在计算操作结果时，Erlang VM 被迫复制一份`++`左侧列表的完整副本，而这对于右侧列表来说并不是必需的。后一个列表分配的内存可以被结果列表重用，但是左边的原始列表需要被复制到内存中。因此，左侧的列表越大，整个追加操作的成本就越高。

你可以在这里找到更多信息[。](http://erlang.org/doc/efficiency_guide/listHandling.html)

## 尝试不使用`++`实现

官方文档建议我们利用`++`来重构代码，以包含`[item|rest]`。我们为什么不试一试呢？这里没有明确说明的一点是，我们可能需要使用`Enum.reduce/3`来迭代`++`操作符左边的列表元素，以便获得相同的结果。

```
def number_dups_list3(numbers) do
  Enum.reduce(numbers, [], fn n, acc ->
    Enum.reduce(List.duplicate(n, n), acc, fn n2, acc2 ->
      [ n2 | acc2 ]
    end)
  end)
end 
```

是时候运行这个新的实现了，看看会发生什么...

```
iex> fn -> number_dups_list3(List.duplicate(1,100_000)) end |> :timer.tc
{16279, .... }

iex> fn -> number_dups_list3([10_000_000, 20_000_000]) end |> :timer.tc
{2515518, .... } 
```

我们发现，在第一种情况下，当列表中的值很小时，代码工作得很好。然而，当我们的第二个实现依赖于`++`(通过添加短列表来增加结果列表的那个)时，似乎没有太大的区别。

此外，在输入列表`[10_000_000, 20_000_000]`的情况下，事情执行得相当慢。让我们在 Benchee 的帮助下运行 3 个函数后看看完整的报告:

```
##### With input long list with low values #####
Name                                       ips        average  deviation         median         99th %
Using ++ and reverse                    119.85        8.34 ms    ±24.50%        8.22 ms       15.47 ms
Without ++ ([h | t] and reverse)         70.75       14.13 ms    ±21.33%       13.07 ms       26.79 ms
Using ++                                0.0300    33361.65 ms     ±0.00%    33361.65 ms    33361.65 ms

Comparison:
Using ++ and reverse                    119.85
Without ++ ([h | t] and reverse)         70.75 - 1.69x slower
Using ++                                0.0300 - 3998.44x slower

##### With input short list with high values #####
Name                                       ips        average  deviation         median         99th %
Using ++                                  0.92         1.08 s    ±45.19%         1.01 s         2.51 s
Using ++ and reverse                      0.41         2.45 s    ±27.75%         2.29 s         3.72 s
Without ++ ([h | t] and reverse)          0.35         2.85 s    ±39.46%         2.39 s         4.82 s

Comparison:
Using ++                                  0.92
Using ++ and reverse                      0.41 - 2.26x slower
Without ++ ([h | t] and reverse)          0.35 - 2.64x slower 
```

在我们的任何测试案例中，我们都无法击败依赖于`++`的以前的实现。因此，在这两种情况下，文档建议的选项都不是最适合我们的。

## 另一种实现思路？

我们可以进一步集思广益，想出其他的主意来解决我们的问题。如果我们不再过多地考虑性能，我们可以尝试寻找其他方法来重新实现代码，最终可能会得到一个更优雅的解决方案。

例如，如果我们将`Enum.map/2`和`Enum.flatten/1`组合起来，我们最终会得到下面的实现:

```
def  number_dups_list4(numbers) do
  Enum.map(numbers, &(List.duplicate(&1, &1)))
  |>  List.flatten
end 
```

这个其实挺简单理解的。我们现在创建一个列表，它包括两个带有重复项的中间列表。最终，在最终列表中，一切都变平了。

它会有怎样的表现？如果我们试图猜测将会发生什么，我会说，在所有迭代中将中间列表保存在内存中的需要和不需要反转结果列表(与其他解决方案一样)的事实之间将会有一场斗争。

运行我们的基准测试后，最终答案揭晓:

```
##### With input long list with low values #####
Name                                       ips        average  deviation         median         99th %
Using ++ and reverse                    120.03        8.33 ms    ±25.82%        8.11 ms       16.30 ms
map / flatten                           113.00        8.85 ms    ±21.77%        8.17 ms       17.72 ms
Without ++ ([h | t] and reverse)         76.24       13.12 ms     ±9.12%       12.93 ms       17.96 ms
Using ++                                0.0324    30821.21 ms     ±0.00%    30821.21 ms    30821.21 ms

Comparison:
Using ++ and reverse                    120.03
map / flatten                           113.00 - 1.06x slower
Without ++ ([h | t] and reverse)         76.24 - 1.57x slower
Using ++                                0.0324 - 3699.55x slower

##### With input short list with high values #####
Name                                       ips        average  deviation         median         99th %
Using ++                                  0.93         1.08 s    ±39.43%         1.03 s         2.38 s
map / flatten                             0.61         1.63 s    ±46.49%         1.53 s         3.90 s
Using ++ and reverse                      0.53         1.89 s    ±34.73%         1.84 s         3.61 s
Without ++ ([h | t] and reverse)          0.37         2.70 s    ±33.23%         2.37 s         4.16 s

Comparison:
Using ++                                  0.93
map / flatten                             0.61 - 1.51x slower
Using ++ and reverse                      0.53 - 1.75x slower
Without ++ ([h | t] and reverse)          0.37 - 2.50x slower 
```

我们可以看到这个解决方案排名相当不错，**在两种情况下都位居第二**。如果我们希望两种类型的输入都有，那么这个实现并不是一个坏的选择(特别是如果我们放弃了第一次尝试，结果很糟糕，累积列表总是在`++`操作符的左边)。

然而，我们需要测试更多的案例来做出更明智的决定。毕竟，我们正在测试两个非常不同的，也许是极端的用例。例如，我们可以用`Enum.to_list(1..1000)`测试我们的实现，或者任何可以被认为是一般情况的东西。我用这个新的输入进行了测试，我发现第二个实现运行得更快，第二个实现使用了`++`和`reverse`(而`map`和`flatten`版本的性能类似于`[item | rest]`版本)。

## 结论

这里有一些关于在 Elixir 中使用`++`操作符的想法，特别是在预期负载很高并且性能影响可能成为问题的情况下。

*   了解使用`++`添加到列表中的风险是很重要的，特别是当操作符左边的列表可能有大量元素时。

*   如果你处于上述情况，你可以考虑使用`Enum.reduce`和`[item | rest]`，尽管你应该验证这些调整是否真的改善了情况。虽然我认为文档中的建议是一个很好的经验法则，但是你不应该盲目地遵循它。事实上，我会尝试使用`++`,但是颠倒附加列表的顺序(并在以后颠倒结果)。此外，您还可以使用不同的语言工具测试不同的实现，正如我们在尝试`Enum.map`和`Enum.flatten`时所做的那样，以便查看它们与之前的尝试相比如何。

*   思考你预期输入的形状和大小。还要考虑在计算的中间步骤中创建的列表的数量及其长度。最佳性能的实现将取决于使用模式。

*   请记住，通常很难预测最终的性能...除非您是 Elixir/Erlang 内部专家😀。对于我们这些凡人来说，在每种情况下运行基准并基于此做出决策是至关重要的。每种情况都是独特的！

最后，你可能想看一下 [Erlang 效率指南](http://erlang.org/doc/efficiency_guide/introduction.html)中关于[列表](http://erlang.org/doc/efficiency_guide/listHandling.html)的章节。它包含了一些关于列表操作符的好见解，包括`++`、`flatten`等。Elixir 主要依赖于那些 Erlang 函数。

并且，因为我知道你很好奇，这里有[本帖中提到的完整代码](https://gist.github.com/jmbejar/3761e0fd2905f7aaa0aedbbfda598624)和基准测试结果。