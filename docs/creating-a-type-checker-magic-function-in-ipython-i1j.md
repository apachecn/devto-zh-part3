# 在 IPython 中创建类型检查器魔法函数

> 原文：<https://dev.to/switowski/creating-a-type-checker-magic-function-in-ipython-i1j>

## IPython 中的细胞魔法

在[之前的帖子](https://switowski.com/python/ipython/2019/02/01/creating-magic-functions-part1.html)中，我解释了什么是神奇的功能，以及为什么它们很酷。我们还创建了一个**线魔法**函数，解释用波兰符号写的数学公式。今天，我们将谈论**细胞魔法**的功能。

单元格魔术与线条魔术相似，只是它们作用于单元格(代码块)，而不是单行。IPython 附带了[一些预定义的](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cell-magics)，其中大部分将允许你解释用不同编程语言编写的代码。需要运行一些 Python 2 代码，但是 IPython 默认使用的是 Python 3？没问题，只需键入`%%python2`，粘贴/键入代码并运行:

```
In [1]: print 'hello there'
  File "<ipython-input-1-202d533f5f80>", line 1
    print 'hello there'
                      ^
SyntaxError: Missing parentheses in call to 'print'. Did you mean print('hello there')?

# But! 
In [2]: %%python2
   ...: print 'hello there'
   ...:
   ...:
hello there 
```

Enter fullscreen mode Exit fullscreen mode

你也可以运行用 [Ruby](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-ruby) 、 [Bash](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-bash) 、 [JavaScript](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-javascript) 和其他语言编写的代码。并且那些不同的代码块可以相互交互，例如，[你可以运行一些 JavaScript 代码，并将变量发送回 Python](https://michhar.github.io/javascript-and-python-have-a-party/) 。

## 编写一个单元格魔术函数

现在，让我们试着编写自己的细胞魔法函数。我最初想继续使用本系列第一部分中的波兰符号的例子。所以我开始编写一个函数，将代码块中的所有数学运算转换成波兰符号形式。不幸的是，我很快意识到，如果我想写一个好的例子(而不是一些只对`+`和`-`有效的半吊子代码)，我必须写一个合适的解释器。而这就不再是一个简单的例子 <sup id="fnref1">[1](#fn1)</sup> 。所以这一次，我们要做一些不同的事情。

Python 3.5 版本中的一个新特性是**类型提示**。有些人喜欢它们，有些人不喜欢(对于*的每一个*新特性、*的每一个*编程语言来说大概都是如此)。Python 类型提示的好处是它们不是强制性的。如果你不喜欢它们，就不要使用它们。对于快速原型或您自己维护的项目，没有它们您可能也很好。但是对于一个大型的代码库来说，大量的遗留代码由多个开发人员维护——类型提示非常有用！

正如您可能已经开始猜测的那样，我们的 cell magic 函数将检查代码块的类型。为什么？嗯，有了 IPython，你可以快速地制作一些代码的原型，使用 [%save](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-save) 或 [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile) 神奇的函数对其进行调整并保存到一个文件中(或者简单地复制并粘贴它，如果这对你来说更快的话)。但是，在撰写本文时，Python 中还没有内置的类型检查器。 [mypy](http://mypy-lang.org/) 库是一个*事实上的*静态类型检查器，但是它仍然是一个从 shell ( `mypy filename.py`)运行的外部工具。因此，让我们制作一个助手，允许我们直接在 IPython 中对 Python 代码进行类型检查！

这就是我们期望的工作方式:

```
In [1]: %%mypy
   ...: def greet(name: str) -> str:
   ...: return f"hello {name}"
   ...: greet(1)
   ...:
   ...:
Out[1]: # It should print an error message, as 1 is not a string 
```

Enter fullscreen mode Exit fullscreen mode

为了实现这一点，我们将简单地从`mypy.api`调用`run`函数(如[文档](https://mypy.readthedocs.io/en/latest/extending_mypy.html#integrating-mypy-into-another-python-application)中所建议的)，并传递`-c PROGRAM_TEXT`参数，让[检查一个字符串](https://mypy.readthedocs.io/en/latest/command_line.html#specifying-what-to-type-check)。

下面是类型检查器的代码:

```
from IPython.core.magic import register_cell_magic

@register_cell_magic('mypy')
def typechecker(line, cell):
    try:
        from mypy.api import run
    except ImportError:
        return "'mypy' not installed. Did you run 'pip install mypy'?"

    args = []
    if line:
        args = line.split()

    result = run(['-c', cell, *args])

    if result[0]:
        print('\nType checking report:\n')
        print(result[0]) # stdout 
    if result[1]:
        print('\nError report:\n')
        print(result[1]) # stderr 
    # Return the mypy exit status
    return result[2] 
```

Enter fullscreen mode Exit fullscreen mode

让我们浏览一下代码，因为有几个有趣的部分:

```
@register_cell_magic(mypy)
def typechecker(line, cell): 
```

Enter fullscreen mode Exit fullscreen mode

我们首先定义一个名为`typechecker`的函数，并将其注册为一个名为`%%mypy`的单元格魔术函数。为什么我不直接定义一个名为`mypy`的函数来代替重命名呢？嗯，如果我那样做了，那么**我们的** `mypy`函数将[隐藏](https://en.wikipedia.org/wiki/Variable_shadowing#Python)模块`mypy`。这种情况下，大概不会造成什么问题。但总的来说，你应该避免隐藏变量/函数/模块，因为总有一天，它会让你非常头疼。

```
try:
    from mypy.api import run
except ImportError:
    return "`mypy` not found. Did you forget to run `pip install mypy`?" 
```

Enter fullscreen mode Exit fullscreen mode

在我们的函数内部，我们首先尝试导入`mypy`模块。如果它不可用，我们会通知用户应该安装它，然后才能使用这个神奇的功能。在`typechecker`函数中导入`mypy`的好处是，只有当你运行这个神奇的函数时，导入错误才会出现。如果您将导入放在文件的顶部，然后将文件保存在 IPython 启动目录中，并且您**没有**安装`mypy`模块，您将在每次启动 IPython 时获得`ImportError`。这种方法的缺点是每次运行`typechecker`函数时都在运行导入代码。如果你关心性能，这是你应该避免做的事情，但是在我们的小助手的情况下，这不是一个大问题。

如果您使用的是 Python 3.6 或更高版本，您可以捕捉到`ModuleNotFoundError`错误，而不是`ImportError`。`ModuleNotFoundError`是`ImportError`的一个新子类[，当一个模块找不到](https://docs.python.org/3/library/exceptions.html#ModuleNotFoundError)时抛出。我想让我的代码与 Python 3 的较低版本兼容，所以我将坚持使用`ImportError`。

```
args = []
if line:
    args = line.split()

result = run(['-c', cell, *args]) 
```

Enter fullscreen mode Exit fullscreen mode

注意，用于定义单元格魔术的函数必须同时接受`line`和`cell`参数。这很好，因为这样，我们实际上可以将参数传递给`mypy`！所以在这里，我们从`line`参数向`run`函数传递额外的参数。下面是如何用不同的设置运行我们的神奇功能:

```
In [1]: %%mypy --ignore-missing-imports --follow-imports error
   ...: CODEBLOCK 
```

Enter fullscreen mode Exit fullscreen mode

这相当于在命令行中运行下面的命令:`mypy --ignore-missing-imports --follow-imports error -c 'CODEBLOCK'`。

代码的其余部分与文档中的[示例非常相似。](https://mypy.readthedocs.io/en/latest/extending_mypy.html#integrating-mypy-into-another-python-application)

## 测试时间！

我们的细胞魔法功能准备好了。我们先保存在 IPython 启动目录([IPython 启动目录是什么？](https://switowski.com/python/ipython/2019/01/04/ipython-startup-files.html))，所以下次我们启动 IPython 的时候就可以用了。在我的例子中，我把它保存在一个名为
的文件中

```
~/.ipython/profile_default/startup/magic_functions.py 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们启动 IPython，看看它是否工作:

```
In [1]: %%mypy
   ...: def greet(name: str) -> str:
   ...: return f"hello {name}"
   ...: greet('Bob')
   ...:
   ...:
Out[1]: 0

In [2]: %%mypy
   ...: def greet(name: str) -> str:
   ...: return f"hello {name}"
   ...: greet(1)
   ...:
   ...:

Type checking report:

<string>:3: error: Argument 1 to "greet" has incompatible type "int"; expected "str"

Out[2]: 1 
```

Enter fullscreen mode Exit fullscreen mode

太好了，成功了！如果一切正常，它将返回 0(这是成功命令的标准 UNIX 退出代码)。否则，它会报告发现了什么问题。

传递一些附加参数怎么样？

```
In [3]: %%mypy
   ...: import flask
   ...:
   ...:

Type checking report:

<string>:1: error: No library stub file for module 'flask'
<string>:1: note: (Stub files are from https://github.com/python/typeshed)

Out[3]: 1

# Ok, this can happen (https://mypy.readthedocs.io/en/latest/running_mypy.html#ignore-missing-imports)
# Let's ignore this error 
In [4]: %%mypy --ignore-missing-imports
   ...: import flask
   ...:
   ...:
Out[4]: 0 
```

Enter fullscreen mode Exit fullscreen mode

传递附加参数也可以！

很好，我们创建了一个很好的小助手函数，可以用来检查给定代码块中的类型提示是否正确。

## 线条和单元格的神奇功能

还有一个装饰者我们还没有讨论:`@register_line_cell_magic`。这没什么特别的——尤其是现在你已经知道了线魔法和细胞魔法是如何工作的——所以没有必要单独写一篇文章。 [IPython 文档](https://ipython.readthedocs.io/en/stable/config/custommagics.html#defining-custom-magics)很好地解释了这个装饰器:

```
@register_line_cell_magic
def lcmagic(line, cell=None):
    "Magic that works both as %lcmagic and as %%lcmagic"
    if cell is None:
        print("Called as line magic")
        return line
    else:
        print("Called as cell magic")
        return line, cell 
```

Enter fullscreen mode Exit fullscreen mode

如果您运行`%lcmagic`，这个函数将不会收到`cell`参数，它将作为一个行魔术。如果您运行`%%lcmagic`，它将接收`cell`参数和可选的`line`参数(就像我们上一个使用`%%mypy`的例子)。因此，您可以检查`cell`参数的存在，并基于此，控制它是否应该充当线条或单元格魔术。

## 结论

现在你知道如何制作一个**线魔法**和一个**细胞魔法**功能，以及如何将它们组合成一个**线魔法**功能。IPython 还提供了一个特性——Magics 类。它允许您编写更强大的神奇函数，因为它们可以在调用之间保存状态。所以请继续关注本文的最后一部分！

图片来自:[像素](https://www.pexels.com/photo/creativity-magic-paper-text-6727/)

* * *

1.  写翻译器还是一个很棒的练习！我最近关注了[让我们构建一个简单的解释器](https://ruslanspivak.com/lsbasi-part1/)系列，在那里你可以用 Python 构建一个 Pascal 解释器，对于从未学习过编译器的人来说，这是一个非常有趣的项目。所以，如果你对这种类型的挑战感兴趣，这个博客可以帮助你开始。 [↩](#fnref1)