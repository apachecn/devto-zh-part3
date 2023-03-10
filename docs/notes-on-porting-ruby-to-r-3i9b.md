# 将 Ruby 移植到 R 的注意事项

> 原文：<https://dev.to/sckott/notes-on-porting-ruby-to-r-3i9b>

在多次移植 Ruby gems to R ( [vcr](https://github.com/ropensci/vcr) ，[web mocker](https://github.com/ropensci/webmockr))的过程中，我注意到了这两种语言之间的一些差异，深入其中很有趣，至少对我来说是这样。

## 猴子打补丁

Ruby 有一个很好的东西，你可以[“猴子补丁”](https://en.wikipedia.org/wiki/Monkey_patch)类/方法/等等。在其他 Ruby 库中。例如，假设你有红宝石`foo`和`bar`。如果`foo`有一个方法`hello`，你可以用`bar`中的方法覆盖`foo`中的`hello`方法。Rubygems.org 的 gems 和整个社区都可以接受这一点。

猴子补丁在 R 中技术上是可行的，但是在 CRAN 上的包中是不允许的(警告见`?assignInNamespace`帮助)，尽管[在 CRAN 包](https://github.com/search?p=1&q=org%3Acran+assignInNamespace&type=Code)中有一些用法。我们可以使用`utils::assignInNamespace`来做到这一点。假设你有一个 R 包`foo`和另一个 R 包`bar`。这里，我们可以给已经在`foo` :
中定义的方法分配一个新的`hello`方法

```
# the foo::hello method looks like  hello  <-  function()  return("world!")  # make a new hello method  hello2  <-  function()  return("mars!")  # override the hello method in foo  utils::assignInNamespace("hello",  hello2,  "foo") 
```

Enter fullscreen mode Exit fullscreen mode

尝试任何包装。很好玩。

您可以通过使用一个`.onAttach`指令在一个包中完成这项工作。

```
.onAttach  <-  function(libname,  pkgname)  {  utils::assignInNamespace("bar",  bar,  "foo")  } 
```

Enter fullscreen mode Exit fullscreen mode

无论如何，猴子补丁并不是 R 中真正的东西，所以这使得把 Ruby 移植到 R 中变得更加困难。例如，在 [vcr](https://github.com/ropensci/vcr) 和 [webmockr](https://github.com/ropensci/webmockr) 中，我不能简单地覆盖它们所挂钩的 http 库中的方法，而是必须对 http 库本身进行修改以支持 HTTP 模仿——我们最终会实现这一点，但会花费更长的时间，尽管可能会更安全？

## 基于 0(红宝石)与 1(红宝石)的索引

不断重复这些不会有坏处。

## 序列

Ruby 能够用`..`和`...`构造数字序列，例如

```
# inclusive of second number
x = 1..3
x.to_a
=> [1, 2, 3]
# exclusive of second number
x = 1...3
x.to_a
=> [1, 2] 
```

Enter fullscreen mode Exit fullscreen mode

AFAIK，在 R 中我们只能做包含序列

```
1:3  #> [1] 1 2 3 
```

Enter fullscreen mode Exit fullscreen mode

## 显式导入

至少在 Ruby 和 Python 中，你必须明确说明从其他文件导入方法的位置。

而在 R 中，你可以使用一个函数/等等。包中的任何其他文件，而不声明您需要它。这使得推理依赖函数等变得更加困难。任何一个文件都需要。对此有帮助的一个工具是 [functionMap](https://github.com/MangoTheCat/functionMap) (虽然最后一次提交是在 2016 年，不确定是否还在维护，是 Gábor 吗？).

另外，在 Ruby 中我们可以使用全局变量，比如:

```
$foo = 5 
```

Enter fullscreen mode Exit fullscreen mode

据我所知，以上是不好的做法，但我确实在自己的 Ruby 作品中使用了它们。

在 R 中，所有的变量/方法/类在包的命名空间中都是“全局”的。

## 添加字符串

唉，真希望 R 有和`+`一起加弦的能力。

## ？作为有效字符

嗯，好的。我喜欢 Ruby 中的方法，比如`nil?`、`empty?`等等。这样直截了当地表明意图。希望我们在 R 中有这些，但是`?`本身甚至不是一个有效的字符，所以从来没有？)会发生。

## 类

r 的最接近 Ruby 的类系统(我愿意使用)是来自 Winston Chang 的。使用`R6`使得从 Ruby 或类似语言移植变得更加容易，因为你可以直接翻译具有公共或私有方法、初始化器、打印方法等的类。另外，对于任何足够复杂的 R 包，使用`R6`可以更容易地管理复杂性。

## 红宝石的||=

在 ruby 中，这个操作符的基本意思是“如果 a 未定义或为 falsey，则计算 b 并将 a 设置为结果”。在 R，没有比这更好的了。`||=`在我移植的 Ruby gems 中被广泛使用，使得 R 中移植的版本更加冗长。我可以在 R `a %||% b`(其中`%||% = function(x, y) if (is.null(x) || !x) y else x`)中做“如果 a 为空，未定义或假，评估 b”；但之后我还得分配结果，给`a = a %||% b`。

## splat args

splat 操作符在 Ruby 中被大量使用。看起来像:

```
def foo(*args)
  p args
end
foo(1, 2, 3)
# => [1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

在 R 中，最相似的是省略号，所以

```
foo  <-  function(...)  c(...)  foo(1,  2,  3)  #> [1] 1 2 3 
```

Enter fullscreen mode Exit fullscreen mode

如果你知道如何进行这种转换，Ruby splat args 不会让你犯错。当然在 R 中也有`rlang`和诸如此类的。