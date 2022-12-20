# 富有诗意的 Python

> 原文：<https://dev.to/dbanty/productive-python-with-poetry-67h>

> 这是解释管理 Python 依赖关系的不同选项的系列文章的一部分。确保你已经阅读了之前的文章，这篇文章将基于这些信息。

# TL；速度三角形定位法(dead reckoning)

诗歌通过提供更好的开发体验解决了我对 pipenv 和 T2 香草栈的许多抱怨。不利的一面是，它是一个更新的工具，仍然在开发一些重要的功能，而且它没有得到 [Python 包管理机构(PyPA)](https://www.pypa.io/en/latest/) 的支持，这导致了一些冲突。

# 组件

*   [诗歌](https://poetry.eustace.io/):用于管理依赖关系、虚拟环境、构建和发布你的项目。
*   pyproject.toml :包含所有的项目元数据
*   poetry.lock:一个用于同步环境间依赖关系的精确版本的文件
*   [pip](https://pypi.org/project/pip/) :用于一些配电情况

# 发展

## TL；速度三角形定位法(dead reckoning)

到目前为止，诗歌提供了所讨论的三种堆栈中最好的开发人员体验。

## 定义需求

*   👍开发需求:就像 Pipenv 一样，你可以很容易地在同一个文件中声明开发人员的依赖关系，就像声明其他依赖关系一样。
*   😍再现性:Python 包的最明智的管理。诗歌将安装从你的锁文件，它只更新你的依赖，当你告诉它。
*   ✅增加新的软件包:这是完全应该做的。如果你这样做了`poetry add somepackage`，它将获得 somepackage 的最新版本，并根据[语义版本](https://semver.org/)将约束设置为兼容。因此，如果最新版本是 1.4.8，您的约束是^1.4，这意味着它可以更新到 1.4.9 或 1.5，但不能更新到 2.0。
*   😊替代来源:甚至比 Pipenv 更容易。将您的源代码添加到 pyproject.toml，并使用`poetry config`设置您的凭证。这比试图让环境变量跨平台工作要容易得多。

## 虚拟环境

*   😌设置:轻而易举。如果虚拟环境不存在，则创建一个虚拟环境，比 Pipenv 快得多。一个小小的折衷是，poem 支持一个项目的多个 Python 版本，这意味着您必须指定在任何给定的时间点使用哪个版本。推荐使用 [pyenv](https://github.com/pyenv/pyenv) on -nix 系统。我发现 Windows 的内置 [py](https://www.python.org/dev/peps/pep-0397/) 启动器也很好用。
*   🙂用法:几乎和诗歌一样的故事，虽然有更多的铃铛和哨子。
    *   安装要求:`poetry install`
    *   跑步:`poetry run python [your script]`
    *   激活环境:`poetry shell`(同时产生一个新的外壳)
    *   停用:`exit`

# 分布

## TL；速度三角形定位法(dead reckoning)

诗歌几乎使一切都比 Pipenv 更好。如果你做的是源代码或者预构建的发行版，你的情况会稍微差一点。

## 打造

*   🥰定义:您的 [pyproject.toml](https://poetry.eustace.io/docs/pyproject/) 定义了您的依赖项，也定义了您的项目的所有其他元数据！没有 setup.py 了。
*   😁建筑:`poetry build`。不利方面？只支持纯 Python 轮子，还没有 C 代码。

## 部署

*   `poetry publish`的一个角
*   👌‍源代码发行版:你必须在你的服务器上安装诗歌，但它的开销比 Pipenv 少得多，所以我认为它是值得的。
*   😥预构建的依赖:诗歌的发布版本没有办法做到这一点。我今天的解决方案是使用诗歌的 Alpha 版本(我对此没有异议),并导出一个 requirements.txt 来与 [pip](https://pypi.org/project/pip/) 一起使用

# 结论

诗歌太棒了！它还很年轻(仍在向 1.0 迈进)，但已经比现有的工具要好了。考虑到它的发展方向、支持它的社区以及友好和支持的维护者，我认为它应该是每个人选择的 Python 依赖管理工具。

这个系列大概就到此为止了。当然，这个话题还有更多的内容，所以如果我错过了什么或者你有任何问题，请在下面留言！

# 值得注意的提及

这里有一些我知道的工具，但是我没有足够的经验来对它们进行全面的回顾。如果你认为其中一个值得花时间来写这个系列的另一部分，请告诉我。

*   ## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[pypa](https://github.com/pypa)/[孵化](https://github.com/pypa/hatch)

    ### 现代、可扩展的计算机编程语言项目管理

    <article class="markdown-body entry-content container-lg" itemprop="text">

    # 舱口

    | CI/CD | [![CI - Test](img/0079394ff921bd6206baed121c3a5bbc.png)](https://github.com/pypa/hatch/actions/workflows/test.yml)[![CD - Build Hatch](img/7631e18d5f94415f61fae3dcfda2c163.png)](https://github.com/pypa/hatch/actions/workflows/build-hatch.yml)[![CD - Build Hatchling](img/b1d92ec44dc06dbf837a1c897be7da59.png)](https://github.com/pypa/hatch/actions/workflows/build-hatchling.yml) |
    | 文件(文档的简写) | [![Docs - Release](img/f06319256589d099f8e765f4b7003c31.png)](https://github.com/pypa/hatch/actions/workflows/docs-release.yml)[![Docs - Dev](img/81bab60b55a1c4ee5e0fa62221220274.png)](https://github.com/pypa/hatch/actions/workflows/docs-dev.yml) |
    | 包裹 | [![PyPI - Version](img/98def2293f466fc633c63590ea532541.png)](https://pypi.org/project/hatch/)[![PyPI - Downloads](img/67841e43920ce7616915623011416c91.png)](https://pypi.org/project/hatch/)[![PyPI - Python Version](img/b854fef559e9bffb8a88284d0b1c024d.png)](https://pypi.org/project/hatch/) |
    | 自指的 | [![code style - black](img/1abbac1bb267dc041dcfa091baa4840f.png)](https://github.com/psf/black)[![types - Mypy](img/a4881b83931218e76f042448dddb2d89.png)](https://github.com/python/mypy)[![imports - isort](img/d9de69185ef0691d3ffc2df9120715e0.png)](https://github.com/pycqa/isort)[![License - MIT](img/9f68c430692e7660de8562b7179e0a59.png)](https://spdx.org/licenses/)[![GitHub Sponsors](img/145c35168bbe276dc6172965b09d5833.png)](https://github.com/sponsors/ofek) |

    * * *

    孵化是一个现代的、可扩展的计算机编程语言项目管理器. .

    ## 特征

    *   标准化的[构建系统](https://hatch.pypa.io/latest/build/#packaging-ecosystem)默认情况下可重复构建
    *   健壮的[环境管理](https://hatch.pypa.io/latest/environment/)支持定制 脚本
    *   轻松[发布](https://hatch.pypa.io/latest/publish/)到 PyPI 或其他来源
    *   [版本](https://hatch.pypa.io/latest/version/)管理
    *   可配置[项目生成](https://hatch.pypa.io/latest/config/project-templates/)带有 sane 默认值
    *   响应迅速 [CLI](https://hatch.pypa.io/latest/cli/about/) ，比同等工具

    ## 快 2-3 倍证明文件

    [文档](https://hatch.pypa.io/)由 MkDocs 的[素材制作,由](https://github.com/squidfunk/mkdocs-material) [GitHub 页面](https://docs.github.com/en/pages)托管

    ## 许可证

    影线是根据[麻省理工学院](https://spdx.org/licenses/MIT.html)许可证的条款分发的

    </article>

    [查看 GitHub 上的](https://github.com/pypa/hatch)

*   ## 
    ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)
     [【德佩尔】](https://github.com/dephell) / [德佩尔](https://github.com/dephell/dephell) 

    ### 
    📦 🔥计算机编程语言项目管理。管理软件包：格式之间的转换,锁定,安装,解决,隔离,测试,建立图表,显示过期,审计。管理 venvs，构建包,修改版本

    <article class="markdown-body entry-content container-lg" itemprop="text">

    # 项目已存档

    福克斯:[https://github.com/orsinium/forks](https://github.com/orsinium/forks)

    * * *

    # [![DepHell](img/fe97675c5f345e081d21bc90a5b4132b.png)](https://github.com/dephell/dephell./assets/logo.png)

    [![pypi](img/a9c39d20003e0dead59ebbb8958250e7.png)](https://pypi.python.org/pypi/dephell/)[![MIT License](img/84dcfbee41151652f173d1b2bebd0903.png)](https://github.com/dephell/dephell/blob/master/LICENSE)[![Travis CI](img/63a6857a76e97e01a692ac8db2fb3ad4.png)](https://travis-ci.org/dephell/dephell)

    **德菲尔普斯** -Python 的项目管理. .

    为什么它优于所有其他工具:

    1.  **格式不可知**。你可以用你喜欢的格式使用 de hell:setup . py，requirements.txt，Pipfile，poem。德菲尔支持他们所有人，甚至更多。
    2.  **在任何项目上使用自己喜欢的工具**。想安装一个基于诗歌的项目，但不喜欢诗歌？只需告诉 DepHell 将项目的元信息转换成 setup.py，用 pip 安装即可。或者直接使用 DepHell 的项目，因为 DepHell 可以做你通常想用包做的所有事情。
    3.  **DepHell 并不试图取代你喜欢的工具**。如果你使用诗歌，你必须使用诗歌的文件格式和命令。然而，DepHell 可以与任何其他工具结合，甚至可以通过转换格式将所有这些工具结合在一起。您可以在 GitHub

    </article>

    上使用[视图](https://github.com/dephell/dephell)