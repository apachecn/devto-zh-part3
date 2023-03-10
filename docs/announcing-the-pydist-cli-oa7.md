# 宣布 PyDist CLI

> 原文：<https://dev.to/alexbecker/announcing-the-pydist-cli-oa7>

[PyDist](https://pydist.com) 的卖点之一是它可以和标准的 Python 工具一起工作——你不需要安装任何东西就可以使用它。这对于保持容器化部署和 CI 基础设施的精简和快速非常有用。但是有时您只想做尽可能少的配置，而不必跟踪不同的 Python 工具和它们的怪癖。这就是为什么我创建了 [PyDist CLI](https://pypi.org/project/pydist-cli) ，它结合了`pip`和`twine`的功能，同时最大限度地减少了您需要做的配置量，并消除了它们的粗糙边缘。

要发布到 PyDist 并从 py dist 安装，您需要做的就是安装带有`pip install pydist-cli`的 CLI，并在第一次运行它时提供您的 API 密钥。然后你可以用`pydist publish`发布包到 PyDist，用`pydist install`从 PyDist 安装包。您还可以通过使用`--public`标志将它与 PyPI 一起使用。

PyDist CLI 比标准 Python 工具更加固执己见。默认情况下,`install`命令使用 PyDist，但是自动回退到 PyPI，这使得它比`pip install`更可靠。`publish`命令处理构建发行版(包括二进制和源代码)、检查自述文件和元数据格式、上传和清理。这适用于大多数包——并且避免了与`twine`相关的一些缺陷，比如从以前的版本中挑选`build/`目录中的工件。