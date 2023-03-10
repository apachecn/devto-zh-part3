# 普通 Python 堆栈

> 原文：<https://dev.to/dbanty/vanilla-python-stack-99a>

这是描述管理 Python 项目的不同方法的系列文章的一部分。要了解我所说的“依赖堆栈”是什么意思，请查看第一篇文章。

# TL；速度三角形定位法(dead reckoning)😩

普通堆栈是最基本的选择，使用标准和推荐的 Python 工具。因此，它是最稳定的，也是最难使用的选项。你必须自己做大部分工作。

# 组件

*   [pip](https://pypi.org/project/pip/):Python 的包安装程序。用于安装依赖项。
*   [requirements.txt](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format) :用于声明项目依赖关系的文件
*   dev_requirements.txt:用于为开发者(不是消费者)声明依赖关系。与 requirements.txt 格式相同
*   [setup.py](https://packaging.python.org/tutorials/packaging-projects/#creating-setup-py) :用来设置元数据和创建你的包的文件
*   [setuptools](https://setuptools.readthedocs.io/en/latest/) :用于构建你的项目
*   [twine](https://pypi.org/project/twine/) :用于将你的项目上传到 PyPI 服务器
*   [venv](https://docs.python.org/3/library/venv.html) :用于创建虚拟环境

# 总结

vanilla 栈包含管理 Python 项目的所有最基本的工具。当你用谷歌搜索“如何做”时，你可能会找到这些工具的参考。正如您在组件列表中看到的，有许多移动的部件。因为大多数任务被分解成单独的包，所以这个栈是最难学的，也是最灵活的。

# 发展

## TL；速度三角形定位法(dead reckoning)😩

开发经历坎坷。如果在开发时保持高效是你的主要目标，选择不同的堆栈(文章即将发布)。

## 定义需求

*   😐开发依赖:有了这个堆栈，如果你想把开发者需要的依赖(比如一个测试框架)和运行你的应用需要的依赖分开，你必须把它们放在不同的文件中。
*   😞再现性:对于需求格式，您必须做出选择。您可以将每个依赖项固定到一个特定的版本(`== 1.2.3`)，这意味着如果您想要修复错误，您必须手动寻找包的更新，或者您必须只指定兼容性(`~= 1.2.3`)，这意味着您不能保证所有开发人员都使用相同的版本(或者一个部署的应用程序将具有相同的版本！)导致可怕的“它在我的机器上工作”问题。
*   ✍️添加新的软件包:没有什么好的方法来添加新的软件包，你只能手工将它写入你的需求文件(意味着你必须提前知道你想要什么版本)，然后从那个需求文件安装。
*   🥴替代资源:你可以明确地从所有通常的地方定义需求，但是语法是无处不在的
    *   要添加一个私有的 PyPI repo，您需要在文件的顶部添加一行类似于`--extra-index-url=https://pypi.org/simple/`的代码
    *   添加 Git 依赖项:`-e git://github.com/<repo>.git@<tag>#egg=<package_name>`
    *   从本地路径安装:`-e /path/to/package`

## 虚拟环境

*   🙂设置:使用 Python 3 创建虚拟环境非常简单(使用 Python 2 还需要几个步骤)。你只需要运行 venv 模块，并把它放在你想要的路径上，就像`python3 -m venv .venv`
*   🧟用法:实际上在一个默认的虚拟环境中做事是单调的，并且根据你的平台而不同。您会发现自己一遍又一遍地键入相同的命令。
    *   安装要求:
        *   视窗:`.venv\Scripts\pip.exe install -r dev_requirements.txt`
        *   macOS/Linux: `.venv/bin/pip install -r dev_requirements.txt`
    *   运行脚本:
        *   视窗:`.venv\Scripts\python.exe script.py`
        *   macOS/Linux: `.venv/bin/python script.py`
    *   激活环境(使运行其他命令更加简单)
        *   视窗:`.venv\Scripts\activate`
        *   macOS/Linux: `source .venv/bin/activate`
        *   停用(两个平台):`deactivate`

# 分布

## TL；速度三角形定位法(dead reckoning)😩

与 Python 🧘的[禅相反，有多种非显而易见且复杂的方法使用 Vanilla stack 进行分发。如果您要部署源代码，这很好。如果您要为任何其他类型的发行版构建项目，有更好的选择。](https://www.python.org/dev/peps/pep-0020/)

## 打造

*   🤔定义:关于项目的所有元数据都是在一个 [setup.py](https://packaging.python.org/tutorials/packaging-projects/#creating-setup-py) 文件中完成的，正如您可能已经猜到的，这是一个 Python 脚本。所有内容都进入一个函数调用，这不是一个特别可读的格式。您可以在这里直接声明需求，但是更好的做法是将这个文件指向您的 requirements.txt，这样您就有了一个真实的来源。
*   🙄构建:为了实际打包您的包以便您可以分发它，您可能需要运行命令`python setup.py sdist bdist_wheel`，这是一个非常不明显的命令，您可能希望使用它来简化工作。这将创建一个[轮](https://packaging.python.org/glossary/#term-wheel)和一个[源分布](https://packaging.python.org/glossary/#term-source-distribution-or-sdist)供以后使用。

## 部署

*   🥵·派皮:部署到派皮需要你使用另一个不同的工具，这次是 [twine](https://pypi.org/project/twine/) 。对于公开的 PyPI 回购，这看起来像`python -m twine upload dist/*`。如果您上传到一个私有的 PyPI repo，您需要在您的环境变量或。pypirc 或 keyring(同样，没有明确赢家的多个选项)并添加一个`--repository-url`选项。
*   😸源代码分发:这绝对是使用这个堆栈部署应用程序的最简单的方法。只要把源代码复制到它要去的地方，从 requirements.txt 安装，就大功告成了。安装前无需安装额外的工具。
*   😇预构建的依赖关系:几乎和源代码分发一样简单， [pip](https://pypi.org/project/pip/) 提供了一些简单的方法来将你的依赖关系和你的应用捆绑在一起。如果你试图构建一个桌面应用或者多阶段 docker 构建，这是非常有用的。你可以做`pip install --target build/ -r requirements.txt`将你所有的需求安装到一个文件夹中。或者，你可以做`pip download --dest build/ -r requirements.txt`(为什么是 dest 而不是 target？)将构建好的需求(如[轮](https://packaging.python.org/glossary/#term-wheel) s)下载到一个文件夹中。

# 结论

香草栈是...好吧。它包括许多不同的工具，不一致的语法，以及主观上难以阅读的文件格式。它不像我将在以后介绍的其他两个堆栈那样有一个“锁”文件，这意味着依赖关系要么不太可预测，要么不太可用。从好的方面来说，大多数 Python 安装中都包含了许多所需的工具(例如 [pip](https://pypi.org/project/pip/) 和 [venv](https://docs.python.org/3/library/venv.html) ，所以你不必去获取它们。它们也是受到良好支持的“稳定”工具(出于某种原因，pip 开发人员喜欢删除我使用的特性)。