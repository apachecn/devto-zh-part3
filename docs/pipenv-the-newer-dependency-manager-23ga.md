# Pipenv:较新的依赖性管理器

> 原文：<https://dev.to/dbanty/pipenv-the-newer-dependency-manager-23ga>

> 这是解释管理 Python 依赖关系的不同选项的系列文章的一部分。确保你已经阅读了前两篇文章，这篇文章将基于这些信息。

# TL；速度三角形定位法(dead reckoning)

Pipenv 是一个工具，可以让您在开发过程中节省一些步骤，并让您能够锁定依赖关系。然而，它非常慢而且固执己见，如果你不小心的话，默认设置会给你带来麻烦。

# 组件

这个列表与[普通堆栈](https://dev.to/dbanty/vanilla-python-stack-99a)非常相似，将一些组件捆绑在一起。

*   [Pipenv](https://docs.pipenv.org/en/latest/) :用于安装依赖关系和管理虚拟环境
*   [Pipfile](https://docs.pipenv.org/en/latest/basics/#example-pipfile-pipfile-lock) :用于声明生产依赖和开发依赖
*   Pipfile.lock:一个用于同步环境间依赖关系的精确版本的文件
*   [setup.py](https://packaging.python.org/tutorials/packaging-projects/#creating-setup-py) :用来设置元数据和创建你的包的文件
*   [setuptools](https://setuptools.readthedocs.io/en/latest/) :用于构建你的项目
*   [twine](https://pypi.org/project/twine/) :用于将你的项目上传到 PyPI 服务器
*   [pip](https://pypi.org/project/pip/) :用于一些配电情况

# 总结

Pipenv 栈基本上是一个改进的普通栈，但有一些折衷。开发者体验(大部分)更好，发布过程稍差。对于较小的项目，我认为 Pipenv 是有意义的，对于较大的项目，你最好找到另一个解决方案。

# 发展

## TL；速度三角形定位法(dead reckoning)

Pipenv 堆栈主要通过抽象出与虚拟环境相关的命令来节省您的时间。它还允许您将需求(您的项目所依赖的依赖项的版本)与上一个测试版本(您知道可以用于您的项目的版本)分开。

## 定义需求

*   👍开发需求:对 vanilla 栈的主要改进之一是能够在与生产需求相同的文件中声明开发需求。通过手动添加到 Pipfile 的`[dev-packages]`部分，或者通过执行`pipenv install --dev [package_name]`来完成
*   😕可再现性:虽然拥有一个您可以使用的锁定文件是对[普通堆栈](https://dev.to/dbanty/vanilla-python-stack-99a)的一个巨大进步，但是 Pipenv 非常努力地让您不断更新您的包。您可以使用`pipenv sync`来安装您的锁定文件中的内容，但是**每隔一个命令** ( `add`、`install`等。)将更新您的包并锁定文件，这意味着很难保持再现性。
*   😨添加新的包:因为 Pipenv 会尽可能尝试更新您的整个项目，所以添加新的包是一件非常痛苦的事情。基本上，你不能这样做，除非你手动定义完整的版本(例如`== 2.3.2`)或者准备检查和测试所有更新的东西。更糟糕的是，如果您使用`pipenv install`命令将包添加到您的环境和 Pipfiles 中，它将默认为一个通配符(`*`)，这意味着**该包的任何**版本都被接受为更新(甚至是重大更改！).最重要的是，Pipenv 非常慢。基本上，Pipenv 只是让向项目添加新的包变得很麻烦。
*   😊替代来源:Pipenv 在这方面做得很好。只需在 Pipfile 中添加您的新存储库作为源，并使用环境变量引用任何凭证。很简单。

## 虚拟环境

*   😌设置:轻而易举。然后去煮些咖啡。当您回来时，您将拥有一个全新的虚拟环境，它仍在安装您的依赖项，但它只需要一个命令。
*   🙂用法:Pipenv 极大地简化了虚拟环境的工作流程
    *   安装要求:如果您想要上次测试的内容，请使用`pipenv sync`。如果您想要符合您的约束的最新包，请使用`pipenv install`。我知道我是在白费力气，但是很难夸大 Pipenv 解决和安装包的速度有多慢。
    *   跑步:`pipenv run python [your script]`
    *   激活环境:`pipenv shell`
    *   停用:`deactivate`

# 分布

## TL；速度三角形定位法(dead reckoning)

到目前为止，你喜欢 Pipenv 的任何东西在发行版上都已经过时了。你必须使用和你使用[普通栈](https://dev.to/dbanty/vanilla-python-stack-99a)一样的工具，因为从 Pipfile 到旧的需求格式变得更加困难。

## 打造

*   😿定义:所有东西都必须存储在你的 [setup.py](https://packaging.python.org/tutorials/packaging-projects/#creating-setup-py) 中，甚至是你的需求。这意味着你要么在每次构建之前从你的 Pipfile 中导出一个 requirements.txt 文件( ***)，这将把你所有的依赖项更新到最新的版本，撤销你刚刚完成的任何测试！*** )或者在 setup.py 文件中声明您的依赖项，并将您的项目作为一个依赖项安装在您的 Pipfile 文件中，这相当于将 Pipfile 文件从过程中去掉，并取消了您通过选择 Pipenv 获得的大部分特性。
*   🙄建筑:和在[普通堆栈](https://dev.to/dbanty/vanilla-python-stack-99a)中的一模一样

## 部署

*   🥵·派皮:和在[香草栈](https://dev.to/dbanty/vanilla-python-stack-99a)里的一模一样
*   🙅‍源分布:好的最后一次，我保证。用 Pipenv 安装依赖项**太慢了**以至于你应该导出到一个需求文件，然后用老方法设置你的服务器。说真的，把 Pipenv 放在服务器上不值得。
*   😪预构建依赖关系:这就像[普通堆栈](https://dev.to/dbanty/vanilla-python-stack-99a)，但是现在增加了一个步骤，将您的 Pipfile 导出到一个 [pip](https://pypi.org/project/pip/) 可以使用的需求文件。

# 结论

说实话，我不确定 Pipenv 值不值得。根据我的经验，最终花费的时间比节省的时间要多，但是你的里程数会非常多。从技术上来说，它现在是 PyPA 的一部分，所以希望他们会改进它，但根据我在 GitHub 上看到的问题，这似乎不太可能。维护者非常固执己见，不太关心其他人如何使用这个工具。挑选筹码时，我会避开 Pipenv。