# Python 4 -也许是新的函数语法？

> 原文：<https://dev.to/ablablalbalblab/python-4-new-function-syntax-maybe-28mn>

## 简介

嘿！我是 xMikee，一个为了方便而写 Python 模块的三流开发者。最近，我一直在研究 Python 语法，并将其与其他语言的语法进行比较。

最近，我一直在使用 JavaScript 来完成我的网络项目。我在使用哪种语言之间来回切换(显然是为了不同的用例)，这确实有助于我理解两种语言之间的差异。一些值得注意的例子:

*   大括号——Python 非常依赖缩进

*   `super`(JS 中没有函数调用，Python 中有函数)

*   `this`(指 JS 中某物所属的对象)

*   `get`/`set`(JS 中的特殊语法，Python 中的 decorators 除外)

*   扩展语法(基本上相当于 Python 中的`*variable`)

*   字典(JS 中的键不需要引号，Python 则相反)

可能还有更多我忘记了。现在很明显，这些语言没有共享相同的原则，因此语法会非常不同(JS 是 C 风格的，但 Python 不是)。但是我想在 Python 中加入 JavaScript 的某些方面。什么事？别大惊小怪，我来解释。

* * *

让我开始说 Python 函数现在的样子绝对没有任何问题。我并不想去那里提议我们以任何方式给语言添加括号。如果这项提议被否决，那就再好不过了。我想尝试用函数作为参数来解决可读性问题。不太明白我在说什么？这里有一个例子。

假设您正在使用 JavaScript 中的 Fetch API 执行一个 web 请求。下面是您的示例代码可能的样子:

```
fetch('http://example.com/movies.json')
  .then(function(response) {
    return response.json();
  })
  .then(function(myJson) {
    console.log(JSON.stringify(myJson));
  }); 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们把这个程序翻译成伪 Python(显然`fetch`不是 Python 函数，但我们只是翻译):

```
import json

def handler1(response):
  return response.json()

def handler2(myJson):
  print(json.dumps(myJson))

fetch('http://example.com/movies.json')
  .then(handler1)
  .then(handler2) 
```

Enter fullscreen mode Exit fullscreen mode

不太漂亮，是吗？让我们在这里指出不同之处。

*   在 JavaScript 中，我们可以内联定义函数。这增加了可读性，因为我们可以清楚地看到正在处理的函数之间的联系，以及它如何适应程序流。

*   在 Python 中，我们必须为两个处理程序定义单独的函数。由于 Python 不直接支持内联函数(lambda 表达式除外——我们将在后面介绍),我们必须定义单独的函数作为处理程序传入。在一个复杂得多的程序中，这可能会很快失去控制，并且不清楚在哪里执行什么功能。

这似乎违背了 Python 的一些核心价值观，这些价值观在[Python 的禅宗](https://www.python.org/dev/peps/pep-0020/#the-zen-of-python)中有所定义:

> 简单比复杂好。
> ...
> 可读性很重要。

很有趣，对吧？现在，让我们回到λ表达式。Lambdas 是 Python - **而非**函数中的可调用表达式。lambda 适合传入一个表达式的方法(例如，我们上面的例子可能被转换成两个 lambda)。但这几乎是他们唯一擅长的。

一旦在函数或语句中开始需要不止一个表达式，就需要切换到函数。这是因为 lambdas 是*表达式*，因此只接受表达式语法，不接受语句。您只能使用一个完整表达式的事实严重限制了 lambda 表达式在具有更复杂操作的大规模编程中的能力。

因此，我们在大多数情况下都需要使用函数。正如我们在上面看到的，仅仅为了将它们用作参数而单独定义它们是很难看的。这个问题真的没有解决方案——至少现在还没有。

* * *

## 我的提议

<small>*我提前道歉。语法突出显示确实显示了一些暗红色/黑色文本，因为建议的功能在技术上是无效的语法。尽量忽略明显的语法错误。*T3】</small>

由于 Python 中严格的缩进要求，很难实现一个真正的内联函数系统，同时又有一定的吸引力和实用性。我相信我已经在所有这些选项之间找到了一个折中的办法来创建一个有吸引力的、有用的语法。下面是上面的例子，只是使用了新的语法:

```
import json

