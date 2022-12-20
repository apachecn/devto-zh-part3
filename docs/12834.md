# 如何在 Python 中检查文件是否存在

> 原文：<https://dev.to/renegadecoder94/how-to-check-if-a-file-exists-in-python-11om>

作为一个刚刚了解规范 URL 和交叉发布的人，我想我应该用我的一篇更受欢迎的文章在 [dev.to](//dev.to) 上尝试一下。希望你喜欢！

## 问题介绍

最近，我想从一个配置文件中读取一些数据，但是我希望代码是向后兼容的。换句话说，如果配置文件不存在，我想采用原来的预设值。否则，我会从配置文件中提取数据。

幸运的是，我做了研究，并提出了解决方案。计划是检查配置文件是否存在。如果有，程序会从中读取并填充必要的字段。否则，程序会依赖一些任意的预设值。

但是要做到这一点，我必须找到一种方法来验证文件的存在。事实证明，在 Python 中有很多方法可以做到这一点。

## 方案

如果我们要检查一个文件是否存在，有几个解决方案:

*   检查是否存在带有`try/except`块的文件(Python 2+)
*   使用`os.path` (Python 2+)检查文件是否存在
*   使用`Path`对象检查文件是否存在(Python 3.4+)

当然，由我们来决定哪种解决方案最适合我们！

### 用 Try 块检查文件是否存在

列表中的第一个是一个 try-except 块。在这个场景中，我们将尝试在 try 块中打开我们的文件。如果文件无法打开，我们运行预设值。例如:

```
try:
    fh = open('/path/to/file', 'r')
    # Store configuration file values except FileNotFoundError:
    # Keep preset values 
```

Enter fullscreen mode Exit fullscreen mode

这个解决方案可能是最简单和最健壮的，但是`FileNotFoundError`是 Python 3 的更新。在 Python 2 中捕捉`IOError`会有更多的麻烦。

### 检查操作系统路径下是否存在文件

另一种选择是完全跳过错误处理，直接验证路径是否存在。例如:

```
import os
exists = os.path.isfile('/path/to/file')
if exists:
    # Store configuration file values else:
    # Keep presets 
```

Enter fullscreen mode Exit fullscreen mode

当然这里的弊端是 2 号线到 3 号线的竞态条件。如果由于某种原因，配置文件在第 2 行和第 3 行之间被删除，那么脚本将会崩溃。如果这在您的应用程序中没有风险，那么这个解决方案非常好。

### 检查文件是否存在路径对象

如果你像我一样痴迷于面向对象编程，那么也许这个解决方案适合你。从 Python 3.4 开始，我们可以将文件引用包装在一个对象中，这带来了许多新功能。例如:

```
from pathlib import Path
config = Path('/path/to/file')
if config.is_file():
    # Store configuration file values else:
    # Keep presets 
```

Enter fullscreen mode Exit fullscreen mode

此外，这个新的对象表示允许我们使用我们原来的 try-except 块:

```
try:
    absolute_path = config.resolve()
    # Store configuration file values except FileNotFoundError:
    # Keep presets 
```

Enter fullscreen mode Exit fullscreen mode

当然，您可能不需要所有这些功能。毕竟，如果阅读内容是目标，那么第一个选项可能是最好的。

### 稍微重述一下

使用上面的方法，我们有几个选项来检查一个文件在 Python 中是否存在:

```
# Brute force with a try-except block try: 
    fh = open('/path/to/file', 'r') 
except FileNotFoundError: 
    pass

# Leverage the OS package import os 
exists = os.path.isfile('/path/to/file')

# Wrap the path in an object for enhanced functionality from pathlib import Path
config = Path('/path/to/file') 
if config.is_file(): 
    pass 
```

Enter fullscreen mode Exit fullscreen mode

出于本教程的目的，我们只对文件感兴趣。然而，这些解决方案可以适用于[验证目录和符号链接](https://www.guru99.com/python-check-if-file-exists.html)的存在，所以不要害怕尝试。这就是 Python 的妙处！

无论如何，感谢您花时间阅读这篇文章。如果你是第一次来这里，并且你觉得这篇文章很有帮助，为什么不订阅 Renegade Coder ？订阅是免费的，您将始终获得最新的内容。

下次见！