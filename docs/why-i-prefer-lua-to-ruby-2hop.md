# 为什么我喜欢 Lua 胜过 Ruby

> 原文：<https://dev.to/darkwiiplayer/why-i-prefer-lua-to-ruby-2hop>

所以 Lua 和 Ruby 在很多方面是两种相当相似的语言。有很多文章解释了是什么让它们如此相似，以及许多更表面的差异。

今天我想指出一些形成我对这两种语言看法的原因。

我可能听起来像一个憎恨者，但是我想指出我并不是特别不喜欢 Ruby。它有许多积极的方面，但是因为我更喜欢 Lua，所以我今天只选择指出它的一些问题。

# 速度

简单地说“Lua 快，Lua 好”就可以了。然而在现实中，事情并不总是那么简单。

### 文件大小

Lua 的问题是，它非常小。这不仅意味着它几乎适用于任何地方，而且加载速度非常快

```
> time ruby -e 'puts "hello"'
hello

real    0m0.059s
user    0m0.054s
sys     0m0.005s

> time lua -e 'print "hello"'
hello

real    0m0.003s
user    0m0.003s
sys     0m0.001s 
```

Enter fullscreen mode Exit fullscreen mode

只有当您连续多次调用可执行文件时，才会有所不同，但是随着时间的推移，这种不同会逐渐增加。

* * *

### 函数与方法

接下来要考虑的是，在 Lua 中，函数存储在变量中，因此很容易查找。

另一方面，Ruby 必须为所有事情查找方法。对于短方法(比如说，添加两个数字)，这意味着巨大的开销。

```
> ruby -e 'puts self.class.ancestors'
Object
Kernel
BasicObject 
```

Enter fullscreen mode Exit fullscreen mode

甚至每个 ruby 脚本运行的主对象都继承自 3 个祖先。

应该注意的是，Lua 也不能避免这个问题。当编写面向对象的代码时，这种情况也会发生。不同之处在于，在 Lua 中，程序员必须显式地这样做，而在 Ruby 中，这种情况经常发生。

* * *

### 字符串/符号

这可能会让一些人感到惊讶，但是 Lua 实际上在速度上比 Ruby 有一个巨大的劣势:它没有 Ruby 所拥有的字符串。Lua 中的所有字符串都是自动保留的。Ruby 也有内部字符串；它称它们为符号。

总的来说，这可能是一个明智的决定，但是这也意味着在 Lua 中读取一个长文本文件需要更长的时间。想象一下在 Ruby 的每一行都调用`to_sym`。

`<edit>`

自从写了这个帖子，我反复检查了这个，发现我显然误解了这个或者只是记错了:Lua *默认情况下*会对所有**短**字符串进行实习，然而，在一定长度以上，这种情况不再发生了。对于 interning 来说太长的字符串不会立即进行哈希处理，但是一旦有必要(例如，当将它们与另一个字符串进行比较或索引一个表时)就会进行哈希处理，并且从那时起就只使用那个哈希。

这很大程度上意味着，在大多数情况下，Lua 甚至在处理长(但不可变的)字符串时也能胜过 Ruby。

然而，Lua 字符串不可变的缺点仍然存在，修改字符串意味着创建一个新的、修改过的字符串副本。

`</edit>`

* * *

### Vararg 函数

如果有人问我，这两种语言中哪一种具有强大的可变函数实现，我 100%会说是 Ruby。您可以混合和匹配多种语法结构，将额外的参数捕获到数组和散列中。

但是，强大的力量来了...不是很好的表现。
让我们考虑下面这段代码:

```
def foo(bar, *rest)
    do_something(bar)
    do_something(*rest)
end 
```

Enter fullscreen mode Exit fullscreen mode

每次 foo 方法被调用时，ruby 都需要实例化一个新的数组`rest`并将多余的参数收集到其中。这种情况经常发生，使得变量方法非常不适合需要良好执行的代码。

考虑 Lua 中的等价代码:

```
function foo(bar, ...)
    do_something(bar)
    do_something(...)
end 
```

Enter fullscreen mode Exit fullscreen mode

这里不涉及数组。该函数可以将 bar 留在堆栈中，调用`do_something`告诉它有 1 个参数，然后从堆栈中弹出 bar 并再次调用`do_something`，告诉它堆栈中还有多少项。

这意味着，即使您的代码需要尽可能快地运行，编写这样的函数也更加可行。

* * *

### 心态

一个可能比大多数人想象的更大的差异是两个社区之间的心态差异。

当被问及为什么 Ruby 如此伟大时，社区的普遍反应是“因为写起来很有趣！”。许多 Ruby 例子似乎在开始之前就把性能抛到了九霄云外。这不一定是一件坏事，但它最终会导致人们编写的库运行得比他们能做到的要慢。

我在 Lua 社区的经历完全不同。一个简单的谷歌搜索就能找到更多关于如何提高 Lua 性能的相关和详细信息，尽管后者有一个更大的社区，似乎也写了更多关于它的内容。

* * *

### LuaJIT

Lua 本身已经很快了。不过，那是完全不同的层次。有例子表明，JITed Lua 代码甚至可以比同等的 C 代码运行得更快，因为编译器有时对它正在优化的代码有更多的了解(毕竟，它在代码执行时跟踪代码)

Ruby 已经用它自己的 JIT 编译器在正确的方向上迈出了一大步，但是与 PUC Lua——“参考”Lua 实现相比，它的性能改进还远远不够。

* * *

# 简朴

现在先把性能放在一边，我认为简单是 Lua 的一个非常积极的特性的另一个原因是:它不仅容易学习，而且容易掌握。

### 模块化

这可能是我最讨厌 Ruby 的一点。无论你如何努力，你都无法摆脱全球状态。至少你会有一些污染全局环境的模块和类，并且没有办法将一个库加载到一个包含的空间中。

这意味着，例如，如果不仔细检查源代码并到处重命名，就不可能加载一个库的两个不同版本。

```
require 'some_class'

foo = SomeClass.new # Where did this come from?! 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，在 Lua 中，您可以重命名文件并将其加载到另一个本地。如何在内部调用对象与宿主程序无关。

```
local someclass = require 'someclass'

local foo = someclass.new() 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 副作用

Ruby 尽可能让开发人员感到舒适。我经常发现自己希望没有

考虑下面的例子:

```
require 'missile_cruiser'

MissileCruiser.new.last_fired 
```

Enter fullscreen mode Exit fullscreen mode

那里发生了什么？有人会假设，变量`last_fired`将返回最后发射的导弹。既然我们没有开一枪，那么这个是`nil`也是合理的。

但是等等，如果是方法呢？

也许它会引发一个错误，因为我们还没有发射任何导弹？

更糟糕的是，可能一些开发人员认为如果我们想知道最后一枚导弹，我们可能想发射一枚，所以这个方法只是发射一枚导弹并返回那一枚？

这种事情在 Ruby 经常发生。什么是值，什么是被执行的代码之间的界限是模糊的。

* * *

### 【不一致】

尝试`irb`中的以下代码:

```
puts 1/0
puts 0/0
puts 1.0/0
puts 0.0/0 
```

Enter fullscreen mode Exit fullscreen mode

诸如此类的事情使得一种语言(以及用这种语言编写的程序)乍一看很难理解，尤其是对新手而言。没有哪种语言是 100%一致的，但是只要有可能，还是应该减少这种不一致。