# 在 IPython 中创建神奇的函数——第 1 部分

> 原文：<https://dev.to/switowski/creating-magic-functions-in-ipython---part-1-387j>

## IPython 魔法函数

IPython 的一个很酷的特性是[魔法函数](https://ipython.readthedocs.io/en/stable/interactive/magics.html)——内置在 IPython 中的助手函数。他们可以帮助你轻松地[启动一个交互式调试器](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-debug)、[创建一个宏](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-save)、[通过代码分析器](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-prun)运行一条语句或者[测量它的执行时间](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)以及做许多更常见的事情。

更好的是，您可以创建自己的神奇功能。有两种不同类型的魔法功能。

第一种叫做**线魔法**——以`%`为前缀，工作起来就像在你的终端中输入一个命令。您以函数名开始，然后传递一些参数，例如:

```
In [1]: %timeit range(1000)
255 ns ± 10.3 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each) 
```

Enter fullscreen mode Exit fullscreen mode

我最喜欢的是 [%debug](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-debug) 函数。假设你运行一些代码，它抛出一个异常。但是由于您没有为异常做好准备，您没有通过调试器运行它。现在，为了能够调试它，你通常会返回，设置一些断点，然后重新运行相同的代码。幸运的是，如果您正在使用 IPython，有一个更好的方法！您可以在异常发生后立即运行`%debug`, IPython 将为该异常启动一个交互式调试器。它叫做*事后调试*，我绝对喜欢它！

第二种魔法功能是**细胞魔法**，它们作用于一段代码，而不是一行代码。它们以`%%`为前缀。要关闭一个代码块，当你在一个单元格魔术函数中时，按两次`Enter`。下面是一个在代码块上工作的`timeit`函数的例子:

```
In [2]: %%timeit elements = range(1000)
   ...: x = min(elements)
   ...: y = max(elements)
   ...:
   ...:
52.8 µs ± 4.37 µs per loop (mean ± std. dev. of 7 runs, 10000 loops each) 
```

Enter fullscreen mode Exit fullscreen mode

线条魔术和单元格魔术都可以通过简单地修饰 Python 函数来创建。另一种方法是编写一个继承自`IPython.core.magic.Magics`的类。我将在另一篇文章中介绍第二种方法。

## 创建线条魔术功能

理论上就是这样。现在，让我们编写第一个神奇的函数。我们将从一个`line magic`开始，在本教程的第二部分，我们将制作一个`cell magic`。

我们要创造什么样的神奇功能？好吧，让我们做点有用的东西。我来自波兰，在波兰我们使用[波兰语符号](https://en.wikipedia.org/wiki/Polish_notation)来写下数学运算。所以我们不写`2 + 3`，而是写`+ 2 3`。我们没有写`(5 − 6) * 7`，而是写`* − 5 6 7` <sup id="fnref1">[1](#fn1)</sup> 。

让我们编写一个简单的波兰符号解释器。它将接受波兰符号的表达式作为输入，并输出正确的答案。为了使这个例子简短，我将只把它限制在基本的算术运算上:`+`、`-`、`*`和`/`。

下面是解释波兰符号的代码:

```
def interpret(tokens):
    token = tokens.popleft()
    if token == "+":
        return interpret(tokens) + interpret(tokens)
    elif token == "-":
        return interpret(tokens) - interpret(tokens)
    elif token == "*":
        return interpret(tokens) * interpret(tokens)
    elif token == "/":
        return interpret(tokens) / interpret(tokens)
    else:
        return int(token) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将创建一个`%pn`魔法函数，它将使用上面的代码来解释波兰语符号。

```
from collections import deque

from IPython.core.magic import register_line_magic

@register_line_magic
def pn(line):
    """Polish Notation interpreter

    Usage:
    >>> %pn + 2 2
    4
    """
    return interpret(deque(line.split())) 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。`@register_line_magic`装饰器将我们的`pn`函数变成了一个`%pn`魔法函数。`line`参数包含传递给神奇函数的任何内容。如果我们这样称呼它:`%pn + 2 2`，`line`会包含`+ 2 2`。

为了确保 IPython 在启动时加载我们的神奇功能，将我们刚刚编写的所有代码(你可以在 GitHub 上找到整个文件[)复制到 IPython 启动目录中的一个文件中。你可以在](https://github.com/switowski/blog-resources/blob/master/ipython-magic-functions/magic_functions.py) [IPython 启动文件帖子](https://switowski.com/python/ipython/2019/01/04/ipython-startup-files.html)中了解更多关于这个目录的信息。在我的例子中，我将它保存在一个名为:`~/.ipython/profile_default/startup/magic_functions.py`的文件中(文件名并不重要，但放在哪个目录中很重要)。

好了，考验的时候到了。启动 IPython，让我们做一些*润色*数学:

```
In [1]: %pn + 2 2
Out[1]: 4

In [2]: %pn * - 5 6 7
Out[2]: -7 

In [3]: %pn * + 5 6 + 7 8
Out[3]: 165 
```

Enter fullscreen mode Exit fullscreen mode

完美，成功了！当然，它是非常初级的——它只支持 4 个操作符，它不能很好地处理异常，而且因为它使用了递归，所以对于很长的表达式它可能会失败。此外，`queue`模块和`interpret`函数现在可以在您的 IPython 会话中使用，因为您放入`magic_function.py`文件中的任何代码都将在 IPython 启动时运行。

但是，你刚刚写了你的第一个魔法函数！而且也没那么难！

此时，您可能会奇怪- *为什么我们不写一个标准的 Python 函数呢？*这是个好问题——在这种情况下，我们可以简单地运行下面的代码:

```
In [1]: pn('+ 2 2')
Out[1]: 4 
```

Enter fullscreen mode Exit fullscreen mode

甚至:

```
In [1]: interpret(deque('+ 2 2'.split()))
Out[1]: 4 
```

Enter fullscreen mode Exit fullscreen mode

我在开头说过，魔法函数通常是辅助函数。它们的主要优点是，当有人看到带有`%`前缀的函数时，很明显这是来自 IPython 的神奇函数，而不是在代码中某处定义的函数或内置的函数。此外，它们的名称不会与 Python 模块中的函数发生冲突。

我希望你喜欢这个简短的教程，如果你有问题，或者如果你有一个很酷的魔法功能，你想分享-给我发电子邮件或者在评论中告诉我！

请继续关注下一部分。我们仍然需要涵盖**细胞魔法**函数、**线和细胞魔法**函数和**魔法**类。

图片来自:[像素](https://www.pexels.com/photo/actor-adult-business-cards-547593/)

* * *

1.  这是个笑话。在波兰我们不使用*波兰符号*；). [↩](#fnref1)