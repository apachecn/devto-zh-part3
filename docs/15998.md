# Python 包结构死简单 Python:项目结构和导入

> 原文：<https://dev.to/codemouse92/dead-simple-python-project-structure-and-imports-38c6>

喜欢那些文章吗？买这本书！ [***死简单蟒*** 作者杰森·c·麦克唐纳可从无淀粉出版社购得。](https://nostarch.com/dead-simple-python)

* * *

教程最糟糕的部分总是它们的简单，不是吗？很少会发现一个文件有多个文件，更少会有多个目录。

我发现**构建 Python 项目**是语言教学中最常被忽视的部分之一。更糟糕的是，许多开发人员弄错了，在一大堆常见错误中跌跌撞撞，直到他们找到至少*有效的东西。*

好消息是:你不必成为他们中的一员！

在死亡简单 Python 系列的这一期中，我们将探索`import`语句、模块、包，以及如何将所有的东西组合在一起而不会让你抓狂。我们甚至会触及 VCS、PEP 和 Python 的禅。系好安全带。

# 设置知识库

在我们深入研究实际的项目结构之前，让我们先解决它如何适应我们的版本控制系统[VCS]...首先，你*需要*一辆 VCS！几个原因是...

*   跟踪你做的每一个改变，
*   计算出你打碎东西的确切时间，
*   能够看到旧版本的代码，
*   备份您的代码，以及
*   与他人合作。

你有很多选择。 **Git** 是最明显的，尤其是你不知道还能用什么的时候。您可以在 GitHub、GitLab、Bitbucket 或 Gitote 等网站上免费托管您的 Git 存储库。如果你想要 Git 之外的东西，有几十个其他的选择，包括 Mercurial、Bazaar、Subversion(尽管如果你使用最后一个，你可能会被你的同行认为是某种恐龙。)

在本指南的其余部分，我将假设您使用的是 Git，因为这是我专门使用的。

一旦您创建了您的存储库并在您的计算机上克隆了一个本地副本，您就可以开始设置您的项目了。至少，您需要创建以下内容:

*   对你的项目及其目标的描述。
*   如果项目是开源的，你的项目许可。(参见[opensource.org](https://opensource.org/)了解更多关于选择一个的信息。)
*   `.gitignore`:一个特殊的文件，告诉 Git 忽略哪些文件和目录。(如果您使用的是另一个 VCS，此文件会有不同的名称。查一下。)
*   以项目名称命名的目录。

没错...我们的 Python 代码文件实际上属于一个单独的子目录！这非常重要，因为我们仓库的根目录将会被构建文件、打包脚本、虚拟环境和所有其他不属于源代码的东西弄得乱七八糟。

仅仅为了举例，我们将把我们虚构的项目称为`awesomething`。

# PEP 8 和命名

Python 风格在很大程度上由一组名为 **Python 增强提案**的文档管理，缩写为 **PEP** 。当然，并不是所有的 pep 都被采纳了——这就是为什么他们被称为“提议”的原因——但是有些是。可以在 Python 官方网站浏览主 PEP 索引。这个指数正式称为 [PEP 0](https://www.python.org/dev/peps/) 。

现在，我们主要关注的是由 Python 语言创造者吉多·范·罗苏姆在 2001 年首次创作的 [**、PEP 8**](https://www.python.org/dev/peps/pep-0008/) 。它是正式概述所有 Python 开发人员通常应该遵循的编码风格的文档。把它放在你的枕头下面！学习它，跟随它，鼓励其他人也这样做。

(旁注:PEP 8 指出，风格规则总是有例外。这是一份*指南*，而不是*命令*。)

现在，我们主要关注标题为[“包和模块名”](https://www.python.org/dev/peps/pep-0008/#package-and-module-names)的部分...

> 模块应该有简短的，全小写的名字。如果可以提高可读性，可以在模块名中使用下划线。Python 包也应该有简短的、全小写的名字，尽管不鼓励使用下划线。

我们一会儿就会知道*模块*和*包*到底是什么，但是现在，要明白**模块是由文件名**命名的，而**包是由它们的目录名**命名的。

换句话说，**文件名应该全部小写，如果可以提高可读性的话，可以加下划线。**类似地，**目录名应该全部小写，如果可以避免的话，不带下划线**。换句话说...

*   这样做:`awesomething/data/load_settings.py`
*   不是这个:`awesomething/Data/LoadSettings.py`

我知道，我知道，用冗长的方式来证明一个观点，但至少我给你的步伐注入了一点活力。(*喂？这东西开着吗？*)

# 包和模块

这可能会让人觉得虎头蛇尾，但这里有一些承诺的定义:

**任何一个 Python ( `.py`)文件都是一个*模块*，一个目录下的一堆模块就是一个*包*。**

良好的...差不多了。要把一个目录变成一个包，还需要做另外一件事，那就是把一个名为`__init__.py`的文件放入其中。你实际上不必把任何东西*放进*那个文件。它必须在那里。

你还可以用`__init__.py`做其他很酷的事情，但这超出了本指南的范围，所以[去阅读文档了解更多](https://docs.python.org/3/tutorial/modules.html#packages)。

如果你在你的包中*做*忘记`__init__.py`，它会做一些比失败更奇怪的事情，因为这使它成为一个**隐式名称空间包**。对于这种特殊类型的包，您可以做一些很棒的事情，但是我在这里不打算深入讨论。像往常一样，你可以通过阅读文档了解更多: [PEP 420:隐式命名空间包](https://www.python.org/dev/peps/pep-0420/)。

所以，如果我们看看我们的项目结构，`awesomething`实际上是一个包，它可以包含其他包。因此，我们可以称`awesomething`为我们的*顶层包*，以及它的*子包*下的所有包。一旦我们开始进口东西，这将变得非常重要。

让我们来看一个我现实世界项目的快照，来了解我们是如何构建东西的...

```
omission-git
├── LICENSE.md
├── omission
│   ├── app.py
│   ├── common
│   │   ├── classproperty.py
│   │   ├── constants.py
│   │   ├── game_enums.py
│   │   └── __init__.py
│   ├── data
│   │   ├── data_loader.py
│   │   ├── game_round_settings.py
│   │   ├── __init__.py
│   │   ├── scoreboard.py
│   │   └── settings.py
│   ├── game
│   │   ├── content_loader.py
│   │   ├── game_item.py
│   │   ├── game_round.py
│   │   ├── __init__.py
│   │   └── timer.py
│   ├── __init__.py
│   ├── __main__.py
│   ├── resources
│   └── tests
│       ├── __init__.py
│       ├── test_game_item.py
│       ├── test_game_round_settings.py
│       ├── test_scoreboard.py
│       ├── test_settings.py
│       ├── test_test.py
│       └── test_timer.py
├── pylintrc
├── README.md
└── .gitignore 
```

Enter fullscreen mode Exit fullscreen mode

(如果你想知道，我用 UNIX 程序`tree`制作了上面的小图。)

您会看到我有一个名为`omission`的顶层包，它有四个子包:`common`、`data`、`game`和`tests`。我也有目录`resources`，但那只包含游戏音频，图片等。(为简洁起见，此处省略)。`resources`不是一个包，因为它不包含`__init__.py`。

我的顶层包中还有另一个特殊文件:`__main__.py`。这是我们通过`python -m omission`直接执行顶层包时运行的文件。我们一会儿会谈到那个`__main__.py`里发生了什么。

# 导入的工作原理

如果你以前写过任何有意义的 Python 代码，你几乎肯定熟悉`import`语句。例如...

```
import re 
```

Enter fullscreen mode Exit fullscreen mode

当我们导入一个模块时，知道我们实际上正在运行它是很有帮助的。这意味着模块中的任何`import`语句也在运行。

比如 [`re.py`](https://github.com/python/cpython/blob/3.7/Lib/re.py#L122) 有几个自己的 import 语句，我们说`import re`就执行。这并不意味着它们对我们从导入的`re` *文件是可用的，但这意味着那些文件必须存在。如果(出于某种不太可能的原因)`enum.py`在您的环境中被删除，并且您运行了`import re`，它将会失败并出现一个错误...*

> Traceback(最近一次调用 last):
> 文件“怪异. py”，第 1 行，在
> 导入 re
> 文件“怪异. py”，第 122 行，在
> 导入 enum
> ModuleNotFoundError:没有名为“enum”的模块

自然，读到这里，你可能会有点困惑。有人问我为什么找不到外部模块(在这个例子中是`re`)。其他人想知道为什么内部模块(`enum`在这里)被导入，因为他们没有在代码中直接要求它。答案很简单:我们进口了`re`，那个进口了`enum`。

当然，上面的场景是虚构的:`import enum`和`import re`在正常情况下永远不会失败，因为这两个模块都是 Python 核心库的一部分。这只是一个愚蠢的例子。；)

# 导入注意事项

实际上有许多导入的方法，但是大多数应该很少使用，如果曾经使用的话。

对于下面所有的例子，我们假设我们有一个名为`smart_door.py` :
的文件

```
# smart_door.py
def close():
    print("Ahhhhhhhhhhhh.")

def open():
    print("Thank you for making a simple door very happy.") 
```

Enter fullscreen mode Exit fullscreen mode

举个例子，我们将在 Python 交互式 shell 中运行本节中的其余代码，从与`smart_door.py`相同的目录中运行。

如果我们想运行函数`open()`，我们必须首先导入模块`smart_door`。最简单的方法是...

```
import smart_door
smart_door.open()
smart_door.close() 
```

Enter fullscreen mode Exit fullscreen mode

我们实际上会说`smart_door`是`open()`和`close()`的**名称空间**。Python 开发人员非常喜欢名称空间，因为它们让函数和其他东西的来源一目了然。

(顺便说一下，不要混淆*名称空间*和*隐式名称空间包*。它们是两码事。)

Python 的**禅，也被称为 [PEP 20](https://www.python.org/dev/peps/pep-0020/) ，定义了 Python 语言背后的哲学。最后一行有一个声明解决了这个问题:**

> 名称空间是一个非常棒的想法——让我们多做一些吧！

然而，在某种程度上，名称空间可能会成为一种痛苦，尤其是对于嵌套包。就是丑。谢天谢地，我们确实有办法在每次调用函数时都使用名称空间*。*

如果我们希望能够使用`open()`函数，而不必总是在它前面加上模块名，我们可以这样做...

```
from smart_door import open
open() 
```

Enter fullscreen mode Exit fullscreen mode

然而，请注意，`close()`和`smart_door.close()`在最后一个场景中都不能工作，因为我们没有直接导入函数。要使用它，我们必须把代码改成这样...

```
from smart_door import open, close
open()
close() 
```

Enter fullscreen mode Exit fullscreen mode

在之前那个可怕的嵌套包噩梦中，我们现在可以说`from foo.bar.baz.whatever import doThing`，然后直接使用`doThing()`。或者，如果我们想要一点名称空间，我们可以说`from foo.bar.baz import whatever`，说`whatever.doThing()`。

这样的系统非常灵活。

不过，过不了多久，您可能会发现自己在说“但是我的模块中有数百个函数，我想全部使用它们！”这是许多开发人员通过这样做而偏离轨道的地方...

```
from smart_door import * 
```

Enter fullscreen mode Exit fullscreen mode

这非常非常糟糕！简单来说就是直接导入模块里的所有东西，这就有问题了。想象下面的代码...

```
from smart_door import *
from gzip import *
open() 
```

Enter fullscreen mode Exit fullscreen mode

你认为会发生什么？答案是，`gzip.open()`将是被调用的函数，因为这是最后一个版本的`open()`被导入，并在我们的代码中被定义。`smart_door.open()`已经被**盯梢**——我们不能称之为`open()`，也就是说我们实际上根本不能称之为。

当然，由于我们通常不知道，或者至少不记得，*每一个被导入的模块中的每一个函数、类和变量，我们很容易陷入混乱。*

Python 的*禅也解决了这种情况...*

> 显性比隐性好。

你永远不必*猜测*一个函数或变量来自哪里。文件中的某个地方应该有代码，*明确地*告诉我们它来自哪里。前两个场景证明了这一点。

我还应该提到，早期的`foo.bar.baz.whatever.doThing()`场景是 Python 开发人员不喜欢看到的。同样来自 Python 的*禅...*

> 扁平的比嵌套的好。

一些包的嵌套是可以的，但是当你的项目开始看起来像一套精心制作的套娃时，你就做错了。将你的模块组织成包，但是要保持合理的简单。

# 在项目内导入

我们之前创建的项目文件结构即将在*中非常方便地*出现。回忆我的`omission`项目...

```
omission-git
├── LICENSE.md
├── omission
│   ├── app.py
│   ├── common
│   │   ├── classproperty.py
│   │   ├── constants.py
│   │   ├── game_enums.py
│   │   └── __init__.py
│   ├── data
│   │   ├── data_loader.py
│   │   ├── game_round_settings.py
│   │   ├── __init__.py
│   │   ├── scoreboard.py
│   │   └── settings.py
│   ├── game
│   │   ├── content_loader.py
│   │   ├── game_item.py
│   │   ├── game_round.py
│   │   ├── __init__.py
│   │   └── timer.py
│   ├── __init__.py
│   ├── __main__.py
│   ├── resources
│   └── tests
│       ├── __init__.py
│       ├── test_game_item.py
│       ├── test_game_round_settings.py
│       ├── test_scoreboard.py
│       ├── test_settings.py
│       ├── test_test.py
│       └── test_timer.py
├── pylintrc
├── README.md
└── .gitignore 
```

Enter fullscreen mode Exit fullscreen mode

在我的由`omission/data/game_round_settings.py`定义的`game_round_settings`模块中，我想使用我的`GameMode`类。该类在`omission/common/game_enums.py`中定义。我如何到达它？

因为我将`omission`定义为一个包，并将我的模块组织成子包，所以这实际上非常容易。在`game_round_settings.py`中，我说...

```
from omission.common.game_enums import GameMode 
```

Enter fullscreen mode Exit fullscreen mode

这叫做**绝对进口**。它从顶层包`omission`开始，向下进入`common`包，在那里寻找`game_enums.py`。

一些开发人员带着更像`from common.game_enums import GameMode`的导入语句来找我，想知道为什么它不起作用。简单地说，`data`包(`game_round_settings.py`所在的地方)不知道它的兄弟包。

然而，它知道它的父母。正因为如此，Python 有一个叫做**相对导入**的东西，让我们做类似这样的事情...

```
from ..common.game_enums import GameMode 
```

Enter fullscreen mode Exit fullscreen mode

`..`表示“这个包的直接父包”，在本例中是`omission`。所以，进口退回一级，走进`common`，找到`game_enums.py`。

关于使用绝对进口还是相对进口有很多争论。就我个人而言，我更喜欢尽可能使用绝对导入，因为这使得代码可读性更好。然而，你可以自己拿主意。唯一重要的部分是，结果是*显而易见的*——任何东西从何而来都不应该是神秘的。

(继续阅读:[真正的 Python——Python 中的绝对与相对导入](https://realpython.com/absolute-vs-relative-python-imports/)

这里还有一个潜在的陷阱！在`omission/data/settings.py`中，我有这一行:

```
from omission.data.game_round_settings import GameRoundSettings 
```

Enter fullscreen mode Exit fullscreen mode

当然，由于这两个模块在同一个包中，我们应该可以只说`from game_round_settings import GameRoundSettings`，对吗？

*错了！*实际上无法定位`game_round_settings.py`。这是因为我们正在运行顶层包`omission`，这意味着**搜索路径**(Python 在那里寻找模块，以及以什么顺序)的工作方式不同。

但是，我们可以使用相对导入来代替:

```
from .game_round_settings import GameRoundSettings 
```

Enter fullscreen mode Exit fullscreen mode

那样的话，单个`.`就是“这个包”的意思。

如果您熟悉典型的 UNIX 文件系统，这应该开始有意义了。`..`表示“后退一级”，`.`表示“当前位置”。当然，Python 更进了一步:`...`表示“后退两级”，`....`表示“后退三级”，以此类推。

然而，请记住，这里的那些“级别”不仅仅是普通的目录。它们是包裹。如果你在一个不是包的普通目录中有两个不同的包，你不能使用相对导入从一个跳到另一个。为此，您必须使用 Python 搜索路径，这超出了本指南的范围。(参见本文末尾的文档。)

# `__main__.py`

还记得我提到过在我们的顶层包中创建一个`__main__.py`吗？这是一个特殊的文件，当我们用 Python 直接运行这个包时会执行这个文件。我的`omission`包可以用`python -m omission`从我的库的根目录运行。

下面是该文件的内容:

```
from omission import app

if __name__ == '__main__':
    app.run() 
```

Enter fullscreen mode Exit fullscreen mode

是的，实际上就是它！我正在从顶层包`omission`中导入我的模块`app`。

记住，我也可以说`from . import app`来代替。或者，如果我只想说`run()`而不是`app.run()`，我可以说`from omission.app import run`或者`from .app import run`。最后，只要代码可读，我如何进行导入并没有太大的技术差异。

(旁注:我们可以争论我的主`run()`函数有一个单独的`app.py`是否合理，但是我有我的理由...它们超出了本指南的范围。)

一开始让大多数人困惑的部分是整个`if __name__ == '__main__'`声明。Python 没有太多的**样板**——几乎不需要修改就可以普遍使用的代码——但这是少有的一点。

`__name__`是每个 Python 模块的特殊字符串属性。如果我将行`print(__name__)`放在`omission/data/settings.py`的顶部，当该模块被导入(并因此运行)时，我们会看到“省略.数据.设置”被打印出来。

当一个模块通过`python -m some_module`直接运行时，该模块被赋予一个特殊值`__name__`:**main**。

因此，`if __name__ == '__main__':`实际上是检查模块是否作为*主*模块被执行。如果是，它将在条件下运行代码。

你可以从另一个角度来看这一点。如果我在`app.py`的底部添加以下内容...

```
if __name__ == '__main__':
    run() 
```

Enter fullscreen mode Exit fullscreen mode

...然后我可以通过`python -m omission.app`直接执行那个模块，结果和`python -m omission`一样。现在`__main__.py`完全被忽略了，`omission/app.py`的`__name__`就是`"__main__.py"`。

同时，如果我只是运行`python -m omission`，那么`app.py`中的特殊代码会被忽略，因为它的`__name__`现在又变成了`omission.app`。

明白这是怎么回事了吗？

# 包装完毕

我们来复习一下。

*   每个项目都应该使用 VCS，比如 Git。有很多选项可供选择。

*   每个 Python 代码文件(`.py`)都是一个**模块**。

*   将你的模块组织成**包**。每个包必须包含一个特殊的`__init__.py`文件。

*   您的项目通常应该由一个顶层包组成，通常包含子包。这个顶层包通常与您的项目同名，并且作为一个目录存在于您的项目存储库的根目录中。

*   **永远不要**在进口声明中使用`*`。在您考虑可能的例外之前，Python 的禅指出“特例不会特殊到违反规则。”

*   使用绝对或相对导入来引用项目中的其他模块。

*   可执行项目应该在顶层包中有一个`__main__.py`。然后，可以用`python -m myproject`直接执行那个包。

当然，在构建 Python 项目的过程中，我们可以使用很多更高级的概念和技巧，但我们不会在这里讨论这些。我强烈建议阅读这些文档:

*   [Python 引用:导入系统](https://docs.python.org/3/reference/import.html)
*   [Python 教程:模块](https://docs.python.org/3/tutorial/modules.html)
*   [PEP 8:Python 风格指南](https://www.python.org/dev/peps/pep-0008/)
*   [PEP 20:Python 之禅](https://www.python.org/dev/peps/pep-0020/)
*   [PEP 240:隐式命名空间包](https://www.python.org/dev/peps/pep-0420/)

* * *

*感谢`grym`、`deniska` (Freenode IRC `#python`)、 [@cbrintnall](https://dev.to/cbrintnall) 、[@韵脚](https://dev.to/rhymes) (Dev)的修改建议。*