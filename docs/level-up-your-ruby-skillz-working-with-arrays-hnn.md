# 提高您的 Ruby Skillz:使用数组

> 原文：<https://dev.to/molly/level-up-your-ruby-skillz-working-with-arrays-hnn>

当我刚开始工作时，有一位高级工程师和我一起工作，他是处理阵列的奇才。他教会了我在处理数组时编写简洁明了的代码的所有最佳技巧。以下是我认为最有用的方法，我认为从一开始就应该放在 Ruby 工具箱中。

*   [T2`each`](#each)
*   [T2`map`](#map)
*   [T2`flat_map`](#flat_map)
*   [T2`select`](#select)
*   [T2`detect`](#detect)
*   [T2`reject`](#reject)
*   [T2`count`](#count)
*   [T2`partition`](#partition)
*   [T2`with_index`](#with_index)
*   [T2`chaining`](#chaining)

如果你想直接跳到没有解释的代码，检查底部的[备忘单](#cheatsheet)！

# [`each`T4】](https://ruby-doc.org/core-2.6.0.preview2/Array.html#method-i-each)

在我们深入上面的一些更好的方法之前，我们首先需要从最基本的开始，`each`。 [`each`](https://ruby-doc.org/core-2.6.0.preview2/Array.html#method-i-each) 会对数组中的每个给定元素调用一次 block。当它完成时，它将**返回原来的数组。最后一部分很关键，也很容易忘记。即使是我们这些和 Ruby 一起工作了一段时间的人，有时也会忘记这一点。这里有一个例子。** 

```
result = [1, 2, 3].each do |number|
  puts 'hi'
end 
```

Enter fullscreen mode Exit fullscreen mode

该代码在控制台中运行时将产生以下结果。注意:在下面的例子中，irb 仅仅意味着我在一个 Ruby 控制台中。

```
irb:> result = [1, 2, 3].each do |number|
>   puts "hi #{number}"
> end
hi 1
hi 2
hi 3
=> [1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

对于数组中的每个数字，我们打印“hi”加上那个数字。然后，在我们遍历完整个数组后，我们的原始数组被返回。

请记住，我使用的是上面的`do/end`块符号，但是您也可以为您的块使用括号语法，如下所示。

```
irb:> result = [1, 2, 3].each{|number| puts "hi #{number}"}
hi 1
hi 2
hi 3
=> [1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，不管语法如何，结果都是一样的。我将在本指南中继续使用`do/end`语法，因为我认为它使代码和逻辑更容易理解。也就是说，所有这些方法都可以使用括号语法。

# [`map`T4】](https://ruby-doc.org/core-2.6.3/Array.html#method-i-map)

在早期，当我刚接触 Ruby 时，每次我想构建一个数组时，我都会这样做:

```
result = []
[1, 2, 3, 4].each do |number|
  result << number + 2
end
# result = [3, 4, 5, 6] 
```

Enter fullscreen mode Exit fullscreen mode

我很快了解到有一个更好的方法，那就是使用`map`。 [`map`](https://ruby-doc.org/core-2.6.3/Array.html#method-i-map) 返回一个新数组，其结果是对原始数组中的每个元素执行一次该块。这里有一个例子:

```
result = [1, 2, 3, 4].map do |number|
  number + 2
end
# result = [3, 4, 5, 6] 
```

Enter fullscreen mode Exit fullscreen mode

我们将原始数组中的每个数字加 2，然后将这些结果组合成一个新数组，该数组将被返回。现在，我们的代码更简洁了。

# [`flat_map`T4】](https://ruby-doc.org/core-2.6.3/Enumerable.html#method-i-flat_map)

`map`非常适合收集一组结果，但是当您想要映射嵌套数组时会发生什么呢？这时候`flat_map`就派上用场了。如果你发现自己有一组嵌套数组，那么你可能想检查一下`flat_map`。例如，假设您有这样的代码，包含几个嵌套数组。

```
result = []
[1, 2, 3].each do |number|
  ['a', 'b', 'c'].each do |letter|
    result << "#{number}:#{letter}"
  end
end
# result = ["1:a", "1:b", "1:c", "2:a", "2:b", "2:c", "3:a", "3:b", "3:c"] 
```

Enter fullscreen mode Exit fullscreen mode

我们得到了一个单级数组，这正是我们想要的，但是我们怎样才能把它收紧呢？我们试试用`map`。

```
result = [1, 2, 3].map do |number|
  ['a', 'b', 'c'].map do |letter|
    "#{number}:#{letter}"
  end
end
# result = [["1:a", "1:b", "1:c"], ["2:a", "2:b", "2:c"], ["3:a", "3:b", "3:c"]] 
```

Enter fullscreen mode Exit fullscreen mode

嗯，那不完全是我们想要的。我们想要一个扁平的单层数组，而`map`正在创建一个嵌套数组。为了展平嵌套数组，我们可以使用`flat_map`。

```
result = [1,2,3].flat_map do |number|
  ['a', 'b', 'c'].map do |letter|
    "#{number}:#{letter}"
  end
end
# result = ["1:a", "1:b", "1:c", "2:a", "2:b", "2:c", "3:a", "3:b", "3:c"] 
```

Enter fullscreen mode Exit fullscreen mode

[`flat_map`](https://ruby-doc.org/core-2.6.3/Enumerable.html#method-i-flat_map) 的工作方式与`map`类似，它将你的程序块的结果收集到一个数组中，但作为奖励，它会将它展平。在幕后，`flat_map`将[所有的内部数组连接成一个数组。使用`flat_map`返回我们想要的单级数组。](https://ruby-doc.org/core-2.6.3/Array.html#method-i-concat)

# [`select`T4】](https://ruby-doc.org/core-2.6.3/Array.html#method-i-select)

类似于`map`的例子，当我开始时，如果我想有条件地从一个数组中选择元素，例如，选择所有的偶数，我会这样做:

```
result = []
[1, 2, 3, 4].each do |number|
  result << number if number.even?
end
# result = [2, 4] 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但是还有一种更简洁的方法，那就是使用`select`。 [`select`](https://ruby-doc.org/core-2.6.3/Array.html#method-i-select) 返回一个数组，该数组包含给定块返回真值的所有元素。这意味着我们可以像这样重写上面的代码块！

```
result = [1, 2, 3, 4].select do |number|
  number.even?
end
# result = [2,4] 
```

Enter fullscreen mode Exit fullscreen mode

# [`detect`T4】](https://ruby-doc.org/core-2.6.3/Enumerable.html#method-i-detect)

现在我们要把它提升一个档次。如果不希望从数组中返回所有偶数，而只希望找到第一个偶数，会怎么样？为此你可以使用`detect`。 [`detect`](https://ruby-doc.org/core-2.6.3/Enumerable.html#method-i-detect) 将返回块评估为真的第一个条目。因此，如果我们运行与上面类似的代码块，但是用`detect`替换`select`，您可以看到我们只得到第一个偶数。

```
result = [1, 2, 3, 4].detect do |number|
  number.even?
end
# result = 2 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意的一点是，我们现在返回的是一个数字(我们的条目)而不是一个数组。

但是如果我们的块从来没有评估为真会发生什么？如果数组中没有偶数呢？在这种情况下，`detect`将返回 nil。

```
result = [1, 3, 5, 7].detect do |number|
  number.even?
end
# result = nil 
```

Enter fullscreen mode Exit fullscreen mode

总而言之，`detect`将返回块中第一个求值为真的条目，或者如果块中没有条目求值为真，它将返回零。

# [`reject`T4】](https://ruby-doc.org/core-2.6.3/Array.html#method-i-reject)

现在我们来看看`select`的逆，也就是`reject`。 [`reject`](https://ruby-doc.org/core-2.6.3/Array.html#method-i-reject) 将返回您的块评估为假的所有条目。所以不这样做:

```
result = []
[1, 2, 3, 4].each do |number|
  result << number if !number.even?
end
# result = [1, 3] 
```

Enter fullscreen mode Exit fullscreen mode

我们可以简化上面的代码，改为这样做:

```
result = [1, 2, 3, 4].reject do |number|
  number.even?
end
# result = [1, 3] 
```

Enter fullscreen mode Exit fullscreen mode

这一次我们将返回每个不是偶数的数字，因此那些在`number.even?`处的数字将返回 false。

# [`partition`T4】](https://ruby-doc.org/core-2.6.3/Enumerable.html#method-i-partition)

我们刚刚看到了在 Ruby 中使用`select`和`reject`过滤数组的两种方法。但是如果你想把你的数组分成两个数组，一个是偶数数组，一个是奇数数组，那该怎么办呢？实现这一点的一个方法是做:

```
even = [1, 2, 3, 4].select do |number|
  number.even?
end
# even = [2, 4]
odd = [1, 2, 3, 4].reject do |number|
  number.even?
end
# odd = [1, 3] 
```

Enter fullscreen mode Exit fullscreen mode

但是，有一个更好的方法，你可以使用`partition`！为了这一个抓紧你的座位。 [`partition`](https://ruby-doc.org/core-2.6.3/Enumerable.html#method-i-partition) 将返回两个数组，第一个包含块评估为真的原始数组的元素，第二个包含其余的元素。这意味着我们可以把上面写的东西简化成:

```
result = [1, 2, 3, 4].partition do |number|
  number.even?
end
# result = [[2, 4], [1, 3]] 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，`partition`将返回两个数组，一个是偶数，一个是奇数。如果我们想给我们的`even`和`odd`变量赋值，我们所要做的就是

```
even = result.first
odd = result.last 
```

Enter fullscreen mode Exit fullscreen mode

然而，正如你可能猜到的，有一个更好的方法！我们可以完全去掉单个的`result`变量，写成这样的东西

```
even, odd = [1, 2, 3, 4].partition do |number|
  number.even?
end
# even = [2, 4] and odd = [1, 3] 
```

Enter fullscreen mode Exit fullscreen mode

这个语法会自动将第一个数组分配给`even`，将第二个数组分配给`odd`。在处理嵌套数组时，可以随时使用这种数组赋值语法。这是一个如何分解 3 个数组的例子。

```
irb:> a, b, c = [[1], [2], [3]]
=> [[1], [2], [3]]
irb:> a
=> [1]
irb:> b
=> [2]
irb:> c
=> [3] 
```

Enter fullscreen mode Exit fullscreen mode

# [`count`T4】](https://ruby-doc.org/core-2.6.3/Array.html#method-i-count)

默认情况下，它会计算数组中元素的数量。

```
irb:> [1, 1, 2, 2, 3, 3].count
=> 6 
```

Enter fullscreen mode Exit fullscreen mode

但是，您知道它还能做更多的事情吗？对于初学者来说，可以通过`count`进行论证。如果你传递 [`count`](https://ruby-doc.org/core-2.6.3/Array.html#method-i-count) 一个参数，它会计算这个参数在你的数组中出现的次数。

```
irb:> [1, 1, 2, 2, 3, 3].count(1)
=> 2
irb:> ['a', 'a', 'b', 'c'].count('c')
=> 1 
```

Enter fullscreen mode Exit fullscreen mode

还可以通过 [`count`](https://ruby-doc.org/core-2.6.3/Array.html#method-i-count) 一挡！😲当传递给 block 时，`count`将返回 block 评估为真的条目数。

```
irb:> [1, 1, 2, 2, 3, 3].count do |number|
  number.odd?
end
=> 4 
```

Enter fullscreen mode Exit fullscreen mode

我们计算了数组中的每个奇数，返回的结果是 4。

# [`with_index`T4】](https://ruby-doc.org/core-2.6.0.rc1/Enumerator.html#method-i-with_index)

最后但同样重要的是，我想谈谈用索引遍历数组。通常，当我们想知道我们在一个元素数组中的位置时，我们会这样做。

```
irb:> index = 0
irb:> ['a', 'b', 'c'].each do |letter|
  puts index
  index += 1
end
0
1
2 
```

Enter fullscreen mode Exit fullscreen mode

但是，有更好的办法！你可以使用 [`with_index`](https://ruby-doc.org/core-2.6.0.rc1/Enumerator.html#method-i-with_index) 和`each`，或者我上面列出的任何方法，来帮助你跟踪你在数组中的位置。这里有一些你如何使用它的例子。(记住:数组索引从 0 开始😃)

```
irb:> ['a', 'b', 'c'].each.with_index do |letter, index|
  puts index
end
0
1
2 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们只是简单地迭代数组，并打印出每个元素的索引。

```
result = ['a', 'b', 'c'].map.with_index do |letter, index|
  "#{letter}:#{index}"
end
# result = ["a:0", "b:1", "c:2"] 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们使用`map`方法将索引和数组中的字母组合起来，形成一个新的数组。

```
result = ['a', 'b', 'c'].select.with_index do |letter, index|
  index == 2
end
# result = ["c"] 
```

Enter fullscreen mode Exit fullscreen mode

这个例子有点复杂。这里我们使用索引来帮助我们选择数组中索引等于 2 的元素。在这种情况下，元素是“c”。

# `chaining`

我想留给你们的最后一点知识是，上面任何返回一个数组的方法(除了`count`和`detect`)都可以链接在一起。对于这些例子，我将使用括号符号，因为我认为从左向右比从上向下更容易阅读链接方法。

例如，您可以这样做:

```
result = [1, 2, 3, 4].map{|n| n + 2}.select{|n| n.even?}.reject{|n| n == 6}.map{|n| "hi #{n}"} 
# result = ["hi 4"] 
```

Enter fullscreen mode Exit fullscreen mode

根据我们在上面学到的知识，让我们来分析一下这里正在发生的事情。
1) `map`将为我们的每个数组元素加 2 并返回`[3, 4, 5, 6]`
2) `select`将只从数组中选择偶数并返回`[4, 6]`
3) `reject`将删除任何等于 6 的数字，剩下的是`[4]`
4)我们最终的`map`将在 4 前面加上“hi”并返回`["hi 4"]`

# 你做到了！！！！

[![](img/e146c406bec34266db04db4b207b313b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4QjOOMLx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0hw597r707r9a3koalyf.gif) 
恭喜你，你一路走到了终点！希望您在编写 Ruby 代码时会发现这些数组方法很有用。如果有任何不清楚的地方，请在评论中告诉我。这是我第一次写教程，所以我欢迎任何反馈🤗

如果你想要所有这些没有冗长解释的代码示例，请查看[这份由](https://paper.dropbox.com/doc/Ruby-Array-Methods--AcuxIuoFBAtO4Q1CxBDuNrVsAg-cRF8GIgVFnC3zhfINk7Wy) [@lukewduncan](https://dev.to/lukewduncan) 大方整理的备忘单！