fetch('http://example.com/movies.json')
  .then((def (response):
    return response.json()
  ))
  .then((def (myJson):
    print(json.dumps(myJson))
  )) 
```

Enter fullscreen mode Exit fullscreen mode

让我们把这些线分开。

```
.then((def (response):
  return response.json()
)) 
```

Enter fullscreen mode Exit fullscreen mode

*   整个函数都用括号括起来。如果直接将其作为函数传递，以允许其他参数与内联函数一起传递，则这是必需的。

*   `def`语句后面没有名字——参数立即开始。

*   缩进仍然是必需的和强制性的(如果我必须这么说的话，它实际上非常适合这个论点)。

*   右括号结束内联函数。此时可以向函数中添加更多的参数。

不算太寒酸吧？这就是我的建议。这叫做内联函数语法。与 lambda 表达式不同，内联函数完全能够执行常规函数可能需要的任何任务。

这里有另一个例子供你参考。它使用`map`功能。以前，为了编写更复杂的操作，你必须使用一个完整的独立函数，而不是 lambda，这是不实用的。但是现在，这里是函数*的内嵌* :

```
result = map(range(60), (def (number):
  if number % 2 == 0:
    return "even"
  elif number % 2 == 1:
    return "odd"
  else:
    return "whoa, freaky number"
)) 
```

Enter fullscreen mode Exit fullscreen mode

你看到了吗？它甚至看起来有些吸引人！我们可以传入`range(60)`作为第一个参数，如果我们愿意，我们甚至可以在函数后面传入更多的参数，就像这样:

```
result = map(range(60), (def (number):
  if number % 2 == 0:
    return "even"
  elif number % 2 == 1:
    return "odd"
  else:
    return "whoa, freaky number"
), another_value) 
```

Enter fullscreen mode Exit fullscreen mode

当然，内联函数是对象。这意味着它们可以被赋给变量。这是另一个使用内联函数的例子，但是这次把它赋给了一个变量:

```
freaky = def (number):
  if number % 2 == 0:
    return "even"
  elif number % 2 == 1:
    return "odd"
  else:
    return "whoa, freaky number"

result = map(range(60), freaky) 
```

Enter fullscreen mode Exit fullscreen mode

这基本上是函数创建的另一种语法。因为简单来说内联函数就是函数，所以它们支持[类型提示/函数注释](https://stackoverflow.com/a/32558710/) :
并不奇怪

```
from typing import Iterator

fib = def (n: int) -> Iterator[int]:
  a, b = 0, 1
  while a < n:
    yield a
    a, b = b, a+b

result = fib(60) 
```

Enter fullscreen mode Exit fullscreen mode

由于保留了缩进，所以在将内联函数作为函数参数传递时，也支持类型提示。

还有一个更特殊的*问题*，它直接受到 JavaScript 的启发(甚至超过了本文中的其他语法)。它叫做立即调用函数表达式。这是一个直接的 JavaScript 特性([阅读 MDN](https://developer.mozilla.org/en-US/docs/Glossary/IIFE) 上的内容)。您可以使用它来生成私有范围的变量以执行计算。而不在之后存储该功能。

这里有一个例子。假设我们想要生成前 60 个斐波那契数，但是我们希望所有的变量都是私有的，并且我们不希望函数在之后被存储。这里有一个例子:

```
result = (def (n):
  a, b = 0, 1
  while a < n:
    yield a
    a, b = b, a+b
)(60) 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的是，函数**必须用括号**括起来，这样函数和调用才能分开。这在某些情况下是有用的。

* * *

如果我们在这里诚实地说，我不认为这将*永远*实现，一点也不。但这只是我提议的另一件事，来增加语言的趣味。:)

当然，这也会带来它自己的许多不兼容问题。但这就是任何有趣的语法变化所带来的结果。

让我们听听你的想法。你对函数有什么看法？Python 社区会从这样的改变中受益吗，还是会损害这种语言？感谢你的阅读，这里有一只猫，因为你一直读到最后。😺