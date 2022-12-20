# Ruby 2.7 —模式匹配—第一印象

> 原文：<https://dev.to/baweaver/ruby-2-7-pattern-matching-first-impressions-13j9>

### Ruby 2.7 —模式匹配—第一印象

我们能先睹为快看看 Ruby 2.7 中的模式匹配是什么样子吗？代码的合并，每夜构建正在进行中，我是一个相当不耐烦的作家，想看看新的礼物。

[![](img/695b54fe81fb36b264547741ab1aaa95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ha4wwkeZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2Ap0Ad_zpGIOEFOTX4Yft1Fw.png)

这是我第一次讨论模式匹配，随着我有更多的时间来试验这些特性，将会有更详细的文章问世。

要明确的是:这篇文章将曲折，因为这是第一印象。明天我会对它进行更多的测试，但今晚我想写点东西来看看第一印象有多匹配。

### 短版

这将是一篇很长的文章，简短的版本将不会涵盖它的甚至一小部分。每个主要部分将有一个单一的风格，它涵盖。

如果您想一目了然，测试文件涵盖了这方面的大部分内容:

[红宝石/红宝石](https://github.com/ruby/ruby/blob/9738f96fcfe50b2a605e350bdd40bd7a85665f54/test/ruby/test_pattern_matching.rb)

### 文字匹配

与常规的`case`语句非常相似，您[可以执行文字匹配](https://github.com/ruby/ruby/blob/9738f96fcfe50b2a605e350bdd40bd7a85665f54/test/ruby/test_pattern_matching.rb#L64) :

```
case 0
in 0 then true
else false
end
# => true 
```

Enter fullscreen mode Exit fullscreen mode

在这些情况下，最好使用 when。

不同之处在于，如果没有匹配，那么[会抛出一个](https://github.com/ruby/ruby/blob/9738f96fcfe50b2a605e350bdd40bd7a85665f54/test/ruby/test_pattern_matching.rb#L83) [`NoMatchingPattern`](https://github.com/ruby/ruby/blob/9738f96fcfe50b2a605e350bdd40bd7a85665f54/test/ruby/test_pattern_matching.rb#L83) 错误，而不是像`case` / `when`语句那样返回`nil`。

### 多个匹配器

在典型的`case`语句中，会使用逗号，但是在一些新的语法中，这会破坏一些析构，这些析构将在后面提到:

```
case 0
in 0 | 1
  true
end
# => true 
```

Enter fullscreen mode Exit fullscreen mode

对于以下捕获的变量，它们不能混合:

```
case 0
in a | 0
end
# Error: illegal variable in alternative pattern 
```

Enter fullscreen mode Exit fullscreen mode

### 捕获的变量

一个更有趣的增加是[捕获变量](https://github.com/ruby/ruby/blob/9738f96fcfe50b2a605e350bdd40bd7a85665f54/test/ruby/test_pattern_matching.rb#L104) s:

```
case true
in a
  a
end
# => true 
```

Enter fullscreen mode Exit fullscreen mode

**在保护条款中**

这些也可以在[后缀条件句中使用，或者更确切地说，在让人想起 Haskell:
的](https://github.com/ruby/ruby/blob/9738f96fcfe50b2a605e350bdd40bd7a85665f54/test/ruby/test_pattern_matching.rb#L125)中使用

```
case 0
in a if a == 0
  true
end
# => true 
```

Enter fullscreen mode Exit fullscreen mode

**在赋值映射中**

它们甚至可以被赋予一个类似散列的语法:

```
case 0
in 0 => a
  a == 0
end 
```

Enter fullscreen mode Exit fullscreen mode

**带占位符**

本节中最有趣的是，它们似乎将下划线视为特殊的占位符:

```
case 0
in _ | _a
  true
end 
```

Enter fullscreen mode Exit fullscreen mode

虽然这也可以被看作是一种文字变量赋值，但这是 Scala 模式匹配通配符的有趣重复。

### **解构**

有趣的是，你也可以在析构数组时隐藏变量:

```
case [0, 1]
in a, a
  a == 1
end
# => true 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，最后一个赋值似乎是 a，逗号现在用于析构，而不是像 when 那样表示要匹配的单独模式。

**解构**

在文件的顶部定义了一个类:

```
class C
  class << self
    attr_accessor :keys
  end

  def initialize(obj)
    @obj = obj
  end

  def deconstruct
    @obj
  end

  def deconstruct_keys(keys)
    C.keys = keys
    @obj
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

它似乎指出了一种定义如何通过模式匹配来析构一个对象的方法:

```
[[0, 1], C.new([0, 1])].all? do |i|
  case i
  in 0, 1
    true
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

包括不平衡匹配的核算:

```
[[0], C.new([0])].all? do |i|
  case i
  in 0, 1
  else
    true
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我不明白`deconstruct_keys`是如何工作的，但第一眼看上去，我真的觉得很奇怪。

**带劈啪声**

它还将捕获多个参数，就像当前在 Ruby 数组上工作的析构一样:

```
case []
in *a
  a == []
end

case [0, 1, 2]
in *a, 1, 2
  a == [0]
end 
```

Enter fullscreen mode Exit fullscreen mode

这意味着`*`也可以用作通配符:

```
case 0
in *
 true
end 
```

Enter fullscreen mode Exit fullscreen mode

**上哈希**

这些看起来很像关键字参数:

```
case {a: 0}
in a: 0
  true
end 
```

Enter fullscreen mode Exit fullscreen mode

这也意味着这里可以使用 kws plats:

```
case {}
in **a
  a == {}
end 
```

Enter fullscreen mode Exit fullscreen mode

…对于上面的关键字，这意味着类可以使用关键字定义它们自己的析构，但是我不清楚这一点。

我真的希望这也在每个参数上使用`===`,但是测试没有指出这一点。稍后我会用这个做实验。

### 三重相等

就像我们当前的 case 语句一样，模式[匹配的新方法使用](https://github.com/ruby/ruby/blob/9738f96fcfe50b2a605e350bdd40bd7a85665f54/test/ruby/test_pattern_matching.rb#L255) [`===`](https://github.com/ruby/ruby/blob/9738f96fcfe50b2a605e350bdd40bd7a85665f54/test/ruby/test_pattern_matching.rb#L255) 进行比较:

```
case 'abc'
in /a/
  true
end

case 0
in -> i { i == 0 }
  true
end 
```

Enter fullscreen mode Exit fullscreen mode

**结合解构**

我在`Qo`中喜欢的一件事是使用`===`编写查询来匹配另一个数组的能力。看起来新的语法将比较每个元素，并给我们一些非常相似的东西:

```
case [0, 1, 2, 3, 4, 5]
in [0..1, 0...2, 0.., 0..., (...5), (..5)]
  true
end 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，他们在例子中包括了无始无终的范围，因为这在以后可能会非常有用。我当然希望这些能和哈希一起工作，因为我*真的真的真的*希望这个能工作:

```
case object
in method_a: 0..10, method_b: String
 true
end 
```

Enter fullscreen mode Exit fullscreen mode

…因为如果我们能定义自己的解构者，想象一下那里的可能性。虽然键的类属性很奇怪，但不知道该如何看待它。

### 销符

我将不得不回到这个问题上来，因为我不理解它。我假设这意味着不要赋值并“固定”一个变量，这样它[就不会被覆盖](https://github.com/ruby/ruby/blob/9738f96fcfe50b2a605e350bdd40bd7a85665f54/test/ruby/test_pattern_matching.rb#L306) :

```
a = /a/
case 'abc'
in ^a
  true
end

case [0, 0]
in a, ^a
  a == 0
end 
```

Enter fullscreen mode Exit fullscreen mode

我相信这可以用来捕获变量中的一个元素，并在以后像反向引用一样引用它。

### 思想？

这还没有与 Nightly 一起构建，所以我打算在它清除后进行第二次传递，以验证一些我怀疑的行为。我最想看到的是散列匹配器，因为那里有一些惊人的潜力。

在接下来的几周内，我们将继续深入研究这个问题，下面是这个系列的下一篇文章:

[Ruby 2.7 —模式匹配—在点上析构](https://medium.com/@baweaver/ruby-2-7-pattern-matching-destructuring-on-point-90f56aaf7b4e)