# 配置. pypirc 文件以简化 Python 打包

> 原文：<https://dev.to/brodan/configuring-a-pypirc-file-for-easier-python-packaging-2d50>

PyPI 是 Python 包索引。它的目的是帮助 Python 开发者找到并安装由 Python 社区开发的软件。

我最近构建了我的第一个 Python 包，[pattern](https://github.com/brodan/patter)，并通过 PyPI 公开发布了它。在这个过程中，我遇到了一些小问题，所以我写这篇文章来帮助那些处于类似情况的人。

这篇文章将描述. pypirc 文件的基础知识以及如何配置和保护它。我最初为 Truveris 工程博客写了这篇文章，现在我把它重新发布在这里，以便获得更多的关注。

# 入门

在继续之前，最好确保`setuptools`和`wheel`库是最新的。如果需要，下面的命令将更新它们:

```
$ pip install -U setuptools wheel 
```

Enter fullscreen mode Exit fullscreen mode

这篇文章将假设您有一个新的 Python 库，可以发布了。源代码应该使用如下所示的命令打包。根据软件包的需要，您的命令可能会略有不同。

```
$ python setup.py sdist bdist_wheel 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多关于创建可分发 Python 包的信息，请参见这些文档。

在下一节中，我将使用 [twine](https://github.com/pypa/twine) 工具来帮助发布我的新包。你可以在这里阅读使用`twine`优于内置打包工具[的好处。使用以下命令安装【T1:](https://github.com/pypa/twine#why-should-i-use-this) 

```
$ pip install twine 
```

Enter fullscreen mode Exit fullscreen mode

# 这个。pypirc 文件

使用`.pypirc`文件有两个主要好处:

1.  它消除了推送 PyPI 时输入用户名/密码的需要。
2.  它简化了将包推送到非默认包存储库(即除 pypi.org 之外的任何地方)时命令行的使用。

关于`.pypirc`文件的官方文档可以在这里[找到。下面可以看到我的`.pypirc`文件的内容。该文件必须放在`$HOME/.pypirc`中，以便 pip/twine 使用。](https://docs.python.org/3/distutils/packageindex.html#the-pypirc-file) 

```
 [distutils]
    index-servers=
        pypi
        testpypi

    [pypi]
    username: brodan
    password: xxxxxxxxxxxxxxxx

    [testpypi]
    repository: https://test.pypi.org/legacy/
    username: brodan
    password: yyyyyyyyyyyyyyyy 
```

Enter fullscreen mode Exit fullscreen mode

请记住，[pypi.org](https://pypi.org/)和[test.pypi.org](https://test.pypi.org/)没有集成，所以你需要在每个站点上创建一个单独的帐户。

上面要注意的一点是，`[pypi]`部分没有配置`repository`，但是`testpypi`部分有。这是因为`repository`变量默认为`https://upload.pypi.org/legacy/`，所以不需要包含在该部分中。

# 上传 Python 包

一旦上面的文件就位，`--repository`标志现在可以和`twine`一起使用来指定你的包将被上传到哪个包存储库:

如果您希望将包上传到 TestPyPI 存储库，应该使用下面的命令:

```
$ twine upload --repository testpypi dist/* 
```

Enter fullscreen mode Exit fullscreen mode

类似地，一旦包准备好向公众发布，应该使用以下内容:

```
$ twine upload --repository pypi dist/* 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在运行上述命令时，不会提示您输入密码。您也不再需要将存储库 URL 复制并粘贴到终端中。

# 固若金汤。pypirc 文件

由于`.pypirc`文件以纯文本形式存储敏感信息(如密码)，因此相应地设置该文件的权限非常重要，这样系统上的其他用户就不能访问该文件。

为此，运行以下命令:

```
$ chmod 600 ~/.pypirc 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将确保只有文件所有者(应该是您自己的用户)可以读写该文件。关于 UNIX 中文件权限的更多信息可以在[这里](https://www.tutorialspoint.com/unix/unix-file-permission.htm)找到。感谢[这位 StackOverflow answer](https://unix.stackexchange.com/questions/20776/how-do-you-protect-a-plain-text-credentials-file-with-the-username-and-password/20779#20779) 对这一部分的帮助。

# 包装完毕

有了一个`.pypirc`文件，将 Python 包推送到公共存储库的过程就容易多了。

如果你对这篇文章有任何问题或反馈，请通过[电子邮件](//mailto:christopher.hranj@gmail.com)或[推特](https://twitter.com/brodan_)联系我。感谢阅读！