# date-fns v2 API design

> 原文：<https://dev.to/kossnocorp/date-fns-v2-api-design-2jig>

*[date-fns](https://date-fns.org/) 是处理日期的 JavaScript 库。这是 Moment.js 的现代替代品*

近两年来，我们一直在开发一个新的主要版本— v2。我们几乎重新设计了该库的每个方面，并添加了许多令人兴奋的功能。v2 中最重要的变化之一是新的 API 设计。我们仔细完善了每一个功能，使日期-fns 一致，可预测和可靠。在这篇文章中，我讲述了帮助我们设计简单易用的 API 的目标和价值观。

## 目标和价值观

当我们刚刚开始研究 date-fns 时，我们唯一的目标是构建一个以函数风格处理日期的库。多年来，我们一直在添加越来越多的功能，经常会毫不犹豫地复制和修改 Moment.js API。

过了一段时间，我们意识到我们的 API 和行为并不像我们希望的那样一致。做类似工作的函数中的参数并不总是按照相同的顺序，而是有不同的命名方案。有时函数会在不应该抛出异常的地方抛出异常，而在应该抛出异常的时候却没有抛出。我说的不是强制逻辑；这甚至超出了我们的理解。

改变的需要是显而易见的，但首先，我们必须定义目标和价值观，这将有助于我们在不明确的情况下做出决定。

**尽可能坚持 JavaScript 行为**。我们希望 date-fns 成为该语言及其标准库的扩展，而不是替代品。我们相信，它将确保图书馆在快速变化的生态系统中有较长的寿命。

**坚持现有标准**。我们决定寻找当前的标准和成熟的实践，而不是重新发明轮子和依赖我们的主观意见。这将提供与其他语言的最佳兼容性，并使我们免于犯其他人几年前甚至几十年前犯下的错误。

**一致性**。我们希望日期-fns 尽可能地可预测和易于理解。函数名、参数顺序和行为必须在整个库中保持一致。

**明确性**。约会对象应该更喜欢明确而不是含蓄。有时后者有助于使代码看起来更整洁。你知道那种感觉，当一个图书馆一言不发地做你想让它做的事。但更常见的是，它会导致难以调试或者更难注意到的错误。

**方便**。我们希望日期-fns 使用起来愉快。我们应该帮助开发者避免错误。

虽然最后三个是不言自明的，但前两个需要深入研究。

## 尽可能坚持 JavaScript 行为

### 为什么

该库最初的想法是创建一组将与本机`Date`对象一起工作的助手。我们避免引入标准库中已经存在的功能和命名函数，因为它们是标准库的一部分。在 v2 中，我们决定更进一步，让 date-fn 在各个方面都像 JavaScript 一样。

JavaScript 及其行为经常成为批判的对象。由于多年来对向后兼容性的需要，它积累了如今被认为是糟糕的语言设计的细微差别。对，我说的就是它的强制规则，`NaN`，`null`等等。

然而，在每一个奇怪的行为背后，都隐藏着语言中的逻辑一致性。它可能涵盖了 JavaScript 的薄弱部分，但它是一致的，如果你学会了，它就开始有意义了。

### 异常情况

在 v2 中，我们让 date-fns 在标准 JavaScript 函数抛出`TypeError`和`RangeError`的情况下抛出。

每当需要一个参数时，JavaScript 抛出`TypeError`:

```
window.fetch()
//=> TypeError: 1 argument required, but only 0 present. 
```

从现在开始，所有函数检查传递的参数数量是否小于所需的参数数量，如果是，则抛出`TypeError`异常:

```
import { addDays } from 'date-fns'
addDays()
//=> TypeError: 2 arguments required, but only 0 present 
```

每当参数值不在允许值的集合或范围内时，JavaScript 抛出`RangeError`:

```
(42).toFixed(-1)
//=> RangeError: toFixed() digits argument must be between 0 and 100 
```

从现在开始，如果传递给 options 的可选值不是`undefined`或者没有预期值，函数就会抛出`RangeError`:

```
import { formatDistanceStrict } from 'date-fns'
formatDistanceStrict(new Date(2014, 6, 2), new Date(2015, 0, 1), {
  roundingMethod: 'qwe'
})
//=> RangeError: roundingMethod must be 'floor', 'ceil' or 'round' 
```

### 威压

就像 JavaScript date-fns 强制将参数传递给期望的类型一样。

```
import { addDays } from 'date-fns'
addDays(new Date(1987, 1, 11), '42')
//=> Wed Mar 25 1987 00:00:00 GMT+0530 (+0530) 
```

尽管强制是该语言最令人讨厌的方面，但它非常简单和一致，尽管它与算术运算符结合使用会导致意想不到的结果。

以下是我们用来强调论点的规则:

[![](img/68bf98383708c80b2f7b35c8d63e882d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pz8q5j5E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5zg2bzi4b8nd7xjdll3w.png)

在这里，列是我们期望的参数类型，行是我们实际提供的参数——例如，在 addDays 中，第一个参数将由“date”列的规则转换，第二个参数由“number”列的规则转换，因此`addDays(1, '1')`相当于`addDays(new Date(1), 1)`。

### 无效日期

`Date`内部用数字表示，与`Number`一样，对日期的错误操作会导致`Invalid Date`(无效日期是时间值为`NaN`的日期):

```
const date = new Date()

date.setHours('nope')
//=> NaN

date
//=> Invalid Date 
```

date-fns 反映了这种行为，当您传递不可转换的值时，它将返回`Invalid date`:

```
import { addDays } from 'date-fns/addDays'
addDays(new Date(), 'nope')
//=> 'Invalid date' 
```

这是特别难以做出的权衡之一:

> ![](img/8e3d368deb16bc681377ad2107163c21.png)T3】🎅圣诞节. preload()🎄@ Adam rackis![](img/4d9c44713c216584b3d48ff3455cbb68.png)加油 date-fns -如果我把一个字符串格式传递给 isAfter 或者 isBefore 你不喜欢，不要只是默默忽略它，返回 false——冲我吼！抛出一个鲜红色异常！如果我想要充满错误、难以调试的代码噩梦，我会使用这个平台。2018 年 11 月 06 日下午 19:45[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1059894523332120576)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1059894523332120576)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1059894523332120576)14

