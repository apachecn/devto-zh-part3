# Ruby 2.7 —可枚举的#tally

> 原文：<https://dev.to/baweaver/ruby-2-7-enumerable-tally-3b02>

### Ruby 2.7 —可枚举#tally

圣诞节来了又走了，2.6 已经发布了，现在是时候无情地兜售 2.7 特性的发布页面了，这样我们就可以开始我们有趣的关于即将到来的特性的小年度博客传统了。

通常这意味着另一个 12 月的发布，但是也有方法提前发布的情况，如果它们在今年早些时候合并到主干中。

[![](img/43dc1da36f547443a1eccc23257e5c95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GVp_c7ty--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ap0Ad_zpGIOEFOTX4Yft1Fw.png)

这一轮？我们有了可枚举的新方法#tally！

### 短版

`tally`统计事物:

```
[1, 1, 2].tally
# => { 1 => 2, 2 => 1 }

[1, 1, 2].map(&:even?).tally
# => { false => 2, true => 1 } 
```

Enter fullscreen mode Exit fullscreen mode

### 例子

Ruby 官方测试代码中使用的例子是:

```
[1, 2, 2, 3].tally
# => { 1 => 1, 2 => 2, 3 => 1 } 
```

Enter fullscreen mode Exit fullscreen mode

如果没有块，tally 通过计算每个元素在一个`Enumerable`类型中的出现次数来工作。如果我们把它应用于另一种类型的列表，可能会更清楚一点:

```
%w(foo foo bar foo baz foo).tally
=> {"foo" => 4, "bar" => 1, "baz" => 1} 
```

Enter fullscreen mode Exit fullscreen mode

目前`tally_by`还没有被核心接受，所以要通过函数调用`tally`，你可以先使用`map`:

```
%w(foo foo bar foo baz foo).map { |s| s[0] }.tally
=> {“f” => 4, “b” => 2} 
```

Enter fullscreen mode Exit fullscreen mode

[目前正在讨论](https://bugs.ruby-lang.org/issues/11076#note-22)是否接受这个特性，这会产生上面的语法:

```
%w(foo foo bar foo baz foo).tally\_by { |s| s[0] }
=> {“f” => 4, “b” => 2} 
```

Enter fullscreen mode Exit fullscreen mode

### 为什么要用？

如果你一直在使用 Ruby，很可能你已经使用了类似下面这样的代码来做上面`tally`所做的事情

```
list.group_by { |v| v.something }.transform_values(&:size)

list.group_by { |v| v.something }.map { |k, vs| [k, vs.size] }.to_h

list.group_by { |v| v.something }.to_h { |k, vs| [k, vs.size] }

list.each_with_object(Hash.new(0)) { |v, h| h[v.something] += 1 } 
```

Enter fullscreen mode Exit fullscreen mode

很可能还有其他几种变体，但这些都是你可能在周围看到的一些更常见的变体。这是在 Ruby 语言中缩写一个非常常见的习语的一个很好的方法，也是一个非常受欢迎的方法。

### 香草红宝石等价物

这个方法是做什么的？如果我们用普通的 Ruby 实现它，看起来可能有点像这样:

```
module Enumerable
  def tally_by(&function)
    function ||= -> v { v }

    each_with_object(Hash.new(0)) do |value, hash|
      hash[function.call(value)] += 1
    end
  end

  def tally
    tally_by(&:itself)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在没有提供函数的情况下，它将有效地自己计数，或者说是一个身份函数。

> 标识函数是返回给定内容的函数。如果你给它 1，它返回 1。如果给它 true，它返回 true。Ruby 也在一个名为自身的方法中使用了这个概念。

本文不会深入讨论上面的代码是做什么的。“减少可枚举数”的第五部分更详细地介绍了这段代码:

[减少计数—第五部分:天蓝色，计数大师由](https://medium.com/@baweaver/reducing-enumerable-part-five-cerulean-master-of-tally-by-9d5f9e430bc2)

### 源代码

Nobu 最近提交了一个 Ruby core 的补丁来添加这个方法:

[enum.c:可枚举#tally ruby/ruby@673dc51](https://github.com/ruby/ruby/commit/673dc51c251588be3c9f4b5b5486cd80d46dfeee)

Ruby 核心团队接受了这个名字`tally`:

[功能#11076:可枚举方法`count_by` - Ruby trunk - Ruby 问题跟踪系统](https://bugs.ruby-lang.org/issues/11076#change-75622)

### 理货？

让我们从这个词的意思开始:

> 一个**计数**是一个数量或数字的记录，随着影响它的活动的进展，你不断地改变和增加。
> 
> *   [https://www . Collins dictionary . com/us/dictionary/English/tally](https://www.collinsdictionary.com/us/dictionary/english/tally)

这个名字是从哪里来的？最初提出了名称`count_by`,但是该名称被拒绝，因为它不同于 count，count 具有不同的返回类型和行为。

在从太浩湖地区和 [RailsCamp West](https://west.railscamp.us/) 返回的路上，我们(我自己、[大卫](https://twitter.com/d_jones)、[斯蒂芬妮](https://twitter.com/stephanieblack)和[香农](https://twitter.com/_havenn))正在讨论潜在的替代名称，尝试并提议看看该功能是否可以以不同的名称出现。

[大卫](https://twitter.com/d_jones)曾提出`tally`，并正式建议。看起来名字被保留了，代码被合并到了主干中。

现在，我已经在一些会议上做了演讲，并决定在我的 RubyConf 演讲的一个部分中提到`tally_by`而不是`count_by`。书面版本在这里:

[减少计数—第五部分:天蓝色，计数大师由](https://medium.com/@baweaver/reducing-enumerable-part-five-cerulean-master-of-tally-by-9d5f9e430bc2)

只是一点有趣的背景故事。

### 包装完毕

2.7 正在路上，让我们看看它会带来什么！我很期待看到 Ruby 从这里走向何方。