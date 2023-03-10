# Pytabby:基于控制台的 Python 程序的选项卡式菜单系统

> 原文：<https://dev.to/prooffreader/pytabby-a-tabbed-menu-system-for-console-based-python-programs-301n>

我做了一个东西。我在分享这个东西。

[GitHub 上的 py tabby](https://github.com/Prooffreader/pytabby)

[pytabby 是 pydy](https://pypi.org/project/pytabby/)的一个角

[pytabby on ReadTheDocs](https://pytabby.readthedocs.io/en/latest/)

回购协议 99%的时间都有一个无聊的名字(tabbedconsolemenus)，但在我发布初始版本的那天，我把谨慎抛到了九霄云外，将其命名为 pytabby。

基本上，它是一个允许你定义一个基于控制台的菜单系统来控制你的程序流的工具。

我很奇怪，喜欢基于控制台的程序，而不是带参数的命令行，或者 GUI，或者其他什么。(在 Windows 上，我承认我越来越少使用 Linux，主要是因为我的新 System76 Oryx Pro 笔记本电脑，使用丑陋但实用的 ContextEdit 实用程序可以轻松创建一个上下文菜单来启动各种 Python 脚本，将所有东西放在一个伞下很好，这样我就可以选择一个文件夹并递归地删除空文件夹，找到重复的文件，调整图像文件的大小等。，全部用一个包装。).

现在，在 Github 和 PyPI(Python 包组织者)上有几个层次的贡献者:像 Google 这样的商业组织贡献了 Tensorflow 这样的大型开源项目，像 scikit-learn 这样的志愿者组织贡献了大型开源项目，像 Kenneth Reitz 这样的神一般的个人为 request 和 Pipenv 这样的无处不在的项目做了大部分繁重的工作……然后是其他几个层次。我肯定在名单的后面几名。

我认为我在这里的经历是许多开发人员的典型经历。我有一个我用过的东西(一个单曲。py 脚本，我用来制作菜单系统和控制其他 python 程序)，我想，(a)这可以更健壮，(b)也许其他人可以使用它，以及(c)获得一些关于开源工具集的许多元素的实际经验将是很好的，包括 Pipenv(我最终没有使用它，因为一个不适用的原因，但当时我被锁定到 virtualenvwrapper，无论如何，目前)，与 Travis，Appveyor， tox、pytest(我以前使用过，但从未用于像这样的独立项目)、林挺(我以前使用过，但不是很认真)、覆盖率、代码质量和安全性检查、重构的文本、Sphinx 和 readthedocs。 当然，还有 setup.py，并将我的源代码放在 src/文件夹中，这样它就不在您的路径中，必须在本地可编辑地安装 pip。我有一种感觉，我将来会写一篇博文来描述这个生态系统。哦，还有徽章。(“徽章？我们不需要讨厌的徽章。“但是他们很好。)

所以这是一次很棒的学习之旅。我觉得我的最终产品还可以。我怀疑它会不会很受欢迎，但重要的是旅程，对吗？

哦，既然有了这个名字，我怎么能不为它拍张照呢？