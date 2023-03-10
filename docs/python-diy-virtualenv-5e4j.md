# Python: DIY virtualenv

> 原文：<https://dev.to/k4ml/python-diy-virtualenv-5e4j>

`virtualenv`在 Python 中是一个非常知名的工具。几乎所有教程都会推荐你使用。过去，我也写过为什么你不应该使用系统 python 。

它是必不可少的，从 python 3.4 开始，它作为`venv`模块被捆绑在 python 本身中。

实际上,`virtualenv`没有什么魔力。它只是你系统中已经有的 python 解释器的副本(或符号链接),加上几个其他文件。

正如我们小时候所知道的，学习和理解事物的最好方法是打破它，或者试着从零开始建立它。所以我们试着不用内置模块来构建 venv/virtualenv。

如前所述，virtualenv 只是一个目录和文件的集合。

```
mkdir myenv
mkdir myenv/bin 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要找出系统 python 解释器在哪里。

```
which python3
/usr/local/bin/python3 
```

Enter fullscreen mode Exit fullscreen mode

让我们把它复制到我们的“virtualenv”中:-

```
cp /usr/local/bin/python3 myenv/bin/ 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们试着调用我们的“新”python 解释器:-

```
myenv/bin/python3 
```

Enter fullscreen mode Exit fullscreen mode

我们应该会看到常见的提示，例如:-

```
Python 3.6.0 (default, Jan 24 2017, 16:44:16)
[GCC 4.2.1 Compatible Apple LLVM 6.0 (clang-600.0.57)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的“virtualenv”有了自己的解释器，我们还必须确保它有自己的`site-packages`目录，所有我们要安装的包都将在那里。这是我们使用 virtualenv 的主要原因，这样我们安装的包就不会与系统 python 或其他项目 virtualenv 混淆。运行`myenv/bin/python3`并运行以下代码:-

```
>>> sys.path
['', '/Library/Frameworks/Python.framework/Versions/3.6/lib/python36.zip', '/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6', '/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/lib-dynload', '/Users/kamal/Library/Python/3.6/lib/python/site-packages', '/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages']
>>> sys.prefix
'/Library/Frameworks/Python.framework/Versions/3.6' 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们的`myenv`目录在输出中是 none。`sys.prefix`上的[文件](https://docs.python.org/3/library/sys.html#sys.prefix)这样写道:-

> 如果虚拟环境有效，该值将在 site.py 中更改为指向虚拟环境。通过 base_prefix，Python 安装的值仍然可用。

点击[虚拟环境](https://docs.python.org/3/library/venv.html#venv-def)上的链接，上面写着

> 虚拟环境是一个目录树，其中包含 Python 可执行文件和其他表明它是虚拟环境的文件。

但是什么是虚拟环境呢？我在这里做了一点手脚，用`python -mvenv tmp_env`创建了一个新的 virtualenv，在新创建的 virtualenv 中，我注意到一个名为`pyenv.cfg`的文件，它包含了:-

```
home = /usr/local/bin
include-system-site-packages = false
version = 3.6.0 
```

Enter fullscreen mode Exit fullscreen mode

所以让我们试着将这个文件添加到我们的 virtualenv 中作为`myenv/pyenv.cfg`。添加这个文件后，我们的解释器将给出以下输出:-

```
>>> import sys
>>> sys.path
['', '/Library/Frameworks/Python.framework/Versions/3.6/lib/python36.zip', '/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6', '/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/lib-dynload']
>>> sys.prefix
'/Users/kamal/myenv' 
```

Enter fullscreen mode Exit fullscreen mode

注意`sys.prefix`值。现在我们有所发现了。它正确地指向了我们的 virtualenv 目录。然而我们的 virtualenv 仍然不在`sys.path`中。我忘了点东西！我们还没有创建`lib`目录。所以现在就开始吧:-

```
mkdir -p myenv/lib/python3.6/site-packages 
```

Enter fullscreen mode Exit fullscreen mode

并查看我们的`sys.path` :-

```
>>> sys.path
['', '/Library/Frameworks/Python.framework/Versions/3.6/lib/python36.zip', '/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6', '/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/lib-dynload', '/Users/kamal/myenv/lib/python3.6/site-packages'] 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的 virtualenv 加到了`sys.path`！是时候安装一些包了:-

```
myenv/bin/python3 -mpip install requests 
```

Enter fullscreen mode Exit fullscreen mode

但是我们会得到下面的错误:-

```
/Users/kamal/myenv/bin/python3: No module named pip 
```

Enter fullscreen mode Exit fullscreen mode

显然我们的目录仍然是空的，所以我们还没有 pip。幸运的是，得到 pip 并不困难。

```
wget https://bootstrap.pypa.io/get-pip.py
myenv/bin/python3 get-pip.py
Collecting pip
  Using cached https://files.pythonhosted.org/packages/c2/d7/90f34cb0d83a6c5631cf71dfe64cc1054598c843a92b400e55675cc2ac37/pip-18.1-py2.py3-none-any.whl
Collecting setuptools
  Using cached https://files.pythonhosted.org/packages/37/06/754589caf971b0d2d48f151c2586f62902d93dc908e2fd9b9b9f6aa3c9dd/setuptools-40.6.3-py2.py3-none-any.whl
Collecting wheel
  Using cached https://files.pythonhosted.org/packages/ff/47/1dfa4795e24fd6f93d5d58602dd716c3f101cfd5a77cd9acbe519b44a0a9/wheel-0.32.3-py2.py3-none-any.whl
Installing collected packages: pip, setuptools, wheel
Successfully installed pip-18.1 setuptools-40.6.3 wheel-0.32.3 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经安装了`pip`，让我们再次尝试安装`requests` :-

```
myenv/bin/python3 -mpip install requests 
```

Enter fullscreen mode Exit fullscreen mode

这一次，`requests`将顺利安装。让我们检查一下它是否被安装到正确的位置:-

```
myenv/bin/python3
>>> import requests
>>> requests
<module 'requests' from '/Users/kamal/myenv/lib/python3.6/site-packages/requests/__init__.py'> 
```

Enter fullscreen mode Exit fullscreen mode

这是正确的，我们现在有一个全功能的虚拟！

敏锐的读者可能会注意到，到目前为止，我们一直调用 python 作为`myenv/bin/python3`。如果我们想简单地调用它作为`python3`呢？在 virtualenv 中，有一个`activate`的概念，你可以在创建新的 env 后使用它。这基本上只是将新创建的 env 目录添加到`PATH`环境变量中，这是 OS shell 用来查找特定程序所在位置的搜索路径列表。`activate`的剧本基本上是这样的:-

```
OLD_PATH=$PATH
PATH=`pwd`/myenv/bin:$PATH 
```

Enter fullscreen mode Exit fullscreen mode

到`deactivate`，我们把`PATH`替换回`OLD_PATH`。

或者，我们也可以用包含新路径的 PATH 启动一个 subshell:-

```
PATH=`pwd`/myenv/bin:$PATH sh 
```

Enter fullscreen mode Exit fullscreen mode

要取消激活，我们只需从 subshell 中`exit`。我个人不推荐这个“激活”的东西。我更喜欢直接调用 virtualenv 解释器，使用它的完整路径。