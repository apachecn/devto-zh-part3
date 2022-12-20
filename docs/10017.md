# Ruby 2.7 —编号参数

> 原文：<https://dev.to/baweaver/ruby-2-7-numbered-parameters-1a79>

### Ruby 2.7 —编号参数

Ruby 2.7 将于今年 12 月发布，就像所有现代版本一样，但这并不能阻止我们在这段时间寻找和写下我们发现的所有有趣的事情！不不不。

[![](img/695b54fe81fb36b264547741ab1aaa95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ha4wwkeZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2Ap0Ad_zpGIOEFOTX4Yft1Fw.png)

对于本文，我们有一些很容易让人想起 Bash、Perl 和 Scala 的东西:编号参数。

> **注意**:该语法从`_1`更新为`_1`，文章将对此进行更新。

### 短版

如果你有一个简单的带有位置参数的程序块，尤其是单个位置参数，你可以做如下操作:

```
[1, 2, 3].map { _1 + 3 }
=> [4, 5, 6] 
```

Enter fullscreen mode Exit fullscreen mode

…其中`_1`是块函数的第一个参数。

### 讨论

如果您想了解此功能背后的讨论，可以在此处找到:

[特性#4475:参数- Ruby 主干- Ruby 问题跟踪系统的默认变量名](https://bugs.ruby-lang.org/issues/4475)

### 例子

由于这直接公开了对象，所以大多数示例只是对您可能已经熟悉的内容进行了轻微的调整。让我们看看测试代码中的一些例子:

[编号参数[功能#4475] ruby/ruby_12acc75](https://github.com/ruby/ruby/commit/12acc751e3e7fd6f8aec33abf661724ad76c862a#diff-4216bbf6387c50d56b418c67768dcccb)

#### **只在**区块有效

编号参数仅在块内引用时有效:

```
assert_syntax_error('_1', /outside block/)
assert_valid_syntax('proc {_1}') 
```

Enter fullscreen mode Exit fullscreen mode

这意味着这是有效的:

```
proc { _1 }
=> #<Proc:0x0000000111d42990@(pry):6> 
```

Enter fullscreen mode Exit fullscreen mode

…这将导致语法错误:

```
_1
SyntaxError: (eval):2: numbered parameter outside block 
```

Enter fullscreen mode Exit fullscreen mode

现在，这个错误与老版本的 Ruby 相比有了一点小小的改进，它识别了类实例变量语法的第二次使用，并让我们知道我们在块外使用了它。

#### **命名规则**

编号参数必须遵循一些规则，即其中只有数字，0 和前导 0 都是错误:

```
assert_syntax_error('proc {_01}', /leading zero/) assert_syntax_error('proc {_1_}', /unexpected/) 
```

Enter fullscreen mode Exit fullscreen mode

这也意味着，如果你尝试使用下划线来表示更长的数字，它会做坏事:

```
(1..1_000_000).each_slice(1_000).map { _1 + _1_000 + _2 + _3 }
SyntaxError: unexpected local variable or method, expecting '}'
..._slice(1_000).map { _1 + _1_000 + _2 + _3 }
... ^~~~
(eval):2: numbered parameter outside block
...e(1_000).map { _1 + _1_000 + _2 + _3 }
... 
```

Enter fullscreen mode Exit fullscreen mode

#### **多个编号参数**

假设我们有集合或者甚至是散列，我们可以使用 _2，如果我们需要它们来得到特定的值:

```
assert_equal(3, eval('[1,2].then {_1+_2}'))
assert_equal("12", eval('[1,2].then {"#_1#_2"}')) 
```

Enter fullscreen mode Exit fullscreen mode

对于哈希表，这意味着您可以访问键和值:

```
{name: 'foo', age: 42}.map { [_1, _2] }
=> [[:name, "foo"], [:age, 42]] 
```

Enter fullscreen mode Exit fullscreen mode

如果你有三个小组，你甚至可以开始使用更多:

```
(1..9).each_slice(3).map { _1 + _2 + _3 }
=> [6, 15, 24] 
```

Enter fullscreen mode Exit fullscreen mode

…尽管开始进入太多这些编号的参数可能是不明智的，因为最终你会用完。

#### **太大的数字**

那里*是*的一个极限，但是它相当高:

```
assert_syntax_error('proc {_9999999999999999}', /too large/) 
```

Enter fullscreen mode Exit fullscreen mode

在这里定义的这个常数:

```
#define NUMPARAM_MAX 100 /* INT_MAX */ 
```

Enter fullscreen mode Exit fullscreen mode

尽管在接近这个数字之前，应该有警告信号表明你正在做一些奇怪的事情。

目前，如果你尝试，Pry 只会放弃，并期待更多的输入:

```
[13] pry(main)> (1..1_000_000).each_slice(1_000).map { _101 }
[13] pry(main)* 
```

Enter fullscreen mode Exit fullscreen mode

#### 普通参数

Ruby 不喜欢混搭我们当前的块参数方式:

```
assert_syntax_error('proc {|| _1}', /ordinary parameter is defined/) assert_syntax_error('proc {|x| _1}', /ordinary parameter is defined/) 
```

Enter fullscreen mode Exit fullscreen mode

如果你决定使用这个，知道它是一个或另一个，而不是两个。

#### 散列和对象

最好记住 _1 和 friends 只是 Ruby 对象，这意味着我们可以调用它们上面的任何参数:

```
[{name: 'foo'}, {name: 'bar'}].map { _1[:name] }
=> ["foo", "bar"]

[{name: 'foo'}, {name: 'bar'}]
  .map { OpenStruct.new(_1) }
  .map { _1.name }
=> ["foo", "bar"] 
```

Enter fullscreen mode Exit fullscreen mode

尽管在最后一个例子中，最好记住当前的简写语法`map(&:name)`。

### 包装完毕

这绝对是一个非常有趣的特性，尽管我确实感觉到了 Matz 所说的话:

> 看到@和@1 等还是觉得怪怪的。也许过一段时间我就会习惯了。
> 
> 我需要时间。
> 
> *   马茨。
> 
> **注**:语法从`@1`更新为`_1`，更新上述注释以反映这一点。

我想知道它会带来什么新的东西，但我仍然很兴奋地想知道人们还可以用它做什么。

2.7 已经有了一个有趣的开始，让我们看看它从这里走向何方。