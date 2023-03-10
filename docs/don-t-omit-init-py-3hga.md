# 不要省略 __init__。巴拉圭

> 原文：<https://dev.to/methane/don-t-omit-init-py-3hga>

我看到一些人认为 Python 3 允许在 Stackoverflow 或邮件列表上没有`__init__.py`的包。

这是误解。没有`__init__.py`的包目录是“命名空间包”，不是常规包。参考参见 [PEP 420](https://www.python.org/dev/peps/pep-0420/) 。

名称空间包是为创建复杂的“分发包”(可以在 PyPI 上注册的“包”)的人准备的特殊包。“Python 打包用户指南”中有名称空间包的[指南。](https://packaging.python.org/guides/packaging-namespace-packages/)

名称空间包和常规包之间有一些区别。

例如，标准库中的`unittest`模块在没有`__init__.py`的情况下不会搜索目录。如果他们这样做了，他们可能要花十几秒钟在包含数百万个文件和子目录的`node_modules`中搜索测试。

除非你 100%确定“名称空间包”是什么，否则你不应该在你的项目中省略`__init__.py`。用带`__init__.py`的“常规包”代替。