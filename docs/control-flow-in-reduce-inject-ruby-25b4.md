# reduce/inject 中的控制流(ruby)

> 原文：<https://dev.to/xpbytes/control-flow-in-reduce-inject-ruby-25b4>

[`reduce`](https://ruby-doc.org/core/Enumerable.html#method-i-reduce) ( [`inject`](https://ruby-doc.org/core/Enumerable.html#method-i-inject) )是存在于[可枚举](https://ruby-doc.org/core/Enumerable.html)模块上的最强大的
方法之一，这意味着
方法可用于包含该模块的任何类的任何实例，
包括[数组](https://ruby-doc.org/core/Array.html)，[散列](https://ruby-doc.org/core/Hash.html)，[集合](https://ruby-doc.org/stdlib/libdoc/set/rdoc/Set.html)和
[范围](https://ruby-doc.org/core/Range.html)。

[`reduce`](https://ruby-doc.org/core/Enumerable.html#method-i-reduce) 可以用在 [MapReduce](https://en.wikipedia.org/wiki/MapReduce) 过程中，
通常是理解的基础，是对值进行分组的好方法，或者
计算单个值(*将一组值减少为单个值*)给定一组
值。

这篇文章快速地向您展示了如何在一次 [`reduce`](https://ruby-doc.org/core/Enumerable.html#method-i-reduce) 迭代中跳过值/有条件地返回值
，以及如何提前中断/返回一个
不同的值并停止迭代。

[![Bridge In The Mist in Stockholm, Sweden](img/619839551ed119c7d00044480030e1bb.png "Photo by Anders Jildén (https://unsplash.com/@andersjilden) on Unsplash (https://unsplash.com/)")](https://res.cloudinary.com/practicaldev/image/fetch/s--DWl4tjEV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6h5h6rrr3zuvh7sudfni.jpg)

## 重述💬

从文档中，给出一个实例`enum`(一个**可枚举**)调用
`enum.reduce`:

```
# Combines all elements of <i>enum</i> by applying a binary
# operation, specified by a block or a symbol that names a
# method or operator. 
```

使用 [`reduce`](https://ruby-doc.org/core/Enumerable.html#method-i-reduce) 的一个例子是编写一个对集合中的所有元素求和的函数:

```
##
# Sums each item in the enumerable (naive)
#
# @param [Enumerable] enum the enumeration of items to sum
# @return [Numeric] the sum
#
def summation(enum)
  sum = 0
  enum.each do |item|
    sum += item
  end
  sum
end

##
# Sums each item in the enumerable (reduce block)
#
# Each iteration the result of the block is the passed in previous_result.
#
# @param [Enumerable] enum the enumeration of items to sum
# @return [Numeric] the sum
#
def summation(enum)
  enum.reduce do |previous_result, item|
    previous_result + item
  end
end

##
# Sums each item in the enumerable (reduce method)
#
# Each iteration the :+ symbol is sent as a message to the current result with
# the next value as argument. The result is the new current result.
#
# @param [Enumerable] enum the enumeration of items to sum
# @return [Numeric] the sum
#
def summation(enum)
  enum.reduce(:+)
end

##
# Alias for enum.sum
#
def summation(enum)
  enum.sum
end 
```

[`reduce`](https://ruby-doc.org/core/Enumerable.html#method-i-reduce) 取一个可选的初始值，用来代替
集合的第一项，当给定时。

## 如何控制流量？

当与 [`reduce`](https://ruby-doc.org/core/Enumerable.html#method-i-reduce) 一起工作时，你可能会发现自己处于两种
情况之一:

*   您希望有条件地为迭代返回一个不同的值(用作下一次迭代的基础值)
*   您希望尽早爆发(完全停止迭代)

### 接下来⏭

`next`关键字允许你从`yield`块提前返回，这是任何枚举的
情况。

比方说你对一组数字求和，但要对任意 **数字的**一半**，以及任意**奇数**数字的**一半**:**

```
def halfly_even_doubly_odd(enum)
  enum.reduce(0) do |result, i|
    result + i * (i.even? ? 0.5 : 2)
  end
end 
```

不算太坏。但是现在，另一个业务需求出现了，它要求跳过 5:

```
def halfly_even_doubly_odd(enum)
  enum.reduce(0) do |result, i|
    if i < 5
      result
    else
      result + i * (i.even? ? 0.5 : 2)
    end
  end
end 
```

呃。这不是很好的 ruby 代码。使用`next`它可能看起来像:

```
def halfly_even_doubly_odd(enum)
  enum.reduce(0) do |result, i|
    next result if i < 5
    next result + i * 0.5 if i.even?
    result + i * 2
  end
end 
```

`next`在任何枚举中都有效，所以如果你只是使用
T1 处理项目，你也可以使用它:

```
(1..10).each do |num|
  next if num.odd?
  puts num
end
# 2
# 4
# 6
# 8
# 10
# => 1..10 
```

### 击破🛑

不用跳到下一项，您可以使用`break`完全停止一个
枚举器的迭代。

如果我们有和以前一样的业务需求，但是我们必须返回
号 **42** 如果项目是*正好 7* ，它看起来就像这样:

```
def halfly_even_doubly_odd(enum)
  enum.reduce(0) do |result, i|
    break 42 if i == 7
    next result if i < 5
    next result + i * 0.5 if i.even?
    result + i * 2
  end
end 
```

同样，这适用于任何循环。因此，如果您正在使用 find 来尝试 [`find`](https://ruby-doc.org/core/Enumerable.html#method-i-find)
枚举中的一个项目，并希望*更改那个
`find`的`return`值*，您可以使用`break`来这样做:

```
def find_my_red_item(enum)
  enum.find do |item|
    break item.name if item.color == 'red'
  end
end

find_my_red_item([
  { name: "umbrella", color: "black" },
  { name: "shoe", color: "red" },
  { name: "pen", color: "blue" }
])
# => 'shoe' 
```

### 停止迭代

你可能听说过或者看过 [`raise StopIteration`](https://ruby-doc.org/core/StopIteration.html) 。
这是一个特殊的例外，您可以使用它来停止枚举的迭代，
因为它被 [`Kernel#loop`](https://ruby-doc.org/core/Kernel.html#method-i-loop) 捕获，但是它的用例被限制为
您不应该试图使用`raise`或 [`fail`](https://ruby-doc.org/core/Kernel.html#method-i-fail) 来控制流程。
[airbrake 博客](https://airbrake.io/blog/ruby-exception-handling/stopiteration)有一篇关于这个
用例的[好文章](https://airbrake.io/blog/ruby-exception-handling/stopiteration)。

## 何时使用 reduce

如果你需要一个何时使用 [`reduce`](https://ruby-doc.org/core/Enumerable.html#method-i-reduce) 的指南，不用再找了。我
用这四条规则来决定我是需要用 [`reduce`](https://ruby-doc.org/core/Enumerable.html#method-i-reduce) 还是
[`each_with_object`](https://ruby-doc.org/core/Enumerable.html#method-i-each_with_object) 还是别的什么。

我用 [`reduce`](https://ruby-doc.org/core/Enumerable.html#method-i-reduce) 当:

*   *将*个值的集合减少到一个**更小的**结果(例如 1 个值)
*   *分组*值的集合(如果可能，使用 [`group_by`](https://ruby-doc.org/core/Enumerable.html#method-i-group_by)
*   *改变*不可变原语/值对象(返回新值)
*   你需要一个*新值*(例如新的[数组](https://ruby-doc.org/core/Array.html)或[散列](https://ruby-doc.org/core/Hash.html))

### 替代品🔀

当用例与上面的指导方针不匹配时，大多数时候我
实际上需要 [`each_with_object`](https://ruby-doc.org/core/Enumerable.html#method-i-each_with_object) ，它具有类似的
签名，但是不基于块的`return`值构建新值，
而是使用预定义的“对象”迭代集合，使得在块内使用逻辑更加容易
:

```
doubles = (1..10).each_with_object([]) do |num, result|
  result << num* 2
  # same as result.push(num * 2)
end
# => [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]

doubles_over_ten = (1..10).each_with_object([]) do |num, result|
  result << num * 2 if num > 5
end
# => [12, 14, 16, 18, 20] 
```

使用 [`each_with_object`](https://ruby-doc.org/core/Enumerable.html#method-i-each_with_object) 时:

*   构建新的容器(例如[数组](https://ruby-doc.org/core/Array.html)或[散列](https://ruby-doc.org/core/Hash.html))。**注意**你不是真的*减少*当前集合到一个*更小的*结果，而是有条件或无条件的*映射*值。
*   您希望块中的逻辑不重复结果值(因为在使用 [`reduce`](https://ruby-doc.org/core/Enumerable.html#method-i-reduce) 时，您的*必须*提供一个返回值)

## 我的用例

我使用`reduce`研究控制流的原因是因为我正在通过表示迁移路径的值对象列表迭代
。在不使用
[`lazy`](https://ruby-doc.org/core/Enumerator/Lazy.html) 的情况下，我想要一种优雅的方式来表示这些
迁移应该何时运行，所以使用了语义版本控制。可枚举的迁移是一个附有语义版本的迁移排序列表。

```
migrations.reduce(input) do |migrated, (version, migration)|
  migrated = migration.call(migrated)
  next migrated unless current_version.in_range?(version)
  break migrated
end 
```

函数`in_range?`基于
当前“输入”版本和迁移的语义版本来确定是否执行迁移。这将
执行迁移，直到“当前”版本在范围内，此时
将执行最终迁移并停止。

替代方案不太有利:

*   [`take_while`](https://ruby-doc.org/core/Enumerable.html#method-i-take_while) 、 [`select`](https://ruby-doc.org/core/Enumerable.html#method-i-select) 和好友能够*过滤*列表，但是需要多次迭代迁移集合(过滤，然后“执行”)；
*   [`find`](https://ruby-doc.org/core/Enumerable.html#method-i-find) 将是一个很好的选择，但是我需要更改输入，这将要求我有一个簿记变量来跟踪“迁移”。在 Ruby 中，簿记变量几乎是不必要的。

[![Photo called "It’s Own Kind of Tranquility", displaying a series of windmills on either side of a 'water street (canal)' in Alblasserdam, The Netherlands](img/16e5c8d87b0b4eb0d0d19f6b98451244.png "Photo by Vishwas Katti (https://unsplash.com/@vishkatti) on Unsplash (https://unsplash.com/)")](https://res.cloudinary.com/practicaldev/image/fetch/s--h_3a0nUn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rdbjr94rx7rp3ftw3rl0.jpg)

## 引用

*   [数组](https://ruby-doc.org/core/Array.html)
*   [可枚举](https://ruby-doc.org/core/Enumerable.html)
*   [可枚举# `each_with_object`](https://ruby-doc.org/core/Enumerable.html#method-i-each_with_object)
*   [可枚举# `find`](https://ruby-doc.org/core/Enumerable.html#method-i-find)
*   [可枚举# `group_by`](https://ruby-doc.org/core/Enumerable.html#method-i-group_by)
*   [可枚举# `inject`](https://ruby-doc.org/core/Enumerable.html#method-i-inject)
*   [可枚举# `lazy`](https://ruby-doc.org/core/Enumerator/Lazy.html)
*   [可枚举# `reduce`](https://ruby-doc.org/core/Enumerable.html#method-i-reduce)
*   [可枚举# `select`](https://ruby-doc.org/core/Enumerable.html#method-i-select)
*   [可枚举# `take_while`](https://ruby-doc.org/core/Enumerable.html#method-i-take_while)
*   [哈希](https://ruby-doc.org/core/Hash.html)
*   [内核# `fail`](https://ruby-doc.org/core/Kernel.html#method-i-fail)
*   [内核# `loop`](https://ruby-doc.org/core/Kernel.html#method-i-loop)
*   [范围](https://ruby-doc.org/core/Range.html)
*   [设置](https://ruby-doc.org/stdlib/libdoc/set/rdoc/Set.html)
*   [停止迭代](https://ruby-doc.org/core/StopIteration.html)**