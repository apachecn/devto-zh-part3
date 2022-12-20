# 我如何发布我的第一个 pip 包

> 原文：<https://dev.to/prashantsengar/how-i-published-my-first-pip-package-4d5d>

这是我第一篇关于 [DEV](https://dev.to) 的文章，也是第一篇关于编程的文章。
我是一名编程学生，今天我满心欢喜地在 [Pypi](https://pypi.org/project/RedPy) 上发布了我的第一个[包](https://github.com/prashantsengar/RedPy)。这篇文章将包含我为完成这个壮举所做的事情。

## 1。编写代码:

我的包是从 Reddit 下载图片的 Python 包装器。它由一个类组成，该类由一个接受输入的方法组成——用户代理、子编辑名和其他输入。
把要上传的代码写成一个包，需要很好的模块化。每一行代码都应该在一个函数或类中。首先写下项目的目标将有助于这一过程。绘制流程图和/或编写算法也会有所帮助。
在你的代码中遵循 [PEP-8](https://www.python.org/dev/peps/pep-0008/) 指南。

## 2。测试代码:

在你写完代码后，第一步应该是做一次测试，看看你的代码中是否有 bug。

如果您在模拟运行中没有发现任何错误，那么测试您的代码。编写一个小脚本，导入你的模块并使用其中的所有函数。

如果您在测试中没有发现任何错误，瞧，继续下一步。如果你发现了一个 bug，追踪并删除它。

## 3。代码审查*(可选)*:

虽然这一步是可选的，但却是最重要的一步。让其他程序员审查你的代码是你能做的最好的事情。其他人能比你更好地发现你代码中的错误或糟糕的编程实践。因此，强烈建议执行此步骤。
在这个过程中，我学到了很多，你也会学到很多。

**您可以审查代码的地方-**

*   [codereview.stackexchange.com](https://codereview.stackexchange.com)
*   [r/python](https://reddit.com/r/python)
*   [r/learnpython](https://reddit.com/r/learnpython)

## 4。为上传和上传包设置包

写一个好的自述文件并将代码上传到 Github 应该是上传前的第一步
有一整套[文档](https://packaging.python.org/tutorials/packaging-projects)让你的包准备好上传到 PyPi 上，但是它遗漏了很多细节。
我推荐阅读[这篇文章](https://medium.com/@joel.barmettler/how-to-upload-your-python-package-to-pypi-65edc5fe9c56)作者 [joelbarmettlerUZH](https://medium.com/@joel.barmettler) 。

您的包最终被上传到 PyPi，并且可供用户使用。把话传出去！

这是我的软件包 Github repo 的链接-[https://github.com/prashantsengar/RedPy](https://github.com/prashantsengar/RedPy)