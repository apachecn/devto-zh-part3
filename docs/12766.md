# python 模块和包之间的区别

> 原文：<https://dev.to/wangonya/difference-between-a-python-module-and-a-package-59df>

## 模块

模块是可以导入的单个 Python 文件。任何 python 文件都可以是一个模块。例如，如果我有两个 Python 文件:`module.py`和`hello.py`在同一个目录:

```
# module.py 
def hello(name):
    print("Hello {}".format(name)) 
```

Enter fullscreen mode Exit fullscreen mode

我可以在我的`hello.py` :
中`import`那个模块

```
#hello.py 
import module

module.hello("World!") # Hello World! 
```

Enter fullscreen mode Exit fullscreen mode

同样可以在解释器中完成:

```
>>> from module import hello
>>> hello("World!") # Hello World! 
```

Enter fullscreen mode Exit fullscreen mode

## 套餐

包由多个 Python 文件(或模块)组成，甚至可以包括用 C 或 C++等不同语言编写的库。在一个文件夹中看到一个`__init.py__`文件通常会告诉你这个文件夹是一个 Python 包。`__init__.py`不需要包含任何代码——有时需要——它只需要在 Python 把那个特定的文件夹打包时出现。

```
📁 my_package
    |- __init__.py
    |- module.py 
```

Enter fullscreen mode Exit fullscreen mode

```
# __init.py__ 
from my_package.module import hello 
```

Enter fullscreen mode Exit fullscreen mode

当您在脚本中导入`my_package`时，`__init__.py`脚本将会运行，让您可以访问包中的所有函数。在这种情况下，它只提供对`module.hello`功能的访问。