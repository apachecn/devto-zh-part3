# 在 IPython 中创建神奇的函数——第 3 部分

> 原文：<https://dev.to/switowski/creating-magic-functions-in-ipython---part-3-41p>

## 魔法类

到目前为止，在这个系列中，我们已经介绍了三种不同的装饰者:`@register_line_magic`(在[第一部分](https://dev.to/switowski/creating-magic-functions-in-ipython---part-1-387j))、`@register_cell_magic`和`@register_line_cell_magic`(在[第二部分](https://dev.to/switowski/creating-a-type-checker-magic-function-in-ipython-i1j))。这足以在 IPython 中创建任何类型的神奇函数。但是，IPython 提供了另一种创建它们的方法——创建一个 **Magics** 类，并在其中定义魔法函数。

魔法类比函数更强大，就像类比函数更强大一样。它们可以保存函数调用之间的状态，封装函数，或者提供继承。要创建一个 Magics 类，您需要三样东西:

*   您的类需要从`Magics`继承
*   你的班级需要用`@magics_class`来装饰
*   您需要使用`ipython.register_magics(MyMagicClass)`函数注册您的魔法类

在你的魔法类中，你可以用`@line_magic`、`@cell_magic`和`@line_cell_magic`来修饰你想要转换成魔法函数的函数，

## 写一堂魔术课

为了展示 magics 类是如何工作的，我们将创建另一个版本的`mypy` helper。这一次，它将允许我们对以前的单元格进行类型检查。这就是我们期望的工作方式:

```
In [1]: def greet(name: str) -> str:
   ...: return f"hello {name}"

In [2]: greet('tom')
Out[2]: 'hello tom'

In [3]: greet(1)
Out[3]: 'hello 1'

In [4]: %mypy 1-2
Out[4]: # Everything should be fine 
In [4]: %mypy 1-3
Out[4]: # It should report a problem on cell 3 
```

Enter fullscreen mode Exit fullscreen mode

以下是关于`%mypy`函数的一些假设:

*   它应该接受`mypy`命令接受的所有参数
*   它应该接受与`%history`命令接受的相同的范围参数，但**仅来自当前会话**。无论如何，我通常不引用以前的历史，这将使解析论点稍微容易一些。所以`1`、`1-5`和`1 2 4-5`都是有效的参数，而`243/1-5`或`~8/1-~6/5`不是。
*   参数的顺序并不重要(您甚至可以将范围与`mypy`参数混合)，因此我们可以通过以下方式调用我们的函数:
    *   `%mypy --ignore-imports 1 2 5-7`
    *   `%mypy 1-3`
    *   `%mypy 2 4 5-9 --ignore-imports`
    *   `%mypy 2 4 --ignore-imports 5-9`

记住这一点，让我们写代码。主类是这样的:

```
from IPython.core.magic import Magics, magics_class, line_magic
import re

# The class MUST call this class decorator at creation time @magics_class
class MypyMagics(Magics):
    @line_magic
    def mypy(self, line):
        try:
            from mypy.api import run
        except ImportError:
            return "'mypy' not installed. Did you run 'pip install mypy'?"

        if not line:
            return "You need to specify cell range, e.g. '1', '1 2' or '1-5'."

        args = line.split()
        # Parse parameters and separate mypy arguments from cell numbers/ranges
        mypy_arguments = []
        cell_numbers = []
        for arg in args:
            if re.fullmatch(r"\d+(-\d*)?", arg):
                # We matched either "1" or "1-2", so it's a cell number
                cell_numbers.append(arg)
            else:
                mypy_arguments.append(arg)

        # Get commands from a given range of history
        range_string = " ".join(cell_numbers)
        commands = _get_history(range_string)

        # Run mypy on that commands
        print("Running type checks on:")
        print(commands)

        result = run(["-c", commands, *mypy_arguments])

        if result[0]:
            print("\nType checking report:\n")
            print(result[0]) # stdout 
        if result[1]:
            print("\nError report:\n")
            print(result[1]) # stderr 
        # Return the mypy exit status
        return result[2]

ip = get_ipython()
ip.register_magics(MypyMagics) 
```

Enter fullscreen mode Exit fullscreen mode

我们有`MypyMagics class`(从`Magics`继承而来)并且在其中，我们有`mypy`线魔法做以下事情:

*   检查是否安装了`mypy`
*   如果没有传递参数，它会返回一个关于如何正确使用它的简短信息。
*   解析参数，并从单元格编号/范围中拆分出用于`mypy`的参数。由于`mypy`不接受看起来像数字(`1`)或数字范围(`1-2`)的参数，我们可以安全地假设所有匹配这两种模式之一的参数都是单元格。
*   使用`_get_history`助手(下面解释)作为字符串从单元格中检索输入值，并将该字符串打印到屏幕上，这样您就可以看到将检查哪些代码。
*   运行`mypy`命令，打印报告并返回退出代码。

最后，我们需要记住在 IPython 中注册`MypyMagics`类。

我们正在使用一个助手函数:

```
def _get_history(range_string):
    ip = get_ipython()
    history = ip.history_manager.get_range_by_str(range_string)
    # history contains tuples with the following values:
    # (session_number, line_number, input value of that line)
    # We only need the input values concatenated into one string,
    # with trailing whitespaces removed from each line
    return "\n".join([value.rstrip() for _, _, value in history]) 
```

Enter fullscreen mode Exit fullscreen mode

我之前告诉过你，当写一个类的时候，我们可以把我们的助手函数放在里面，但是我有目的地把这个函数放在`MypyMagics`之外。它是一个简单的助手，可以在没有任何关于我们类的知识的情况下使用，所以它并不真正属于它。所以，我把它放在外面，并使用[命名约定](https://stackoverflow.com/questions/1301346/what-is-the-meaning-of-a-single-and-a-double-underscore-before-an-object-name)来暗示它是一个私有函数。

提出`_get_history`助手是一件非常棘手的事情，所以让我们详细讨论一下。

### 方法 1: `_ih`

我需要从 IPython 中检索前面的命令，我知道 IPython 将它们存储在`_ih`列表中(因此，如果您想要检索当前会话中的第一个命令，您只需运行`_ih[1]`)。这听起来很容易，但是需要一些预处理。我首先必须将`1-2`类型的范围转换成列表片段。然后我必须一个接一个地检索历史的所有部分，所以对于`1 2-3 5`，我需要调用`_ih[1]`、`_ih[2:4]`、`_ih[5]`。这是可行的，但我想要一个更简单的方法。

### 方法二:`%history`

我的下一个想法是重用`%history`魔法函数。虽然你不能仅仅用 Python 代码写`%history`并期望它能工作，但是[有一种不同的方式来调用 magics 作为标准函数](https://stackoverflow.com/questions/10361206/how-to-run-an-ipython-magic-from-a-script-or-timing-a-python-script)——我不得不使用`get_ipython().magic(<magic_function_name>)`函数。

问题解决了！除了`%history` magic 既可以将输出打印到终端，也可以保存在文件中。没有办法说服它去*还*我们一个字符串。真扫兴。我可以用以下两种方法之一来解决这个问题:

*   因为默认情况下`%history`会写入`sys.stdout`，所以我可以对`sys.stdout`打猴子补丁(改变运行时的行为),让它将`history`输出的内容保存在一个变量中。猴子补丁通常不是最好的主意，我不想在我的代码中引入不好的实践，所以我不喜欢这个解决方案。
*   否则，我可以将`%history`的输出保存到一个文件中，然后从该文件中读取它。但是在文件系统上创建文件只是为了在里面写些东西并立即读回来，这听起来很可怕。我需要考虑在哪里创建文件，文件是否已经存在，然后记得删除它。即使有了能够为我处理临时文件的创建和删除的 [tempfile](https://docs.python.org/3.7/library/tempfile.html#examples) 模块，对于一个简单的例子来说，这感觉太多了。

所以`%history`功能是不可行的。

### 方法三:`HistoryManager`

最后，我决定在`%history`内部达到顶点，并使用该函数在幕后使用的任何东西——来自`IPython.core.history`模块的[历史管理器](https://ipython.readthedocs.io/en/stable/api/generated/IPython.core.history.html#IPython.core.history.HistoryManager)。`HistoryManager.get_range_by_str()`接受与`%history`函数相同的字符串格式，所以不需要预处理。那正是我所需要的！我只需要稍微清理一下输出(从元组中检索正确的信息)就可以了。

## 测试时间！

现在，我们的`%mypy`助手已经完成(整个文件在 GitHub 上是[)并保存在 IPython](https://github.com/switowski/blog-resources/blob/master/ipython-magic-functions/magic_functions3.py) [启动目录](///python/ipython/2019/01/04/ipython-startup-files.html)中，让我们测试一下:

```
In [1]: def greet(name: str) -> str:
   ...: return f"hello {name}"
   ...:

In [2]: greet('Bob')
Out[2]: 'hello Bob'

In [3]: greet(1)
Out[3]: 'hello 1'

In [4]: %mypy 1-3 # this is equivalent to `%mypy 1 2 3` Running type checks on:
def greet(name: str) -> str:
    return f"hello {name}"
greet('Bob')
greet(1)

Type checking report:

<string>:4: error: Argument 1 to "greet" has incompatible type "int"; expected "str"

Out[4]: 1

# What about passing parameters to mypy? In [5]: import Flask

In [6]: %mypy 5
Running type checks on:
import flask

Type checking report:

<string>:1: error: No library stub file for module 'flask'
<string>:1: note: (Stub files are from https://github.com/python/typeshed)

Out[6]: 1

In [7]: %mypy 5 --ignore-missing-imports
Running type checks on:
import flask
Out[7]: 0 
```

Enter fullscreen mode Exit fullscreen mode

完美，它的工作完全符合预期！现在您有了一个助手，可以直接在 IPython 中检查代码的类型。

只有一件事可以让它变得更好——一个**自动**类型检查器，一旦在 IPython 中被激活，它会在您执行代码时自动对代码进行类型检查。但那是另一篇文章的内容了。

## 结论

这就是我们关于 IPython 神奇函数的短暂旅程的结束。如你所见，它们没有什么神奇的地方，只需要添加一个装饰器或者从一个特定的类继承。神奇的功能可以进一步扩展 IPython 已经令人惊叹的功能。所以，如果你发现自己在一遍又一遍地做某件事，不要犹豫，创造你自己的。例如，当我和 [SQLAlchemy](https://www.sqlalchemy.org/) 一起工作的时候，我制作了一个神奇的函数[将 SQLAlchemy 行对象转换成 Python 字典](https://stackoverflow.com/a/1960546)。除了以一种很好的方式呈现结果之外，它没做多少事情，但是男孩，当玩数据的时候，那是多么方便啊！

你知道你喜欢并愿意与他人分享的很酷的魔法功能吗？如果是这样，请在评论中发布它们！

图片来自:[坎瓦](https://www.canva.com/photos/misc/MACZWLaSh7E-magic-conjure-conjurer-cylinder-magic-hat-wand/)