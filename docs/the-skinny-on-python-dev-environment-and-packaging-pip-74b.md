# Python 开发环境和打包(pip)概述

> 原文：<https://dev.to/cocoroutine/the-skinny-on-python-dev-environment-and-packaging-pip-74b>

欢迎来到 Python dev 的狂野世界。没有什么是一致的，也没有共识这回事。

别担心，我会做你的向导。

## 背景

我悄悄地发布了[py 绗缝](http://github.com/cocoroutine/pyquilted)，它生成了来自 Yaml 的 PDF 格式的漂亮简历。我学到了很多关于设置 python 开发环境和打包应用程序以在 Pypi (pip)上分发的知识，现在我正在分享这些知识。

你可以在这里找到更多关于绗缝简历的信息。

## 单元测试

出于某种原因，运行测试用例比它应该的要难。遵循这些步骤，让你的生活更轻松。

在项目根目录下创建一个名为“test”的目录。

将所有测试命名为' test_* '。

放一个 __init__。py，这样目录就可以被识别为一个包。

在 Python 2 和 3 上运行所有测试
`python -m unittest discover`

运行一个测试
`python -m unittest test.test_name`

## Venv vs VirtualEnv vs PipEnv

这些都是隔离您的 python pip 环境的方法，因此您不会污染您的主系统 python。卸载一个包和它的依赖项是 pip 中的痛苦。这通过允许您轻松地删除和创建新的 env 来避免这种情况。

#### 但是你跟哪个走？

venv~~vs VirtualEnv vs PipEnv~~

跟小文走吧。python 标配，3.3+以后正式推荐。它比 virtualenv 有更少的陷阱，我不想被抓住。

创建新的 venv 文件夹
`python -m venv venv`

加载环境
`source venv/bin/activate`

关闭 venv
`deactivate`

Pipvenv 很好，但它不是标准的，是基于 virtualenv 的。

#### Python 2.7 和 Venv

Venv 不支持 python 2.7，扫兴。

简单解法
`pip install py2venv`

这将为您安装 virutalenv 并创建一个 virtualenv 包装器。这意味着您可以使用 venv 命令。太好了！

#### 你的 venv 文件夹放在哪里？

如果您只是为每个项目运行一个 venv，那么将它放在与项目根目录相同的目录中，并将文件夹命名为‘venv’。确保在你的。gitignore 您添加了“venv”——在 github 和 gitlab 上，这是为您添加的。

每个项目一个 venv 环境
`project_root/venv`

当您想要为正在测试的不同 python 版本使用不同的 venv 环境时，问题就来了。这里有几个选择

每个项目多个 venv
`project_root/venv/{{python_version}}`

或者，主文件夹
`~/venv/{{project_name}}_{{python_version}}`中的 venv

## setup.py

Setup.py 就像是你的应用程序的 makefile。它基于 setuptools。它通常是预装的。如果没有，你可以这样安装它

`pip install setuptools`

文档可以在这里找到[。有些部分要么令人困惑，要么不能按预期工作。我在这里记下了零件。](https://setuptools.readthedocs.io/en/latest/)

你可以在 py 绗缝[源](https://github.com/cocoroutine/pyquilted)中看到我的 setup.py 的例子

#### 命令行输入点

您希望在终端中使用您的应用程序的昵称运行您的应用程序。这比想象中要棘手得多。经过无休止的寻找，下面的这个方法对我有效。

在项目根目录下，创建一个名为 main.py 的文件。这里的一个例子。

将以下内容添加到您的设置中。py

```
entry_points={
    'console_scripts': ['command=pacakge.main:main']
} 
```

其中“command”是您要使用的命令。“包”是您的包名。

例如我的 py 绗缝入口点
`'console_scripts': ['pyquilted=pyquilted.main:main']`

#### 降价自述

Pypi 现在支持 markdown。使用 with 打开文件处理程序将自述文件设置为变量。

```
long_description=readme,
long_description_content_type='text/markdown' 
```

#### 包括非 py 文件

有两种方法可以将文件添加到数据的 site_packages 或者测试用例的源包中。

#### 包数据

使用以下格式将数据文件添加到您的 site_packages 中。

```
package_data={
    'package_name': ['directory/*.pattern']
},
include_package_data=True 
```

#### 载货单

这包括不在 site_packages 文件夹中的源文件包中的文件。格式有点怪。

```
include file   # include single file
recursive-include directory pattern # include a directory and files 
```

## 需求. txt

跳过它。这不是真的必要，但制作简单。

## 输入 Pyenv

还在为从 Python 2 迁移到 3 的灾难而哭泣。我也是，加入俱乐部。我们仍然必须支持 Python 2，并且测试其他版本的 Python 3。如何解决多个 python 版本？我们可以学流浪汉或码头工人。不，那太过分了。

解决方案 Pyenv。

按照此处的说明安装 Pyenv[Pyenv](https://github.com/pyenv/pyenv)。

列出可安装的 python 版本
`pyenv install --list`

安装版本
`pyenv install 2.7.15`

列出安装的版本
`pyenv versions`

切换到 python 版本
`pyenv global 2.7.15`

#### Debian、Ubuntu 等

在 Debian、Ubuntu 和其他发行版上，当你试图构建旧版本的 python 时，你会得到一个关于 ssl 的错误。问题是 libssl 1.1 与 Python < 3.3\. The solution is to download the dev headers for libssl 1.0 and link them manually when running pyenv install, per this [线程](https://github.com/pyenv/pyenv/issues/945#issuecomment-409627448)不兼容。

```
CFLAGS="-I/path_to/libssl1.0-dev/include -I/path_to/libssl1.0-dev/include/x86_64-linux-gnu" \
LDFLAGS="-L/path_to/libssl1.0-dev/lib/x86_64-linux-gnu" pyenv install 2.7.15 
```

## 到处都是鸡蛋

为了阻止 python 创建您在测试时意外上传到 Pypi 的 eggs。

使用下面的命令
`pip install .`

代替这个命令

## 建筑和车轮

老实说，车轮文件是没有必要的。源代码和二进制文件的安装时间相差几秒钟。然而，这真的很容易建立二进制车轮，为什么不呢？

确保您已经安装了车轮
`pip install wheel`

运行这个来构建你的包
`python setup.py sdist bdist_wheel`

## 最后上传到 Pypi

在 Pypi.org 上创建一个帐户。然后安装 twine，这是 pypi 的官方应用程序
`pip install twine`

用此命令
上传`twine upload --skip-existing dist/*`

关键是跳过现有的文件，否则当上传带有现有文件的 dist 文件夹时会出错。

## 恭喜恭喜！

恭喜你打败了最后一个老板！为最终破译 python 开发环境而感到自豪。