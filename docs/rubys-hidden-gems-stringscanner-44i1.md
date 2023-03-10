# 红宝石的隐藏宝石，弦扫描仪

> 原文：<https://dev.to/appsignal/rubys-hidden-gems-stringscanner-44i1>

Ruby 不仅是一种有趣的语言，它还附带了一个优秀的标准库。其中一些并不为人所知，几乎是隐藏的宝石。今天，客座作家迈克尔科尔强调了一个最爱:Stringscanner。

# 红宝石的隐藏宝石:弦扫描器

从像 [OpenStruct](https://ruby-doc.org/stdlib-2.6.1/libdoc/ostruct/rdoc/OpenStruct.html) 和 [Set](https://ruby-doc.org/stdlib-2.6.1/libdoc/set/rdoc/Set.html) over [CSV 解析](https://ruby-doc.org/stdlib-2.6.1/libdoc/csv/rdoc/CSV.html)到[基准测试](https://ruby-doc.org/stdlib-2.6.1/libdoc/benchmark/rdoc/Benchmark.html)这样的数据结构，不需要借助安装第三方 gem 就可以走得很远。然而，在 Ruby 的标准安装中有一些不太知名的库非常有用，其中之一是`StringScanner`，根据[文档](https://ruby-doc.org/stdlib-2.6.1/libdoc/strscan/rdoc/StringScanner.html) *“提供了对字符串的词法扫描操作”*。

## 扫描和解析

那么“词汇扫描”到底是什么意思呢？本质上，它描述了按照一定的规则从输入字符串中提取有意义的信息的过程。例如，这可以在编译器的第一阶段看到，它将一个类似于`2 + 1`的表达式作为输入，并将其转换成下面的标记序列:

```
[{ number: "1" }, {operator: "+"}, { number: "1"}] 
```

Enter fullscreen mode Exit fullscreen mode

词法扫描器通常被实现为[有限状态自动机](https://en.wikipedia.org/wiki/Finite-state_machine)，有几个众所周知的工具可以为我们生成它们(例如 [ANTLR](https://www.antlr.org/) 或 [Ragel](https://www.colm.net/open-source/ragel/) )。

然而，有时我们的解析需求并不复杂，在这种情况下，像基于的[正则表达式`StringScanner`这样简单的库会非常方便。它的工作原理是记住所谓的*扫描指针*的位置，该指针只不过是字符串的一个索引。然后，扫描过程尝试将扫描指针后面的代码与提供的表达式进行匹配。除了匹配操作，`StringScanner`还提供了移动扫描指针(在字符串中向前或向后移动)、向前看(在不修改扫描指针的情况下看到下一步)以及找出我们当前在字符串中的位置(是一行的开头还是结尾/整个字符串等等)的方法。](https://en.wikipedia.org/wiki/Regular_expression)

## 解析 Rails 日志

理论够了，让我们看看`StringScanner`在行动。下面的例子将采用如下所示的 Rails 日志条目，

```
log_entry = <<EOS Started GET "/" for 127.0.0.1 at 2017-08-20 20:53:10 +0900
Processing by HomeController#index as HTML
  Rendered text template within layouts/application (0.0ms)
  Rendered layouts/_assets.html.erb (2.0ms)
  Rendered layouts/_top.html.erb (2.6ms)
  Rendered layouts/_about.html.erb (0.3ms)
  Rendered layouts/_google_analytics.html.erb (0.4ms)
Completed 200 OK in 79ms (Views: 78.8ms | ActiveRecord: 0.0ms) EOS 
```

Enter fullscreen mode Exit fullscreen mode

并将其解析为以下哈希:

```
{
  method: "GET",
  path: "/"
  ip: "127.0.0.1",
  timestamp: "2017-08-20 20:53:10 +0900",
  success: true,
  response_code: "200",
  duration: "79ms",
} 
```

Enter fullscreen mode Exit fullscreen mode

*注意:虽然这为`StringScanner`提供了一个很好的例子，但是一个真正的应用程序使用[日志范围](https://github.com/roidrage/lograge)和它的 JSON 日志格式化程序会更好。*

为了使用`StringScanner`，我们首先需要要求它:

```
require 'strscan' 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们可以通过将日志条目作为参数传递给构造函数来初始化一个新的实例。同时，我们还将定义一个空散列来保存解析结果:

```
scanner = StringScanner.new(log_entry)
log = {} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以使用扫描仪的 [pos](https://ruby-doc.org/stdlib-2.6.1/libdoc/strscan/rdoc/StringScanner.html#method-i-pos) 方法来获取扫描指针的当前位置。不出所料，结果是`0`，字符串的第一个字符:

```
scanner.pos #=> 0 
```

Enter fullscreen mode Exit fullscreen mode

让我们想象一下，这样这个过程会更容易理解:

```
Started GET "/" for 127.0.0.1 at 2017-08-20 20:53:10 +0900
^
...
Completed 200 OK in 79ms (Views: 78.8ms | ActiveRecord: 0.0ms) 
```

Enter fullscreen mode Exit fullscreen mode

为了进一步检查扫描仪的状态，我们可以使用 [`beginning_of_line?`](https://ruby-doc.org/stdlib-2.6.1/libdoc/strscan/rdoc/StringScanner.html#method-i-beginning_of_line-3F) 和 [`eos?`](https://ruby-doc.org/stdlib-2.6.1/libdoc/strscan/rdoc/StringScanner.html#method-i-eos-3F) 来确认扫描指针当前位于一行的开头，并且我们还没有完全使用我们的输入:

```
scanner.beginning_of_line? #=> true
scanner.eos? #=> false 
```

Enter fullscreen mode Exit fullscreen mode

我们想要提取的第一点信息是 HTTP 请求方法，可以在单词“Started”后面的空格中找到。我们可以使用扫描仪适当命名的 [skip](https://ruby-doc.org/stdlib-2.6.1/libdoc/strscan/rdoc/StringScanner.html#method-i-skip) 方法来推进扫描指针，这将返回被忽略的字符数，在我们的例子中是 8。另外我们可以用[搭配吗？](https://ruby-doc.org/stdlib-2.6.1/libdoc/strscan/rdoc/StringScanner.html#method-i-matched-3F)确认一切正常:

```
scanner.skip(/Started /) #=> 8
scanner.matched? #=> true 
```

Enter fullscreen mode Exit fullscreen mode

扫描指针现在正好在请求方法之前:

```
Started GET "/" for 127.0.0.1 at 2017-08-20 20:53:10 +0900
       ^
...
Completed 200 OK in 79ms (Views: 78.8ms | ActiveRecord: 0.0ms) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用 [scan_until](https://ruby-doc.org/stdlib-2.6.1/libdoc/strscan/rdoc/StringScanner.html#method-i-scan_until) 来提取实际值，这将返回整个正则表达式匹配。因为请求方法都是大写的，所以我们可以使用一个简单的字符类和匹配一个 or 字符的`+`操作符:

```
log[:method] = scanner.scan_until(/[A-Z]+/) #=> "GET" 
```

Enter fullscreen mode Exit fullscreen mode

在此操作之后，扫描指针将位于单词“GET”的最后一个“T”处。

```
Started GET "/" for 127.0.0.1 at 2017-08-20 20:53:10 +0900
          ^
...
Completed 200 OK in 79ms (Views: 78.8ms | ActiveRecord: 0.0ms) 
```

Enter fullscreen mode Exit fullscreen mode

为了提取请求的路径，我们需要跳过一个空格，然后提取双引号中的所有内容。有几种方法可以实现这一点，其中之一是通过一个捕获组(包含在括号中的正则表达式的一部分，即`(.+)`)来匹配一个或多个任意字符:

```
scanner.scan(/\s"(.+)"/) #=> " \"/\"" 
```

Enter fullscreen mode Exit fullscreen mode

但是，我们不会直接使用这个`scan`操作的返回值，而是使用[捕获](https://ruby-doc.org/stdlib-2.6.1/libdoc/strscan/rdoc/StringScanner.html#method-i-captures)来获得第一个捕获组的值:

```
log[:path] =  scanner.captures.first #=> "/" 
```

Enter fullscreen mode Exit fullscreen mode

我们成功提取了路径，扫描指针现在位于右双引号:

```
Started GET "/" for 127.0.0.1 at 2017-08-20 20:53:10 +0900
              ^
...
Completed 200 OK in 79ms (Views: 78.8ms | ActiveRecord: 0.0ms) 
```

Enter fullscreen mode Exit fullscreen mode

为了从日志中解析 IP 地址，我们再次使用`skip`来忽略由空格包围的字符串“for”，然后使用`scan_until`来匹配一个或多个非空白字符(`\s`是表示空白的字符类，`[^\s]`是它的否定):

```
scanner.skip(/ for /) #=> 5
log[:ip] = scanner.scan_until(/[^\s]+/) #=> "127.0.0.1" 
```

Enter fullscreen mode Exit fullscreen mode

你能说出扫描指针现在会在哪里吗？思考片刻，然后将您的答案与解决方案进行比较:

```
Started GET "/" for 127.0.0.1 at 2017-08-20 20:53:10 +0900
                            ^
...
Completed 200 OK in 79ms (Views: 78.8ms | ActiveRecord: 0.0ms) 
```

Enter fullscreen mode Exit fullscreen mode

解析时间戳现在应该感觉非常熟悉了。首先我们使用可靠的旧的`skip`来忽略文字字符串`" at "`，然后使用`scan_until`来读取直到当前行的末尾，这在正则表达式中由`$`表示:

```
scanner.skip(/ at /) #=> 4
log[:timestamp] = scanner.scan_until(/$/) #=> "2017-08-20 20:53:10 +0900" 
```

Enter fullscreen mode Exit fullscreen mode

我们感兴趣的下一条信息是最后一行上的 HTTP 状态代码，所以我们将使用 [skip_until](https://ruby-doc.org/stdlib-2.6.1/libdoc/strscan/rdoc/StringScanner.html#method-i-skip_until) 将我们一直带到单词“Completed”后面的空格。

```
scanner.skip_until(/Completed /) #=> 296 
```

Enter fullscreen mode Exit fullscreen mode

顾名思义，这与`scan_until`的工作方式类似，但它返回跳过的字符数，而不是返回匹配的字符串。这将扫描指针放在我们感兴趣的 HTTP 状态代码的正前方。

```
Started GET "/" for 127.0.0.1 at 2017-08-20 20:53:10 +0900
...
Completed 200 OK in 79ms (Views: 78.8ms | ActiveRecord: 0.0ms)
         ^ 
```

Enter fullscreen mode Exit fullscreen mode

现在，在我们扫描实际的 HTTP 响应代码之前，如果我们能够知道 HTTP 响应代码是表示成功(对于本例来说，是 2xx 范围内的任何代码)还是失败(所有其他范围)，不是很好吗？为了实现这一点，我们将利用 [peek](https://ruby-doc.org/stdlib-2.6.1/libdoc/strscan/rdoc/StringScanner.html#method-i-peek) 来查看下一个字符，而不实际移动扫描指针。

```
log[:success] = scanner.peek(1) == "2" #=> true 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以用[扫描](https://ruby-doc.org/stdlib-2.6.1/libdoc/strscan/rdoc/StringScanner.html#method-i-scan)读取接下来的三个字符，用正则表达式`/\d{3}/` :
表示

```
log[:response_code] = scanner.scan(/\d{3}/) #=> "200" 
```

Enter fullscreen mode Exit fullscreen mode

扫描指针将再次位于先前匹配的正则表达式的末尾:

```
Started GET "/" for 127.0.0.1 at 2017-08-20 20:53:10 +0900
...
Completed 200 OK in 79ms (Views: 78.8ms | ActiveRecord: 0.0ms)
            ^ 
```

Enter fullscreen mode Exit fullscreen mode

我们希望从日志条目中提取的最后一点信息是以毫秒为单位的执行时间，这可以通过`skip` ping 字符串`" OK in "`然后读取包括文字字符串`"ms"`在内的所有内容来实现。

```
scanner.skip(/ OK in /) #=> 7
log[:duration] = scanner.scan_until(/ms/) #=> "79ms" 
```

Enter fullscreen mode Exit fullscreen mode

有了最后一点，我们就有了我们想要的散列。

```
{
  method: "GET",
  path: "/"
  ip: "127.0.0.1",
  timestamp: "2017-08-20 20:53:10 +0900",
  success: true,
  response_code: "200",
  duration: "79ms",
} 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

Ruby 的`StringScanner`在简单的正则表达式和成熟的词法分析器之间占据了一个很好的中间位置。对于复杂的扫描和解析需求来说，这不是最佳选择。但是它的简单性使得每个具有基本正则表达式知识的人都可以很容易地从输入字符串中提取信息，我过去已经在生产代码中成功地使用过这些信息。我们希望你能发现这颗隐藏的宝石。

迈克尔美国柯尔百货公司与鲁比的恋情始于 2003 年。他也喜欢写作和谈论语言，并共同组织了[曼谷. rb](https://www.meetup.com/bangkok-rb/members/) 和[泰国 ruby conf](https://rubyconfth.com/)。