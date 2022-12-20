# 诗意地打包您的 Python 项目

> 原文：<https://dev.to/hackersandslackers/poetically-packaging-your-python-project-5dom>

[![Poetically Packaging Your Python Project](img/bc7f598c7250ebda10a7ad1a6261d56e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Hgo_spo0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res-5.cloudinary.com/hackers-and-slackers/image/upload/f_auto%2Cq_auto/img/poetry4.jpg)

不久前，我们黑客还在为 Pipenv 唱赞歌:Python 当时看似卓越的依赖管理器。虽然我们心中充满了爱，但有时还是有爱存在。我们只是碰巧是天气爱好者，这提醒了我:Pipenv 最近为我*做了什么*？

正如你可能已经猜到的(考虑到它是一个软件)，没什么。嗯，有一次 pip 从`v.18`升级到`v.18.1`，这完全破坏了 Pipenv，几乎没有任何确认(据我所知，这可能仍然会被破坏)。当我们的生命似乎要消逝时，一个奇迹从 heans 出现了:一个年轻、聪明、有吸引力的 Pipenv 的替代品，它一直在我耳边低语，并向世界承诺。她的名字叫 [**诗**](https://poetry.eustace.io/) 。

# 那边的 GitHub 发出了什么光？

诗意来源于一种真正的挫败感，这种挫败感不仅来自于管理 Python 中的环境和依赖项，还来自于这样一个事实，即即使解决了这个问题(尽管很糟糕),仍然不能解决创建体面的 Python 项目时需要完成的相关任务。考虑节点的`package.json`:包含项目元数据、产品依赖、开发依赖、联系信息等的单个文件。相反，Python 项目通常附带以下内容:

### Setup.py

如果你以前从未费心将一个包发布到 PyPI，那么很有可能你不太熟悉`setup.py`的一些细微差别，或者你为什么要费心创建一个包。这是一种失败的心态:我们应该假设我们构建的大部分(或一些)东西有一天会变得足够有用，可以分发。

因此，我们得到了这个怪物:

```
from setuptools import setup, find_packages, tests_require, packages, name

with open("README", 'r') as f:
    long_description = f.read()

setup = (
    name='Fake Project',
    version='1.0',
    description='A fake project used for example purposes.',
    long_description=long_description,
    author='Todd Birchard',
    author_email='todd@hackersandslackers.com',
    maintainer='Some Loser',
    maintainer_email='some.loser@example.com,
    url="https://github.com/toddbirchard/fake-project",
    license='MIT',
    include_package_data=True,
    package_dir={'application'}
    packages=['distutils', 'modules'],
    tests_require=["pytest"],
    cmdclass={"pytest": PyTest},
    classifiers=[
          'Development Status :: 2 - Beta',
          'Environment :: Console',
          'Environment :: Web Environment',
          'Intended Audience :: End Users/Desktop',
          'Intended Audience :: Developers',
          'Intended Audience :: System Administrators',
          'License :: OSI Approved :: Python Software Foundation License',
          'Operating System :: MacOS :: MacOS X',
          'Operating System :: Microsoft :: Windows',
          'Operating System :: POSIX',
          'Programming Language :: Python',
          'Topic :: Communications :: Email',
          'Topic :: Office/Business',
          'Topic :: Software Development :: Bug Tracking',
          ],
) 
```

许多元数据字段都是不言自明的。但是与包依赖关系相关的字段呢，比如 package_dir 或者 packages？这不是已经在我们的 Pipfile 中处理过了吗？最重要的是，我们需要通过 **tests_require** 和 **cmdclass** 来指定我们正在使用的测试套件。简而言之:差不多。

### Setup.cfg

与`setup.py`真正的笑话是，它需要自己的配置文件:是的，一个配置文件给你的配置文件。`setup.cfg`顾名思义，为`setup.py`中提到的事情设置了更细粒度的配置，比如 **pytest** 应该如何处理等等。我们不深入讨论，但这里有一个例子:

```
[coverage:run]
omit = */test/*

[flake8]
exclude = *.egg*,.env,.git,.tox,_*,build*,dist*,venv*,python2/,python3/
ignore = E261,W503
max-line-length = 121

[tool:pytest]
minversion = 3.2
addopts =
  # --fulltrace
  # -n auto
  --cov-config=setup.cfg
  --cov=httplib2
  --noconftest
  --showlocals
  --strict
  --tb=short
  --timeout=17
  --verbose
  -ra 
```

### Pipfile 和 Pipfile.lock

如果您一直在使用 Pipenv，您会发现这些文件负责设置您的 Python 版本和依赖项。但是等等，我们不是也需要在 setup.py 中指定依赖关系吗？是的，我们做到了。没有上帝，但如果有，他可能会恨你。下面是创建一个可接受的 Pipfile 需要做的所有工作:

```
[[source]]
url = "https://pypi.org/simple"
verify_ssl = true
name = "pypi"

[packages]
Flask-SQLAlchemy = "*"
psycopg2 = "*"
psycopg2-binary = "*"
requests = "*"
configparser="*"
mapbox="*"
flask="*"
pandas="*"
Flask-Assets="*"
libsass="*"
jsmin="*"
dash_core_components="*"
dash-table="*"
dash_html_components="*"
dash="*"
flask-session="*"
flask-redis="*"
gunicorn="*"
pytest-flask="*"

[dev-packages]

[requires]
python_version = "3.7.1" 
```

但是等等，还有呢！

### 需求. txt

因为 Pipfile 格式还没有被采纳为依赖关系管理的标准，如果我们想把我们的应用程序部署到像 **Google App Engine** 或诸如此类的主机上，我们*仍然*需要创建一个 requirements.txt 文件。所以现在我们也要对付这个石器时代的丑陋的狗娘养的:

```
atomicwrites==1.2.1
attrs==18.2.0
boto3==1.9.75
botocore==1.12.75
CacheControl==0.12.5
certifi==2018.11.29
chardet==3.0.4
Click==7.0
configparser==3.5.0
dash==0.35.1
dash-core-components==0.42.0
dash-html-components==0.13.4
dash-renderer==0.16.1
dash-table==3.1.11
decorator==4.3.0
docutils==0.14
Flask==1.0.2
Flask-Assets==0.12
Flask-Compress==1.4.0
Flask-Redis==0.3.0
Flask-Session==0.3.1
Flask-SQLAlchemy==2.3.2
gunicorn==19.9.0
idna==2.8
ipython-genutils==0.2.0
iso3166==0.9
itsdangerous==1.1.0
Jinja2==2.10
jmespath==0.9.3
jsmin==2.2.2
jsonschema==2.6.0
jupyter-core==4.4.0
libsass==0.17.0
mapbox==0.17.2
MarkupSafe==1.1.0
more-itertools==5.0.0
msgpack==0.6.0
nbformat==4.4.0
numpy==1.15.4
pandas==0.23.4
plotly==3.5.0
pluggy==0.8.0
polyline==1.3.2
psycopg2==2.7.6.1
psycopg2-binary==2.7.6.1
py==1.7.0
pytest==4.1.0
pytest-flask==0.14.0
python-dateutil==2.7.5
pytz==2018.9
redis==3.0.1
requests==2.21.0
retrying==1.3.3
s3transfer==0.1.13
six==1.12.0
SQLAlchemy==1.2.15
traitlets==4.3.2
uritemplate==3.0.0
urllib3==1.24.1
webassets==0.12.1
Werkzeug==0.14.1 
```

### 载货单

是的，还有更多。如果你现在不介意，请马上离开这个博客。对于喜欢不必要的复杂性的白领来说，就业市场已经成熟。在机器人接管之前，这个博客是给人类的。

无论如何，有一个完整的文件专门用来包含你的项目中的非代码文件。我们正进入一个滑稽可笑的领域:

```
include README.rst
include docs/*.txt
include funniest/data.json 
```

## 是鸟！是一架飞机！它的...一个复杂的配置文件？

我希望你在回顾了我们年复一年放任不管的所有事情后彻底生气，告诉自己这种拼凑的标准很好。提示我们的英雄:诗歌的创造者:

> Python 中的打包系统和依赖性管理相当复杂，新手很难理解。即使对于经验丰富的开发人员来说，创建一个 Python 项目中需要的所有文件有时也很麻烦:`setup.py`、`requirements.txt`、`setup.cfg`、`MANIFEST.in`和新添加的`Pipfile`。所以我想要一个工具，将所有事情限制在一个配置文件中完成:依赖管理、打包和发布。

哦，上帝，是的，但是怎么做呢？！？！

### 【pyproject.toml 简介

诗歌是围绕一个被称为`pyproject.toml`的单一配置构建的，这个配置已经通过 **PEP 518 成为了[Python 社区](https://www.python.org/dev/peps/pep-0518/)中被接受的标准。**鉴于 Python 开发社区本身的重要性，可以有把握地说这不是另一种时尚，值得使用。

这里有一个例子**。来自[诗歌 Github 库的 toml](https://github.com/sdispater/poetry)** 文件:

```
[tool.poetry]
name = "my-package"
version = "0.1.0"
description = "The description of the package"

license = "MIT"

authors = [
    "Sébastien Eustace <sebastien@eustace.io>"
]

readme = 'README.md' # Markdown files are supported

repository = "https://github.com/sdispater/poetry"
homepage = "https://github.com/sdispater/poetry"

keywords = ['packaging', 'poetry']

[tool.poetry.dependencies]
python = "~2.7 || ^3.2" # Compatible python versions must be declared here
toml = "^0.9"
# Dependencies with extras
requests = { version = "^2.13", extras = ["security"] }
# Python specific dependencies with prereleases allowed
pathlib2 = { version = "^2.2", python = "~2.7", allows-prereleases = true }
# Git dependencies
cleo = { git = "https://github.com/sdispater/cleo.git", branch = "master" }

# Optional dependencies (extras)
pendulum = { version = "^1.4", optional = true }

[tool.poetry.dev-dependencies]
pytest = "^3.0"
pytest-cov = "^2.4"

[tool.poetry.scripts]
my-script = 'my_package:main' 
```

除了涵盖前面提到的所有文件的范围之外，将 **pyproject.toml** 用于诗歌还包括:

*   自动填充**从`.gitignore`中找到的值中排除**部分
*   在最终的 PyPi 包中添加了一个**关键字**部分
*   支持使用任何语法的版本号，例如**通配符(*)** 或**胡萝卜( <sup>1.0.0)</sup>** 语法
*   虚拟环境的自动检测，因此可以在 envs 中使用全局安装

## 创造诗艺

我们都准备好了吗？右图:让我们永远改变我们的工作流程。

### 安装

要在 OSX 上安装诗歌，请使用以下命令:

```
$ curl -sSL https://raw.githubusercontent.com/sdispater/poetry/master/get-poetry.py | python 
```

这将为您的`~/.bash_profile`创建一个附件。重新启动终端并验证安装:

```
$ poetry --version
Poetry 0.12.10 
```

### 创建新的 Python 项目

导航到您希望新项目称为 home 的任何文件路径。要开始，我们接下来需要的是以下命令:

```
poetry new my-package 
```

准备好呼吸新鲜空气了吗？这个命令为您生成一个基本的项目结构——与 Node 或其他类似的生成器相比，这是 Python 中很长时间以来所缺少的。最终的项目结构如下所示:

```
my-package
├── pyproject.toml
├── README.rst
├── my_package
│ └── __init__.py
└── tests
    ├── __init__.py
    └── test_my_package 
```

在这里发生的美好事情中，唯一我们还没有触及的是诗歌与 **pytest** 的内置集成。哦，快乐的一天！

#### 替代交互式安装方法

如果您喜欢更多的帮助，可以在一个空目录中随意使用`poetry init`(或者在一个没有现有**的目录中)。toml** 文件)要走过的创建过程:

```
$ poetry init

This command will guide you through creating your pyproject.toml config.

Package name [my-package]: Great Package
Version [0.1.0]:
Description []: Great package for great people.
Author [Todd Birchard <todd@hackersandslackers.com>, n to skip]:
License []: MIT
Compatible Python versions [^2.7]: ^3.7

Would you like to define your dependencies (require) interactively? (yes/no) [yes] no

Would you like to define your dev dependencies (require-dev) interactively (yes/no) [yes] no

Generated file

[tool.poetry]
name = "Great Package"
version = "0.1.0"
description = "Great package for great people."
authors = ["Todd Birchard <todd@hackersandslackers.com>"]
license = "MIT"

[tool.poetry.dependencies]
python = "^3.7"

[tool.poetry.dev-dependencies]

[build-system]
requires = ["poetry>=0.12"]
build-backend = "poetry.masonry.api"

Do you confirm generation? (yes/no) [yes] yes 
```

## 管理 pyproject.toml 中的依赖关系

如果您熟悉 Pipfiles，pyproject.toml 以同样的方式处理依赖关系。请记住，`poetry install`会安装您列出的依赖项，而`poetry update`会将*诗歌中的依赖项更新为最新版本。*

### 继续我任性的儿子

我可以花一整天的时间从诗歌 Github 页面复制粘贴一般用法，但是我想我在这里的工作已经完成了。帮自己一个忙[看看 Github repo](https://github.com/sdispater/poetry) 让你的生活永远轻松。或者至少直到下一个替代解决方案出现。