一方面，当一个参数有一个错误的值时，我们希望有一个异常。另一方面，该异常会迫使我们将每个函数调用包装到 try-catch 块中，这是糟糕的开发人员体验。标准 JavaScript 解决这个问题的方法是期望开发人员负责验证用户输入。在最坏的情况下，应用程序将打印`Invalid Date`并继续工作，如果我们抛出异常，这是不会发生的。

### 正在进行的工作

在写帖子的时候，我发现了一个我们没有考虑到的不一致。在无效日期调用`toString`时，返回`Invalid Date`、`toISOString`以及 Intl API 抛出`RangeError`:

```
date.toString()
//=> 'Invalid Date'

date.toISOString()
//=> RangeError: Invalid time value

new Intl.DateTimeFormat('en-US').format(date)
//=> RangeError: Invalid time value 
```

我们错误地将`toString`行为应用于`format`:

```
import { format } from 'date-fns'

const date = new Date()
date.setHours('nope')

format(date, 'yyyy-MM-dd')
//=> 'Invalid Date' 
```

我提出了一个问题，我们计划在第一个测试版发布之前解决:

# [![](img/375dfcc32199b4dedf2b526645c27ff7.png) 格式函数在试图处理无效日期时必须抛出异常 #987](https://github.com/date-fns/date-fns/issues/987) 

[![kossnocorp avatar](img/c67aeb0688c753e6514319d22e05d2cf.png) ](https://github.com/kossnocorp)  **[kossnocorp](https://github.com/kossnocorp)** commented on [Nov 20, 2018](https://github.com/date-fns/date-fns/issues/987)

推理见 JavaScript 的`toISOString`和 Intl API:

```
const date = new Date()

date.setHours('nope')
//=> NaN

date.getYear()
//=> NaN

date.toString()
//=> Invalid date

date.toISOString()
//=> RangeError: Invalid time value

new Intl.DateTimeFormat('en-US').format(date)
//=> RangeError: Invalid time value
```

[View on GitHub](https://github.com/date-fns/date-fns/issues/987)

## 坚持现有的标准

v2 始于文件名命名方案的改变。我来自 Ruby 世界，所以我认为使用下划线格式的文件名是个好主意。JavaScript 复兴刚刚开始，所以没有一个共同的标准。最初，看到如此多的 JavaScript 使用 camel case 格式的文件令人恼火。但最终，我接受了这种差异，并决定将普通实践置于个人品味之上，并采用了 camel case。

```
// v1
const addDays = require('date-fns/add_days')

// v2
const addDays = require('date-fns/addDays') 
```

这个突破性的变化是一个转折点，它让我们能够从我们的观点和习惯中抽象出来，拥抱现有的标准和成熟的实践。

最重要的变化之一是对`format`和`parse`采用 Unicode 技术标准#35。这引起了很多困惑，但我相信这是值得的。[你可以在一个专门的帖子](https://blog.date-fns.org/post/unicode-tokens-in-date-fns-v2-sreatyki91jg)中读到相关内容。

另一个让我们重新审视命名模式的标准是 ISO 8601。从`isWithinRange`函数开始，我们使用“范围”一词表示时间跨度:

```
const isWithinRange = require('date-fns/is_within_range')
isWithinRange(
  new Date(2014, 0, 3), // the date to check
  new Date(2014, 0, 1), // start
  new Date(2014, 0, 7) // end
)
//=> true 
```

原来，ISO 8601:2004 定义了术语“区间”:

> 时间间隔:时间轴的一部分，由两个瞬间限定

我们采用了这个术语，并将区间作为一个独立的实体:

```
import { isWithinInterval } from 'date-fns'
isWithinInterval(new Date(2014, 0, 3), {
  start: new Date(2014, 0, 1),
  end: new Date(2014, 0, 7)
}) 
```

这也使得代码更容易阅读！

当一个字符串被传递给`new Date()`时，JavaScript 引擎会尽力解析它。在 v1 中，我们依赖于这种机制，但是很快就发现不同的浏览器有不同的解析器，这导致了很难发现的错误。

从 v2 开始，每当一个字符串表示一个日期，它必须是一个有效的 ISO 8601 字符串，否则你将得到`Invalid Date`。

*感谢您的阅读！我希望这是有趣的，适合开发社区。欢迎反馈！*