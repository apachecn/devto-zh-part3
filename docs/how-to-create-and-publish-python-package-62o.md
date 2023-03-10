# 如何创建和发布 Python 包？

> 原文：<https://dev.to/umeshdhakar/how-to-create-and-publish-python-package-62o>

你好世界！Python 有一种创建和发布包的简单方法。 [Python 包索引](https://pypi.org/)是一个包存储库，维护所有发布的包。
有一次我对 python 中的那个打包的东西很好奇，所以决定学习这个东西。在学习的过程中，我记下了笔记，写下了必要的步骤。我提炼了那个笔记，并贴在这里，以便它可以帮助正在研究它的人。如果你想学习这些东西，这篇文章可以帮助你。你需要一个 PyPI 账号来发布一个包，所以在开始之前，请[在 PyPI 上注册](https://pypi.org/account/register/)，然后继续下面的过程。

*   为包创建一个目录。
    `mkdir demo_car`

*   在目录中创建文件`__init__.py`。
    `touch demo_car/__init__.py`
    *(这个文件表示包含它的目录是一个 python 包。)*

*   在文件中写入`'name = "<package-name>"'`。
    T1】

*   为包创建 python 脚本。
    `touch demo_car/engine.py`

*   让我们用 engine.py 脚本写一些代码。打开一个编辑器(我更喜欢 **Nano** ，写一些 python 代码或者粘贴下面的示例代码，通过 Ctrl+o.
    `nano demo_car/engine.py`保存文件

```
def start():
    print("Started!")

def stop():
    print("Stopped!") 
```

Enter fullscreen mode Exit fullscreen mode

> 如果没有可用的编辑器，则使用以下命令安装 nano。
> 
> `apt-get update`
> `apt-get install nano`
> `export EDITOR=nano`

*   包文件结构应该是这样的。

```
.  
├── demo_car  
│   ├── engine.py  
│   └── __init__.py  
├── LICENSE  
├── README.md  
└── setup.py 
```

Enter fullscreen mode Exit fullscreen mode

*   这些文件的用途。

    *   `setup.py`为打包构建脚本。
    *   每个包都应该有一个许可证。我们在这里使用[麻省理工学院许可](https://choosealicense.com/licenses/mit/)。
    *   此文件包含将在 PyPI 存储库上显示的包的描述。
*   创建这三个文件。

    `touch setup.py LICENSE README.md`

*   在编辑器中打开这些文件并更新它们。

    *   对于 setup.py 文件

```
import setuptools

with open("README.md", "r") as fh:
    long_description = fh.read()

setuptools.setup(
    name="demo_car",
    version="0.0.1",
    author="Umesh Kumar Dhakar",
    author_email="kumar886umesh@gmail.com",
    description="A demo_car package",
    long_description=long_description,
    long_description_content_type="text/markdown",
    url="https://github.com/umeshdhakar/sample-python-package",
    packages=setuptools.find_packages(),
    classifiers=[
        "Programming Language :: Python :: 3",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent",
    ],
) 
```

Enter fullscreen mode Exit fullscreen mode

*   对于许可证文件

```
MIT License

Copyright (c) [2019] [demo_car]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE. 
```

Enter fullscreen mode Exit fullscreen mode

*   对于自述文件

```
 ## Demo_car
This is a sample package to learn the steps of creating and publishing package. 
```

Enter fullscreen mode Exit fullscreen mode

*   现在我们已经准备好了所有必要的文件，所以不用等了，我们要打包它。

*   安装虚拟环境包。
    *(如果已经有则跳过这一步。)*
    `pip install virtualenv`

*   创建并激活环境。
    T0
    T1】

*   在环境中安装或更新 setuptools 和 wheel 软件包。
    `pip install --upgrade setuptools wheel`

    *setuptools 和 wheel 是使用 setup.py 文件构建
    包的包。*

现在一切就绪！😊，你就要创建包了，激动吗？😉

*   启动下面的命令来构建包。

    `python3 setup.py sdist bdist_wheel`

希望这个包已经编译好了，你可以在 setup.py 文件旁边的`dist`目录中看到这个包的压缩文件。做得好，我的朋友👍)，你创造了它。

* * *

**安装创建的包。**

*   在其他位置创建一个新环境并激活它。
    *(现在你知道怎么做了)。*

*   在新环境中复制 zip 文件。
    `pip install <package>`
    ( `pip install demo-car-0.1.tar.gz`

*运行* `pip list` *显示激活环境中已安装的软件包。你的包裹应该在这里登记。*

恭喜你！（😊)您已经安装了您的软件包。

* * *

是时候向 PyPI 发布这个包了，这样它就可以公开发布了。

*   我们需要安装 twine 包来上传它，但在此之前，请转到`setup.py`所在的路径，激活您为打包创建的第一个环境，然后启动安装或更新 twine 的命令。
    T1】

*   发送给 **PyPI** 的最终命令。
    `twine upload dist/*`

它会提示您输入帐户凭证，因此请输入您的用户名和密码，然后它会开始上传您的包。

**恭喜你！（🎉)亲爱的，你做到了。**
现在你的包应该可以在[PyPI](https://pypi.org/)T5 上得到([参考](https://packaging.python.org/tutorials/packaging-projects/))

感谢您的阅读😊。这些是我所知道的创建一个包的最基本的步骤。如果您想了解更多，请参考官方文档💡。
随时提问查询，我很乐意帮忙。你有没有发现我在这里漏掉的有用信息？那么请在下面评论。