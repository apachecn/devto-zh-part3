# 机器学习的设置(Pt。3)

> 原文：<https://dev.to/benprax/setup-for-machine-learning-part-3-1c02>

机器学习计算量很大。你需要一些工具来在你自己的电脑上进行机器学习。我推荐以下规格:

*   英特尔酷睿 i5 第一代处理器
*   4GB 内存。
*   **没必要**但是有 GPU 是一个很大的优势。与 CPU 相比，GPU 的计算速度更快。
*   对于存储，我建议至少有 250 GB 的固态硬盘。拥有 120 GB 实在是太少了，尤其是在下载大型数据集和安装大量库的时候。SSD 硬盘驱动器允许更快的读写速度。

我建议你观看 Siraj Raval 的[视频](https://www.youtube.com/watch?v=dtFZrFKMiPI)，了解关于哪些笔记本电脑适合机器学习的更详细解释。

你应该使用基于 Linux 的系统。使用 ml 要容易得多，尤其是因为您会经常使用终端*和*。用 Windows，不是不行，但肯定更难。

*   安装 python (2.7 或 3.5 或更新版本)并将其添加到您的路径中。
*   安装 python 后安装 pip。看我的教程[这里](https://www.youtube.com/watch?v=b2HoPw5Z1dY)。
    *(如果对你有帮助请订阅。今年我会在✌发布更多内容🏽)*
    [https://www.youtube.com/embed/b2HoPw5Z1dY](https://www.youtube.com/embed/b2HoPw5Z1dY)

*   您可以选择安装虚拟环境。我推荐使用 [virtualwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/install.html) 。

借助虚拟环境，您可以轻松管理使用 pip 安装的不同软件包。

*   推荐你也装 [jupyter 笔记本](https://jupyter.org/install)。

```
python -m pip install jupyter
# do python3 if you installed python 3 or greater 
```

使用 jupyter notebook，您可以立即看到代码的结果，而不必在每次更改一行代码时都进入终端并运行`python *.py`。

*   或者，您也可以使用代码编辑器和终端来运行您的代码。我更喜欢使用 [VSCode editor](https://code.visualstudio.com/download) 和 [iTerm](https://www.iterm2.com/) 终端，尽管你也可以选择使用 Mac 或 Linux 中的默认终端或任何你喜欢的代码编辑器。

感谢阅读！这是 4 部分系列的第 3 部分。接下来我们将讨论进入机器学习的代码。