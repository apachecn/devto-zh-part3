# Pathlib 入门

> 原文：<https://dev.to/iyanuashiri/getting-started-with-pathlib-33n4>

## 简介

本教程将指导你如何使用 Pathlib 模块来处理文件系统路径，好处，并理解它所解决的问题，因为 Python 标准库已经包含了`os.path.`

## 怎么比 os.path 好

是处理文件系统路径的唯一方法，但是它有一些限制。为了执行某些任务，你
必须从其他标准库模块导入。例如，假设你想列出一个目录中以`.txt`
结尾的文件，`os.path`是不够的。

使用`os.path`，您将执行以下操作:

```
import os
import glob

list(glob(os.path.join('posts', '*.md'))) 
```

Enter fullscreen mode Exit fullscreen mode

使用`pathlib`，您将执行以下操作:

```
from pathlib import Path

list(Path('posts').glob('*.md')) 
```

Enter fullscreen mode Exit fullscreen mode

从第一个使用`os.path`的例子开始，就需要导入`glob`。但是有了`pathlib`，其他模块中的大部分功能都集中到了一个地方。

## 入门

```
from pathlib import Path 
```

Enter fullscreen mode Exit fullscreen mode

根据标准库，Path 为运行代码的平台实例化一个具体的路径。这基本上意味着 Path 类计算出运行代码的平台所需的路径分隔符。

注意:Windows 使用反斜杠作为路径分隔符，而基于 unix 的系统使用正斜杠。

还有其他可以使用的类，比如`WindowsPath`和`PosixPath`

## 使用路径类方法

```
Path.home()

PosixPath('/home/ashiri')

Path.cwd()

PosixPath('/home/ashiri/iyanuashiri/content/posts') 
```

Enter fullscreen mode Exit fullscreen mode

Path 类为我们提供了两个类方法。`home` classmethod 返回主目录路径对象，而
`cwd` classmethod 返回当前工作目录路径对象

## 创建路径对象

```
p = Path.home() / 'iyanu' 
```

Enter fullscreen mode Exit fullscreen mode

PosixPath('/home/ashiri/iyanu ')

这个代码片段创建了一个可以在 Python 代码中使用的 path 对象，但是这个目录已经存在
,代码可以执行一些操作。在接下来的两个例子中，我们将展示如何从您的 Python 代码中创建一个新目录和一个新文件。

## 新建一个目录

```
p = Path.home() / 'folder_name'
p.mkdir() 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，我们创建了一个名为`p`的路径对象，并使用了实例方法`mkdir`。

## 创建新文件

```
p = Path.home() / 'folder_name'
p.mkdir()

r = p / 'file_name.txt'

r.touch() 
```

Enter fullscreen mode Exit fullscreen mode

在这个代码片段中，我们首先创建一个名为`p`的路径对象，然后使用`mkdir()`实例方法。
接下来是创建另一个名为`r`的路径对象，并使用实例方法`touch()`。

方法可以接受一个参数。这防止了`FileExistsError`被升高。

## 打开路径中的文件

有一个`open`实例方法，它的工作方式类似于内置的`open`函数。所以我们可以这样做
:

```
with r.open as file:
    file.readline 
```

Enter fullscreen mode Exit fullscreen mode

## 总之

除了在 pathlib 文档页面上可以学到的东西，还可以在这里找到

如果你喜欢这篇文章，别忘了在下面分享和评论。在 Twitter 上关注我: [@IyanuAshiri](https://www.twitter.com/iyanuashiri) ，我发关于 Python 的微博。你也可以查看我的博客 [iyanuashiri.me](https://iyanuashiri.me) 来获取更多的
Python 内容。