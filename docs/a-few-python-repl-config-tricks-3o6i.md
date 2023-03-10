# 一些 Python REPL 配置技巧

> 原文：<https://dev.to/kenbellows/a-few-python-repl-config-tricks-3o6i>

作为回到学校攻读计算机科学研究生学位的结果，我五年来第一次大量使用 Python。周末，我一直在忙于一项非常繁重的调试任务，我发现了一些有用的技巧来改进我的 Python [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) 以用于调试工作，所以我想我应该分享一下。

# 给你弄个`.pythonrc`

当我在 REPL 进行调试时，我开始构建一些非常有用的函数。但是每次我退出 REPL 并再次启动它，或者在一个单独的终端中打开另一个 REPL，我都必须再次复制并粘贴所有这些方法。我意识到我想要的是一个相当于`.bashrc`的 Python，一个在我每次启动 REPL 时运行的脚本，用来定义我的函数和设置我的环境。

据我所知(如果我错了，请在评论中纠正我)，Python 默认情况下不查找任何特定的安装文件。但是，它会检查一个名为`PYTHONSTARTUP`的特殊环境变量，如果该变量被设置为一个路径，它会在该路径运行文件。完美！

我正在使用 Bash，所以我将这一行添加到我的`.bashrc`文件:

```
export PYTHONSTARTUP=~/.pythonrc.py 
```

如果你在 Windows land，你可以点击开始按钮，搜索“环境”，选择“编辑你的帐户的环境变量”，然后添加一个新的变量，将“PYTHONSTARTUP”添加到你的帐户的环境变量中。

> **注意:**你不一定要把文件放在你的主目录下，或者命名为`.pythonrc.py`；你在告诉 Python 去哪里找，所以做你想做的！只要确保变量指向你的文件，无论你把它放在哪里。

# 码起来！

我的`~/.pythonrc.py`文件正在处理中，但这里是目前为止的内容:

```
### enable tab completion import rlcompleter, readline
readline.parse_and_bind("tab: complete")

### import custom python utilities import sys
from os.path import expanduser
sys.path.append(expanduser('~'))
from custom_utils import * 
```

我这里有两样东西:

1.  我手动启用制表符补全，因为默认情况下它似乎不工作。
2.  我导入了一个方便函数的自定义模块。我决定将它们与这个配置文件分开定义，这样我就可以在其他地方使用它们(马上回到这个话题)。为此，我必须将我的主目录(我保存模块的地方)添加到路径中，然后从我的模块添加`import *`。

> 注意:同样，这个模块可以被命名为任何名字，可以去任何地方，只要确保你正确地指向它！

# 我的自定义工具

这是我的`custom_utils.py`文件的内容。再说一次，这是一项正在进行的工作，我刚刚开始使用它，但是我发现这些方法对于探索我在课程作业中使用的库和调试会话(接下来！).

```
from math import ceil

def chunk(size, _iter):
  """ split an iterable into chunks of the given size """
  _num_groups = range(int(ceil(len(_iter)/float(size))))
  return (_iter[n*size:(n+1)*size] for n in _num_groups)

def P(obj, key=None):
  """
  List the public properties of an object, optionally
  filtered by a `key` function
  """
  props = [p for p in dir(obj) if p[0] is not '_']
  if key is not None:
    props = [p for p in props if key(p)]
  return props

def PT(obj, key=None):
  """
  List an object's public properties in a 4-col table,
  optionally filtered by a `key` function
  """
  _p = P(obj, key=key)
  if filter is not None:
    _max_w = max(len(p) for p in _p)

  print('\n'.join(''.join(n.ljust(ceil((_max_w/4 + 2)*4)) for n in c) for c in chunk(4, P(obj)))) 
```

这些功能都是围绕我在学习过程中遇到的一个问题展开的。教员给了我们起始代码，我们必须添加它来完成任务。starter 代码使用了一个我以前没有遇到过的库，这个库的文档不够全面，所以我使用这些方法对它创建和传递的对象进行了大量的手工挖掘。

不过，这个文件最酷的地方在于，你可以添加任何对它有用的东西，用几年的时间构建它，将它同步到 Git repo，等等！

# 调试用`pdb`

这个周末我经常使用的另一个工具是`pdb`，Python 内置的调试器。它有自己的 REPL，我沮丧地发现`pdb` REPL 根本不关注 PYTHONSTARTUP 变量。然而，我很高兴地发现，它实际上*有一个默认的配置文件，`~/.pdbrc`！*

 *因为我在一个单独的模块中有我的自定义函数，所以我所要做的就是将那个模块导入到我的`.pdbrc`中，这样我就设置好了！

```
### enable tab completion import rlcompleter
import pdb
pdb.Pdb.complete = rlcompleter.Completer(locals()).complete

### import custom python utilities import sys
from os.path import expanduser
sys.path.append(expanduser('~'))
from custom_utils import * 
```

注意，在`pdb`中启用制表符补全与标准 REPL 略有不同，但是 StackOverflow 拯救了我！

# 你得到了什么？

这是我的设置和我的小工具，但我想看看你的！请在评论中留下您一直使用的任何方便的 Python 实用函数！*