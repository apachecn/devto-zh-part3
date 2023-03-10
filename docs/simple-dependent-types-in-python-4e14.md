# Python 中的简单依赖类型

> 原文：<https://dev.to/wemake-services/simple-dependent-types-in-python-4e14>

**原载于我的博客**:[https://sobolevn . me/2019/01/simple-dependent-types-in-python](https://sobolevn.me/2019/01/simple-dependent-types-in-python)

我对`python`中的这个新特性非常兴奋:简单依赖类型。“依赖类型”听起来可能很复杂，但事实并非如此。相反，它是一个有用的特性，我将展示它是如何工作的，以及何时应该依赖它。

我们不会深究这个理论，我也不会在这里提供任何形式的数学公式。正如史蒂芬·霍金曾经说过的:

> 有人告诉我，我在书中加入的每个等式都会让销售额减半。因此，我决定不要任何方程式。然而，最后我还是加入了一个方程，爱因斯坦著名的方程，E=mc^2.方程我希望这不会吓跑我一半的潜在读者

什么是依赖类型？当你依赖于某些类型的值，而不仅仅是原始类型时，这是一个概念。

考虑这个例子:

```
from typing import Union

def return_int_or_str(flag: bool) -> Union[str, int]:
    if flag:
        return 'I am a string!'
    return 0 
```

我们可以清楚地看到，根据`flag`的值，我们可以得到`str`或`int`的值。结果类型将是`Union[str, int]`。每次我们用 mixed-up-return-type 调用这个函数时，我们必须检查我们实际得到的是什么类型，以及如何处理它。这很不方便，并且会使您的代码更加复杂。

你可能会说这个函数不好，它不应该像现在这样运行。正确，但在一些真实的使用案例中，这是设计所要求的。

考虑来自标准库的 [`open`函数。你多久会因为混淆了`str`和`bytes`而出现一次运行时错误？它在我身上发生了一千次。我不希望这种事再次发生！因此，这次我们将编写类型安全的代码。](https://docs.python.org/3/library/functions.html#open) 

```
def open_file(filename: str, mode: str):
    return open(filename, mode) 
```

这里我们期望什么样的返回类型？`str`？等一下！我们可以这样调用它:`open_file('some.txt', 'rb')`它会返回`bytes`！所以，返回的类型是 [`Union[IO[str], IO[bytes]]`](https://mypy.readthedocs.io/en/latest/cheat_sheet_py3.html#miscellaneous) 。和它一起工作真的很难。我们最终会有很多条件，不需要的演员和守卫。

依赖类型解决了这个问题。但是，在我们继续之前，我们必须知道一些我们以后会用到的原语。

## 文字和@重载

如果没有安装`mypy`和`typing_extensions`，需要安装这些包的最新版本。

```
» pip install mypy typing_extensions 
```

现在我们准备用`Literal`和`@overload` :
的力量重写我们的代码

```
from typing import overload
from typing_extension import Literal 
```

快速端**注** : `typing`是一个内置的`python`模块，其中定义了所有可能的类型。而且这个模块的开发速度仅限于新的`python`版本发布。而`typing_extensions`是一个新类型的官方包，将在`python`的未来版本中提供。因此，它确实解决了常规`typing`模块的发布速度和频率的所有问题。

### 字面意思

`Literal` type 代表特定类型的特定值。

```
from typing_extensions import Literal

def function(x: Literal[1]) -> Literal[1]:
     return x

function(1)
# => OK! 
function(2)
# => Argument has incompatible type "Literal[2]"; expected "Literal[1]" 
```

要运行这段代码，请使用:`mypy --python-version=3.6 --strict test.py`。本文中的所有例子都是一样的。

太棒了。但是，`Literal[0]`型和`int`型有什么区别呢？

```
from typing_extensions import Literal

def function(x: int = 0, y: Literal[0] = 0) -> int:
    reveal_type(x)
    # => Revealed type is 'builtins.int'
    reveal_type(y)
    # => Revealed type is 'Literal[0]'
    return x 
```

揭示的类型不同。获得`Literal`类型的唯一方法是注释为`Literal`。这样做是为了保存与旧版本`mypy`的向后兼容性，而不是将`x: int = 0`检测为`Literal`类型。因为`x`的值以后可以改变。

你可以在任何可以使用常规`int`的地方使用`Literal[0]`，但不能反过来。

```
from typing_extensions import Literal

def function(x: int, y: Literal[0]) -> int:
    return x

x1: int = 0
y1: Literal[0] = 0

function(y1, y1)
function(x1, x1)
# => Argument 2 has incompatible type "int"; expected "Literal[0]" 
```

看到了吗？由于`x1`是一个变量——它不能用在我们期望的地方`Literal` s.
在这个系列的第一部分，我写了一篇关于[在`python`](https://dev.to/wemake-services/1-minute-guide-to-real-constants-in-python-2bpk) 中使用实常数的文章。如果不知道`python`中变量和常量的区别，请阅读。

常量在这种情况下有用吗？是的，他们会的！

```
from typing_extensions import Literal, Final

def function(x: int = 0, y: Literal[0] = 0) -> int:
     return x

x: Final = 0
y: Literal[0] = 0

function(y, y)
function(x, x) 
```

如你所见，当声明某个值`Final`时，我们创建了一个常数。这是无法改变的。而且符合`Literal`是什么。这两种类型的源代码实现也非常相似。

为什么我在`python` simple 中不断的调用依赖类型？因为目前仅限于简单的数值:`int`、`str`、`bool`、`float`、`None`。它目前不能处理元组、列表、字典、自定义类型和类等。但是，您可以在这个线程中跟踪开发进度[。](https://github.com/python/mypy/issues/3062)

不要忘记官方文件。

### @过载

下一件我们需要的东西是室内装潢师。需要用不同的输入类型和结果定义多个函数声明。

想象一下，我们有这样一种情况，当我们需要写一个减少一个值的函数。它应该与`str`和`int`输入一起工作。当给定`str`时，它应该返回除最后一个字符之外的所有输入字符，但当给定`int`时，它应该返回前一个数字。

```
from typing import Union

def decrease(first: Union[str, int]) -> Union[str, int]:
    if isinstance(first, int):
        return first - 1
    return first[:-1]

reveal_type(decrease(1))
# => Revealed type is 'Union[builtins.str, builtins.int]' reveal_type(decrease('abc'))
# => Revealed type is 'Union[builtins.str, builtins.int]' 
```

不太实用吧？仍然不知道返回的具体类型。我们可以用`@overload`装饰器来增强输入。

```
from typing import Union, overload

@overload
def decrease(first: str) -> str:
    """Decreases a string."""

@overload
def decrease(first: int) -> int:
    """Decreases a number."""

def decrease(first: Union[str, int]) -> Union[str, int]:
    if isinstance(first, int):
        return first - 1
    return first[:-1]

reveal_type(decrease(1))
# => Revealed type is 'builtins.int' reveal_type(decrease('abc'))
# => Revealed type is 'builtins.str' 
```

在这种情况下，我们定义了几个函数头来给`mypy`足够的关于正在发生的事情的信息。而这些 head 函数只在这个模块进行类型检查的时候使用。正如你所看到的，只有一个函数定义实际上包含了一些逻辑。您可以根据需要创建任意数量的功能头。

这个想法是:每当`mypy`找到一个有多个`@overload`头的函数时，它会尝试将输入值与这些声明匹配。当找到第一个匹配时，它返回结果类型。

官方文档也可以帮助你理解如何在你的项目中使用它。

## 依赖类型

现在，我们将结合我们关于`Literal`和`@overload`的新知识来解决我们关于`open`的问题。终于来了！

记住，我们需要为`'rb'`模式返回`bytes`，为`'r'`模式返回`str`。
我们需要知道确切的返回类型。

一个算法将是:

1.  编写几个`@overload`装饰器来匹配所有可能的情况
2.  当我们期望得到`'r'`或`'rb'`时，编写`Literal[]`类型
3.  一般情况下编写函数逻辑

```
from typing import IO, Any, Union, overload
from typing_extensions import Literal

@overload
def open_file(filename: str, mode: Literal['r']) -> IO[str]:
    """When 'r' is supplied we return 'str'."""

@overload
def open_file(filename: str, mode: Literal['rb']) -> IO[bytes]:
    """When 'rb' is supplied we return 'bytes' instead of a 'str'."""

@overload
def open_file(filename: str, mode: str) -> IO[Any]:
    """Any other options might return Any-thing!."""

def open_file(filename: str, mode: str) -> IO[Any]:
    return open(filename, mode)

reveal_type(open_file('some.txt', 'r'))
# => Revealed type is 'typing.IO[builtins.str]' reveal_type(open_file('some.txt', 'rb'))
# => Revealed type is 'typing.IO[builtins.bytes]' reveal_type(open_file('some.txt', 'other'))
# => Revealed type is 'typing.IO[AnyStr]' 
```

这是什么？三个`@overload`装饰器和一个带逻辑的函数体。第一个`@overload` decorator 声明为`'r'` `Literal`参数返回`str`，第二个 decorator 告诉当我们使用`'rb'`参数时返回`bytes`。第三个是后退。每当我们提供另一个任何其他模式——我们都可以得到`str`和`bytes`。

现在，我们的问题解决了。我们给函数提供一些特定的值，我们得到一些特定的类型作为回报。这使得我们的代码更容易阅读，执行起来更安全。

感谢依赖类型在`python`中的工作方式！

## 结论

我希望这个小教程能帮助你更好地理解`python`中的输入。在以后的文章中，我将涉及关于`mypy`的更复杂的主题。
[在 Github](https://github.com/sobolevn) 上关注我，订阅我的博客和我的新开源项目。