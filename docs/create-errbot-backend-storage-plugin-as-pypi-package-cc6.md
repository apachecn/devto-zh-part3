# 将 Errbot 后端/存储插件创建为 PyPI 包

> 原文：<https://dev.to/attakei/create-errbot-backend-storage-plugin-as-pypi-package-cc6>

在开发一些 Errbot 插件时，我认为存储和后端插件最好打包管理(如果可能的话，应该在 PyPI 注册)

# 1。在自举中，Errbot 不安装后端和后端的依赖关系。

要运行 bot，我调用`errbot`命令。在这个命令中，按照存储、后端和 bot 插件的顺序加载插件。
当引导程序加载存储和后端插件时，它不会检查依赖性。

实际上一些插件允许通过命令行安装依赖项。

例如:

*   [https://github.com/errbotio/errbot-backend-skype](https://github.com/errbotio/errbot-backend-skype)
*   [https://github.com/Vaelor/errbot-mattermost-backend](https://github.com/Vaelor/errbot-mattermost-backend)

如果插件有依赖关系，需要手动安装，我认为最好定义为模块和包。

# 2。在运行中，Errbot 不会动态改变存储和后端。

Errbot 通常作为守护程序运行。由于这个原因，存储和后端插件在运行中几乎不会改变。

因此，这些可以作为需求与 Errbot 核心一起管理。
如果这样，`pip install -r requirements.txt`在您的本地存储库中可以安装所有的依赖项。

例:(这些插件没有在 PyPI 中注册)

```
errbot
errbot-backend-gitter
errbot-storage-firebase 
```

# 创建为项目

## 文件列表

项目结构与标准 python 包相同。

*   `setup.py`
*   `setup.cfg`
*   `README.rst`
*   `MANIFEST.in`
*   `errbot_backend_demo`
    *   `__init__.py`
    *   `demo.py`
    *   `demo.plug`

## 著述

### `setup.py`

它可以简单地写。

```
#!/usr/bin/env python from setuptools import setup

setup() 
```

### `setup.cfg`

目前所有元数据都可以写入 setup.cfg，
请定义`options.package_data`段和`include_package_data`键来包含。将文件作为安装资源。

```
[metadata]
name = errbot-backend-demo
version = attr:errbot_backend_demo.__version__
author = Kazuya Takei
author_email = attakei@example.com
description = Demo backend plugin for Errbot
long_description = file:README.rst

[options]
install_requires = YOUR_DEPENDENCIES
packages = find:
include_package_data = True

[options.package_data]
errbot_backend_demo = *.plug 
```

### `LICENSE.rst`

正常书写。

### `MANIFEST.in`

同`setup.cfg`，定义。作为包含文件插入。

```
include errbot_backend_demo/*.plug 
```

### `__init__.py`

在此定义包的版本。并定义函数返回安装路径的路径。

```
from pathlib import Path

__version__ = '0.0.1-alpha.1'

def get_plugin_dir() -> str:
    module_dir = Path(__file__).parent
    return str(module_dir) 
```

### `demo.py`和`demo.plug`

正常编写为插件。

# 在 Errbot config.py 中使用

如果插件被这些定义为包，你可以写 config.py 来使用插件。

```
import errbot_backend_demo

BOT_EXTRA_BACKEND_DIR = errbot_backend_demo.get_plugin_dir()
BACKEND = 'Demo' 
```