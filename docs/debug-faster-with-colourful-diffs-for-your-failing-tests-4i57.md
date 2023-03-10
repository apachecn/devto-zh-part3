# 为失败的测试提供丰富多彩的差异，从而加快调试速度

> 原文：<https://dev.to/_darrenburns/debug-faster-with-colourful-diffs-for-your-failing-tests-4i57>

Pytest 正在迅速成为 Python 社区中事实上的测试框架，但我总是发现它报告断言错误的方式在许多情况下很难快速解析。在这篇文章中，我将探索如何使用 Python 标准库的一个鲜为人知的部分来构建一个 pytest 插件，该插件可以输出类似于在语言测试框架(如 Elixir 和 JavaScript)中看到的彩色差异。这个*不是*教程，但是它应该给你以下主题的概述:

*   开始制作 pytest 插件
*   如何使用 Python 标准库计算文本之间的差异
*   Python 中如何使用分部应用的简要说明
*   用转义序列格式化终端输出(与编程语言无关)

下面是安装了`pytest-clarity` ( [GitHub](https://github.com/darrenburns/pytest-clarity) )的失败测试的样子:

[![Colourful Pytest Output](img/2fe560cbaaea6dac17603b4e414ade48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0cPI9Ryv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yp7wmfren9ioon2e3llp.png)

从这个输出中，很容易看出*为什么断言失败了。你的眼睛会立刻被字典之间的不同所吸引，颜色会告诉你为了使它们匹配，需要在断言的两边添加或删除什么。*

如果你想尝试这个插件，但不想阅读我实现它的经验，你可以用:
安装它

```
pip install pytest-clarity 
```

运行该命令后，您可以正常运行您的测试，您将获得彩色输出！如果您有任何问题或者出于任何原因想要卸载，您可以通过运行`pip uninstall pytest-clarity`来完成。

为了比较，我在下面附上了使用 vanilla pytest 的相同测试的输出(启用了非常详细的日志记录`-vv`)。

[![Vanilla Pytest Output](img/f05ed232d4734d641819692007c84a66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R-Ql6INi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3er3g51rsdn5fva790ql.png)

在我看来，这个输出有很多问题，但最突出的问题是它分散了被比较对象的实际内容。这些对象的表示中穿插着`+`、`-`、`?`和`^`符号，这使得回答*这个问题变得异常困难:“我正在比较的对象实际上看起来像什么，它们有什么不同？”*

## 用插件修改 Pytest 输出

Pytest 有一个强大的插件系统，和一个活跃的有用插件生态系统。开始插件开发最简单的方法是使用 [@hackebrot](https://github.com/hackebrot) 的 pytest 插件的 Cookiecutter 模板。

在我们使用 Cookiecutter 模板创建了项目之后，我们需要找到*钩子*，它将使我们能够定制如何报告断言错误。

Pytest 提供了许多挂钩，允许我们添加或定制其功能的各个方面，但在这种情况下，我们需要`pytest_assertrepr_compare`。通过在由 Cookiecutter 创建的`pytest_{yourpluginname}.py`文件中创建一个名为`pytest_assertrepr_compare`的函数，我们可以在断言失败时覆盖默认输出 pytest 打印到终端。

钩子有如下签名(类型注释是我自己的):

```
def pytest_assertrepr_compare(
    config: _pytest.config.Config,
    op: str,
    left: Any,
    right: Any,
) -> List[str] 
```

<small>注意:不幸的是 pytest 似乎没有将详细的断言检查信息传递到这个钩子中，这意味着我们不能充分利用断言重写，除非我们自己检查 AST。</small>

第一个参数是 pytest `Config`对象，我们不需要担心。`op`参数是指测试中的`assert`语句中使用的操作。例如，如果你的断言看起来像`assert 1 == 2`，那么`op`就是`"equal"`。`left`和`right`是指出现在`op`左侧和右侧的数值。在前面的例子中，`left`是`1`，而`right`是`2`。该函数返回一个字符串列表，每个字符串对应于一行输出，pytest 将在断言失败时将该行输出写入终端。

### 一个简短的`assertrepr_compare`插件例子

这里有一个使用`assertrepr_compare`钩子的简单例子，它只打印了`assert`语句的左右操作数，以及用于终端的操作符:

```
# Basic implementation of the assertrepr_compare hook def pytest_assertrepr_compare(config, op, left, right):
    return [
        '',  # newline because it looks strange without
        'op: {}'.format(op),
        'left: {}'.format(repr(left)),
        'right: {}'.format(repr(right)),
    ]

# We'll run this test, and check the output def test_one_equals_two():
    assert 1 == 2 
```

在运行测试之后，钩子的上述实现产生了下面的输出:

```
___________________ test_compare_strings ___________________

    def test_compare_strings():
>       assert 1 == 2
E       assert
E         op: ==
E         left: 1
E         right: 2

tests/test_util.py:64: AssertionError 
```

现在，我们已经获得了将 pytest 的输出转换成我们想要的任何格式所需的所有信息。让我们看看 pytest 当前是如何计算断言失败时向用户显示的差异的。

## Pytest 如何计算差值

`pytest-clarity`会非常依赖 [`difflib`](https://docs.python.org/3.7/library/difflib.html) ，包含在 Python 标准库中。`difflib`提供了用于比较序列和计算序列间差异的类和帮助函数。事实证明，pytest 也使用`difflib`来显示断言为假时的输出。它使用 difflib 中的 [`ndiff`](https://docs.python.org/3.7/library/difflib.html#difflib.ndiff) 辅助函数来实现。这个函数返回一个生成字符串的生成器，每个字符串对应于 delta 输出的一行。

```
import difflib
lhs = "hello"
rhs = "world"
delta = difflib.ndiff([lhs], [rhs])
print("\n".join(delta)) 
```

这是输出，它和你在`pytest`中看到的输出是一样的，如果你写`assert "hello" == "world"` :

```
- hello
+ world 
```

这种方法的问题是，它将语义和它生成的增量的表示紧密地耦合在一起。它希望我们直接输出它产生的字符串。如果我们能够获取一个数据结构而不是一个表示 diff 本身的字符串，并且我们可以使用它来生成丰富多彩的输出，那就太好了。

很幸运，`difflib`有了答案！

## 进入序列匹配器

`SequenceMatcher`类是`difflib`的一部分，它为我们提供了一种比较可散列序列对`A`和`B`的方法。我们可以用它来找到`A`中每个必须被替换、删除或插入的元素的准确索引，以便将`A`转换成`B`。这很棒，因为我们现在可以访问 diff 的抽象表示，并且我们可以按照我们想要的方式呈现它(颜色，*无处不在*)。

在继续之前，让我们看看如何理解和使用`SequenceMatcher`。我们感兴趣的方法叫做`get_opcodes`。该方法返回一个 5 元组列表，描述如何将`A`转换为`B`。Python difflib 文档对它有一个坚实的[解释](https://docs.python.org/3/library/difflib.html#difflib.SequenceMatcher.get_opcodes)，但是下面的代码片段应该给出它如何工作的一个粗略的想法。

```
import difflib
matcher = difflib.SequenceMatcher(None, "hello", "world")
for tag, i1, i2, j1, j2 in matcher.get_opcodes():
    # tag can be one of 'replace', 'delete', 
    # 'insert', or 'equal', and represents an operation
    # to be performed in order to transform the 
    # left string into the right string.
    # i1:i2 represents a slice of the left string, 
    # j1:j2 a slice of the right string.
    # i1:i2 and j1:j2 are the ranges within the strings that 
    # the operation should be performed on 
```

`get_opcodes`方法为我们提供了所需的信息，以确定用哪种颜色将输出写入终端。

## 格式化终端输出

将输出格式化到终端可能很棘手。它的工作原理是向我们的终端发送*转义码*，本质上是“命令”，我们用一系列字符来表示。如果终端支持转义码，而不是打印它，它将执行该命令。终端转义码让我们可以做如下事情:

*   更改光标的位置
*   更改输出文本的前景色和背景色
*   更改输出文本的格式(*斜体*、**粗体**等)。)
*   使光标不可见

这些转义码可能很难管理，并且功能会因终端类型而异。事实上，基于终端的软件，如 Vim 的存在，应该给出了这些转义序列提供的能力的概念。幸运的是，有很多可用的库，这使得我们的过程更加容易。对于这个插件，我使用了`termcolor`库。它提供的`colored`函数使得编写丰富多彩的格式化输出变得容易。

### 旁白:Python 中的部分函数应用

来自`termcolor`的`colored`函数将在插件的几个地方使用。我们将传递给它一组一致而冗长的参数，我们不希望在任何地方都重复这些参数，所以它是*部分函数应用程序*的绝佳候选对象！部分函数应用程序让我们通过预先传递一些参数来“准备”一个函数。然后，当我们以后想要使用该函数时，我们不必再次传递这些参数。

```
from functools import partial
deleted_text = partial(colored, color=Color.red, attrs=[Attr.bold])
diff_intro_text = partial(colored, color=Color.cyan, attrs=[Attr.bold])
inserted_text = partial(colored, color=Color.green, attrs=[Attr.bold]) 
```

现在我们可以像调用`colored`一样调用函数`deleted_text`、`diff_intro_text`和`inserted_text`，但是我们可以省略`color`和`attrs`命名参数，因为它们已经被提前应用了。如果给部分应用的函数起一个有意义的名字，使用部分应用可以使你的代码更具可读性，但是只在有意义的地方使用它。

### 终端转义序列

默认情况下，pytest 将断言报告的每一行输出为红色文本(参见本文开头的截图)。我们不希望这样，所以我们希望指示我们的终端恢复到标准的字符格式。不幸的是，我不认为`termcolor`有这个功能，所以我们必须自己给终端发送转义序列。

为 VT-100 兼容终端清除字符格式的转义序列是`\033[0m`(大多数终端仿真器都支持此功能)。*ascii-table.com*有一个方便的[参考](http://ascii-table.com/ansi-escape-sequences-vt-100.php)列表终端转义序列。`\033`部分是十进制值 27 的八进制表示。如果你查找一个 ASCII 表，你会发现 27 <sub>dec</sub> 映射到`ESC`(转义)字符。然后，我们有一个`[`，据我所知只是一个分隔符。序列的剩余部分是映射到函数的字母数字代码。在本例中，`0m`映射到“清除角色属性”命令。如果我们在每一行输出的开头都打印出这个转义码，当 pytest 试图用红色粗体字打印所有内容时，我们将覆盖它，终端将以默认格式输出文本。

```
def plain_text(string):
    return "\033[0m" + string 
```

## 把所有的东西放在一起

当一个测试失败时，pytest 调用`assert`语句两边的`repr`函数，输出这些对象表示的 diff。我们不依赖于`repr`，而是使用`pprint.pformat`，它将为我们提供一个格式良好的对象字符串表示，为了清晰起见，它可能跨越多行，并使输出更易于解析。在构建表示时，`pprint.pformat`还对无序集合进行排序，比如`dicts`和`sets`。这很重要，因为如果两个字典的表示有不同的键排序，我们每次都会得到不同的输出！

```
lhs_repr = pprint.pformat(left, width=width)
rhs_repr = pprint.pformat(right, width=width) 
```

现在我们有了“漂亮”的表示，我们可以使用前面的`SequenceMatcher`在它们之间生成一个增量，并使用我们的着色函数打印出文本。

下面是打印一个拆分 diff 的代码(拆分 diff 是指 diff 的左边和右边分别打印出来):

```
matcher = difflib.SequenceMatcher(None, lhs_repr, rhs_repr)
for op, i1, i2, j1, j2 in matcher.get_opcodes():

        # Deltas can span multiple lines, but we need to 
        # operate on a line by line basis so we can override
        # pytests attempts to print every individual line red
        lhs_substring_lines = lhs_repr[i1:i2].splitlines()
        rhs_substring_lines = rhs_repr[j1:j2].splitlines()

        # Highlight chars to remove from the left hand side
        for i, lhs_substring in enumerate(lhs_substring_lines):
            if op == 'replace':
                lhs_out += deleted_text(lhs_substring)
            elif op == 'delete':
                lhs_out += deleted_text(lhs_substring)
            elif op == 'insert':
                lhs_out += plain_text(lhs_substring)
            elif op == 'equal':
                lhs_out += plain_text(lhs_substring)

            if i != len(lhs_substring_lines) - 1:
                lhs_out += '\n'

        # Highlight the stuff to be added on the right hand side
        for j, rhs_substring in enumerate(rhs_substring_lines):
            if op == 'replace':
                rhs_out += inserted_text(rhs_substring)
            elif op == 'insert':
                rhs_out += inserted_text(rhs_substring)
            elif op == 'equal':
                rhs_out += plain_text(rhs_substring)

            if j != len(rhs_substring_lines) - 1:
                rhs_out += '\n'

    # Return the left and right diffs as lists of strings
    return lhs_out.splitlines(), rhs_out.splitlines() 
```

## 结论

这篇文章快速浏览了一些`pytest-clarity`背后的代码。该插件目前在 PyPI 上可用，你可以使用`pip` :
安装它

```
pip install pytest-clarity 
```

完整的项目可以在 GitHub 上获得(见下文)。如果您有任何疑问或问题，请告诉我！

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [达伦伯恩斯](https://github.com/darrenburns) / [ pytest-clarity](https://github.com/darrenburns/pytest-clarity)

### 一个插件，以丰富多彩的统一差异改善 pytest 的输出

<article class="markdown-body entry-content container-lg" itemprop="text">

# pytest-透明度

[![PyPI version](img/58c56edd085bfa8bfff88ddf8059cba8.png) ](https://pypi.org/project/pytest-clarity) [![conda-forge version](img/e9f75f12c63960d3511f14dc59610a15.png) ](https://anaconda.org/conda-forge/pytest-clarity) [![Python versions](img/829615706e9e6bf5bef933a36691e721.png) ](https://pypi.org/project/pytest-clarity) [![See Build Status on Travis CI](img/cba25a1c450dd93a3ad82e33653f61b8.png)](https://travis-ci.org/darrenburns/pytest-clarity)

一个提高 pytest 输出可读性的插件。

## 特征

*   为失败的测试提供更容易解析和更容易理解的差异。
*   可以显示统一差异或分割差异，并根据上下文选择它们。
*   向失败测试的输出中添加有用的提示，以帮助您跟踪常见问题。

使用 pytest-clarity:

[![Example output with clarity](img/26bd164300f4cae349221033c92723b1.png)](https://raw.githubusercontent.com/darrenburns/pytest-clarity/master/pytest-clarity.png)

相同的测试，没有 pytest-clarity:

[![Example output without clarity](img/c108ea815bbcf64fa7d20b0a57ba7664.png)](https://raw.githubusercontent.com/darrenburns/pytest-clarity/master/without-clarity.png)

## 要求

支持 Python 2.7 和 3.4+版本

## 装置

您可以从 [PyPI](https://pypi.org/project) 通过 [pip](https://pypi.org/project/pip/) 安装“pytest-clarity”:

```
$ pip install pytest-clarity

```

## 使用

如上所述安装插件。

**只有当-vv 选项提供给 pytest 时，插件才会被激活。**

您可以使用`--diff-type`选择想要的差异类型(自动(默认)、分离或统一):

```
pytest -vv --diff-type=split

```

您可以使用`--no-hints`标志禁用提示:

```
pytest -vv --no-hints

```

您可以使用`--diff-width`选项配置输出的宽度:

```
pytest -vv --diff-width=60

```

## 贡献的

投稿…

</article>

[View on GitHub](https://github.com/darrenburns/pytest-clarity)