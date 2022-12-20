# 新的 Python 依赖管理工具“诗歌”初学者指南

> 原文：<https://dev.to/yukinagae/beginner-guide-on-poetry-new-python-dependency-management-tool-4327>

# 我为什么写这个帖子？

有一次我写了一篇关于 Python 包管理工具之一“pipenv”的[博文](https://dev.to/yukinagae/your-first-guide-to-getting-started-with-pipenv-50bn)， [Dylan](https://dev.to/dbanty) 在帖子上给了我一条[评论](https://dev.to/dbanty/comment/a6h9)。

他指出了 pipenv 上的一些问题，他的建议是用[诗](https://poetry.eustace.io)代替 pipenv。

在这篇文章中，我将简要介绍什么是诗歌以及它的一些用法。这只是第一次尝试使用诗歌，希望我在以后的博客中写更多的细节:)

# 什么是“诗”，为什么？

*poem*是一个 Python 依赖管理工具。

诗歌发展的主要原因在这里提到[。](https://github.com/sdispater/poetry#why)

> Python 中的打包系统和依赖性管理相当复杂，新手很难理解。即使对于经验丰富的开发人员来说，创建 Python 项目中需要的所有文件有时也很麻烦:setup.py、requirements.txt、setup.cfg、MANIFEST.in 和新添加的 Pipfile。

的确，我们应该考虑许多文件，例如:

*   setup.py
*   requirements.txt
*   setup.cfg
*   MANIFEST.in
*   Pipfile 和 Pipfile.lock (pipenv)

为了解决这种混乱的情况，poem 只需要一个`pyproject.toml`文件来管理所有的依赖关系。

现在，让我们开始写诗吧！

# 设置诗歌

## 要求

*   Python 2.7 或 3.4 以上版本

在这篇文章中，我将使用 Python 3.6.0。

```
$ python --version
Python 3.6.0 
```

Enter fullscreen mode Exit fullscreen mode

## 安装

通过提供的安装程序安装诗歌。

```
$ curl -sSL https://raw.githubusercontent.com/sdispater/poetry/master/get-poetry.py | python
Retrieving Poetry metadata

# Welcome to Poetry!

This will download and install the latest version of Poetry,
a dependency and package manager for Python.

It will add the `poetry` command to Poetry's bin directory, located at:

$HOME/.poetry/bin

This path will then be added to your `PATH` environment variable by
modifying the profile files located at:

$HOME/.profile
$HOME/.bash_profile

You can uninstall at any time with `poetry self:uninstall`,
or by executing this script with the --uninstall option,
and these changes will be reverted.

Installing version: 0.12.12
  - Downloading poetry-0.12.12-darwin.tar.gz (7.23MB)

Poetry (0.12.12) is installed now. Great!

To get started you need Poetry's bin directory ($HOME/.poetry/bin) in your `PATH`
environment variable. Next time you log in this will be done automatically.

To configure your current shell run `source $HOME/.poetry/env` 
```

Enter fullscreen mode Exit fullscreen mode

要激活`poetry`命令，运行以下命令:

```
source $HOME/.poetry/env 
```

Enter fullscreen mode Exit fullscreen mode

现在，`poetry`命令应该可用了。让我们检查一下安装的诗歌版本。

```
$ poetry —version
Poetry 0.12.12 
```

Enter fullscreen mode Exit fullscreen mode

有用！

# 诗歌演示

## 创建模板

首先，我将创建一个演示应用程序。

```
poetry new poetry-demo 
```

Enter fullscreen mode Exit fullscreen mode

项目结构是这样的。

```
$ cd poetry-demo
$ tree
.
├── README.rst
├── poetry_demo
│   └── __init__.py
├── pyproject.toml
└── tests
    ├── __init__.py
    └── test_poetry_demo.py 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看`pyproject.toml`。

```
[tool.poetry]
name = "poetry-demo"
version = "0.1.0"
description = ""
authors = ["[Your Name] <[Your Email]>"]

[tool.poetry.dependencies]
python = "^3.6"

[tool.poetry.dev-dependencies]
pytest = "^3.0"

[build-system]
requires = ["poetry>=0.12"]
build-backend = "poetry.masonry.api" 
```

Enter fullscreen mode Exit fullscreen mode

## 添加依赖关系

我可以直接在`pyproject.toml`文件中指定一个依赖项，但是使用`add`命令看起来更容易。

```
poetry add pendulum 
```

Enter fullscreen mode Exit fullscreen mode

自动地，`pendulum`被添加到`pyproject.toml`文件中。

```
[tool.poetry.dependencies]
python = "^3.6"
pendulum = "^2.0" 
```

Enter fullscreen mode Exit fullscreen mode

同样，`poetry.lock`被创建。

```
$ tree
.
├── README.rst
├── poetry.lock
├── poetry_demo
│   └── __init__.py
├── pyproject.toml
└── tests
    ├── __init__.py
    └── test_poetry_demo.py 
```

Enter fullscreen mode Exit fullscreen mode

由于这只是第一次尝试使用诗歌，我会继续使用它，如果我发现有用的东西，可能会再次写博客:)

# 待办事宜

有几件事我想试试:

*   [ ]如何与现有需求整合. txt？
*   [ ]尝试诗歌`publish`命令将我的包发布到 PyPI
*   [ ]比较诗歌和 pipenv

# 参考文献

*   诗意
    *   诗歌——Python 依赖管理和打包变得简单。:官方文件
    *   sdispater/poems:Python 依赖管理和打包变得简单。 : GitHub
*   [hy nek Schlawack 2018 首页 Python 应用依赖管理](https://hynek.me/articles/python-app-deps-2018/)