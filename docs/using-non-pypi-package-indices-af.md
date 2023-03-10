# 使用非 PyPI 包索引

> 原文：<https://dev.to/alexbecker/using-non-pypi-package-indices-af>

默认情况下，像`pip`这样的 Python 工具从 Python 包索引 PyPI 安装包。然而，大多数工具允许您使用任何实现与 PyPI 相同的 API 的服务器(通常但不总是[简单存储库 API](https://www.python.org/dev/peps/pep-0503/) )。您可能希望使用替代包索引有两个主要原因:

*   镜像 PyPI 的索引允许您继续安装软件包，即使它们从 PyPI 中被删除或者 PyPI 离线。
*   如果不想将包公开，可以将包上传到私有索引而不是 PyPI。

如何配置您的工具取决于除了使用 PyPI 之外，您是否使用了另一个索引*来代替*或*。镜像可以以任何一种方式使用，而私有索引通常与 PyPI 一起使用。请注意， [PyDist](https://pydist.com) 或自托管的 [devpi](https://github.com/devpi/devpi) 实例可以充当镜像和私有索引。*

不幸的是，您需要单独配置每个工具。

## 配置`pip`

`pip`有两个配置选项，允许它使用替代索引:

*   *索引 URL* ，这是`pip`最初检查包的地方。默认为`https://pypi.org/simple`。
*   *额外的索引 url*，如果在索引 URL 中没有找到包，`pip`将在这里检查包。如果提供了多个，则按顺序检查它们。

如果您使用镜像，您可以将其设置为通过镜像安装所有软件包的索引，也可以将其设置为额外的索引，仅将其用作备份。如果你正在使用一个私有镜像来赞美 PyPI，那么把它作为一个额外的索引是很有诱惑力的。然而，如果在 PyPI 上发布了一个与你的私有包同名的包，这将导致`pip`安装那个包。因此，将私有索引的 URL 设置为索引并使用 PyPI 作为额外的索引是最安全的。

您可以用多种不同的方式配置`pip`；您应该选择哪一个取决于在您的基础设施中最容易设置什么，以及您希望该配置应用的范围有多广。

*   通过命令行参数`--index-url`和`--extra-index-url`
*   通过环境变量`PIP_INDEX_URL`和`PIP_EXTRA_INDEX_URL`(注意，这只能设置一次，所以您只能通过这种方式添加一个额外的索引)
*   通过`index-url`和`extra-index-url`设置在一个 [`pip.conf`文件](https://pip.pypa.io/en/stable/user_guide/#config-file)里面可以活:
    *   在你的虚拟环境的根目录下(例如`./venv`)
    *   在用户配置的`pip`子目录中(在 Linux 上通常是`~/.config/pip`)
    *   在您的系统级配置目录中(在 Linux 上是`/etc`)

## 配置`twine`

上传 Python 包的标准工具是`twine`。然而，只有一些
的竞争对手 python 索引支持用`twine`上传——据我所知，托管解决方案中只有 [PyDist](https://pydist.com) 和 [Gemfury](https://gemfury.com) 支持上传。其他索引通常会提供自己的 python 包进行部署。

有两种方法可以配置`twine`上传到 PyPI 以外的存储库:

*   通过命令行`--repository-url`参数
*   通过您主目录中的 [`.pypirc`文件](https://docs.python.org/3/distutils/packageindex.html#the-pypirc-file)

`.pypirc`文件应该类似于:

```
[distutils]
index-servers =
    pydist

[pydist]
repository: <index-url>
username: <username>
password: <password> 
```

如果您在`.pypirc`中包含多个`index-servers`，那么您可以在上传时将您给索引服务器起的名字(上面例子中的`pydist`)传递给`--repository`标志。`.pypirc`文件很方便，因为它不会提示你输入用户名/密码。

## 配置`pipenv`

Pipenv 可能是 Python 中最流行的依赖锁定工具。一些指南建议使用`PIP_INDEX_URL`和`PIP_EXTRA_INDEX_URL`环境变量来配置`pipenv`，就像你配置`pip`一样，但是这没有被正确处理，维护者[告诉我不要使用它](https://github.com/pypa/pipenv/issues/1285)。

相反，你可以使用`Pipfile` :
顶部的`[[source]]`部分

```
[[source]]
url = '<index-url>'
verify_ssl = true
name = 'pydist' 
```

如果你想使用多个包索引，你可以包含多个`[[source]]`部分——当`pipenv`找到包时，它会按照指定的顺序尝试它们，或者如果你声明了一个像`mypackage = { version="*", index="pydist" }`这样的包，它会首先尝试指定的索引。然而，`pipenv`对多个指标的处理是[目前漏洞百出的](https://github.com/pypa/pipenv/issues/2159)。