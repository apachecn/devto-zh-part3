# 优化您的数据科学环境

> 原文：<https://dev.to/idoshamun/optimize-your-data-science-environment-26j1>

在过去的几年里，我进入了数据科学的世界，来自工程和 devops 领域。我认为一名工程师对数据科学世界的观点与一开始就以数据科学家身份起步的人非常不同。我想分享我的 3 个工程技巧，它们提高了我的数据科学工作，我希望这对你也一样。

### 使用 Docker🚢

我必须承认，我讨厌 Python 及其所有虚拟环境的包管理。来自更进化的生态系统(比如 NodeJS)，很难适应。最重要的是，如果你在做深度学习，你显然是在利用 GPU，所以你需要安装 Nvidia 驱动程序，CUDA 等等。

我不使用 conda，实际上我没有安装它，我在数据科学项目中使用 Docker。我使用 [Deepo](https://hub.docker.com/r/ufoym/deepo/) 作为我的基本映像，因为它预先构建了开始运行深度学习工作负载所需的一切(Tensorflow、Keras、Python 3、CUDA、驱动程序等)。而不是传统的编写 Dockerfile 的方式，我简单的初始化一个新的容器是这样的:*docker run—runtime = NVIDIA—name container-name-p 8888:8888-v directory/to/share:/container/path-d UFO ym/deepo:all-jupyter jupyter notebook—no-browser—IP = 0 . 0 . 0 . 0—allow-root—notebook-dir = '/container/path '*。这个命令提供一个新的容器，并与该容器共享一个目录，这样您就可以共享您的数据和代码。此外，它还公开了从浏览器访问 jupyter 的端口 8888。您现在可以使用下面的代码访问容器的 bash:*docker exec-ti 容器名 bash* 。你在容器中所做的一切都将保留在那里，包括你安装的任何包或软件。在一批更改之后，我可以提交所有的更改并推送图像，这样我就可以在任何我想要的地方使用它。您在主机系统上唯一需要的就是安装 Docker，仅此而已。

### 远程运行作业👩‍🚀

训练或超调等深度学习过程可能会花费大量时间。我有一台很强的笔记本电脑，配有很棒的 GPU，所以在本地运行它的诱惑很大。自从我想到使用 Docker，在云上运行一切变得容易多了，但在本地进行许多快速迭代。这样，我可以通过下载最新的 Docker 轻松复制我的工作环境。

远程运行作业让我可以使用更高级的硬件，如特斯拉显卡和数量惊人的 CPU 和内存为我服务(如此昂贵😓).最大的好处是，我可以用我的电脑来处理工作负荷之间的其他任务。如果你想走极端，jupyter 有一些 slack 集成，所以它可以在所有事情都完成时通知你，这样你就不必自己检查了。

### 并行化👯

即使只有一个 GPU，只要 GPU 上有足够的内存，就可以运行多个训练工作负载。它可以节省您的处理时间，并可以大大缩短您的迭代，特别是当它涉及到超调。Talos 已经创建了一个配置 Tensorflow 与其他处理程序共享 GPU 的函数，您可以使用它或者直接复制代码:[https://github . com/autonomio/talos/blob/6 a4 fbfacdbd 7 a6 ebf DDD 27668761089978 CFG 053/talos/utils/GPU _ utils . py # L1](https://github.com/autonomio/talos/blob/6a4fbfacdbd7a6ebfddd27668761089978cfc053/talos/utils/gpu_utils.py#L1)

您所需要的就是生成多个流程，例如，每个流程可以运行一个单独的培训工作负载。我喜欢 Python 的多重处理包，它有一个[池](https://docs.python.org/2/library/multiprocessing.html)对象，可以很容易地让你产生其他进程。

就是这样！非常直截了当的建议，有望节省你的时间，让你的生活更轻松😁