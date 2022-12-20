# 开发者机器学习之旅:安装 Python、Jupyter、TensorFlow 和 Keras

> 原文：<https://dev.to/zac_siegel/a-developers-journey-into-machine-learning-installing-python-jupyter-tensorflow-and-keras-4epp>

本文起源于[zsiegel.com](https://www.zsiegel.com/2019/05/30/developers-journey-into-machine-learning-part1)

## 作为开发者的机器学习

毫无疑问，机器学习是当今计算机科学中最有趣的话题之一。与许多机器学习演示相关的极快的速度和令人惊叹的因素给了它一些神秘感，这仍然让我挠头想知道它是如何可能的。

当我最终决定投身于机器学习时，我从一系列问题开始，这些问题无疑将需要很长时间才能最终得到答案，但它至少给了我一个初步的方向。

*   强大的数学和统计背景是必要的吗？
*   有没有一套基本的术语和概念可以作为良好的基础？
*   做机器学习真的有必要用高端 GPU 吗？
*   他们的机器学习类型不需要 GPU 吗？
*   鉴于令人难以置信的变化速度，1-2 年前的任何书籍、教程和在线示例仍然相关吗？

为了探索这些问题，我开始快速行动起来，这样我就可以把时间花在学习和实验上。以下是帮助我以最少的忙乱快速开始的方法。

## Linux, Python, CUDA, TensorFlow, and Keras

我之前提到过，机器学习的变化速度惊人。我与一些涉足该领域的同事交谈过，他们面临的核心挑战之一是工具。

`Many of them had challenges keeping their toolchains and dependencies in simple working order`。他们经常遇到各种框架的小版本更新，导致兼容性问题。

在我的初始设置中，我经历了类似的问题，所以我慢慢地、系统地回到了看起来稳定的设置，并在多台机器上重现。

*   PopOS 19.04
*   Python 3.7
*   TensorFlow 1.13.1
*   Keras 2.2.4
*   CUDA 10.0

作为一名熟悉 [Docker](https://www.docker.com/) 的开发人员，我不得不抵制向工具链添加另一个依赖项的冲动，所以我查看了各种 Docker 文件，找到了一个已经在多个项目中运行的组合，并且在基于 Ubuntu 的 linux 发行版 [PopOS](https://system76.com/pop) 上非常稳定。

这一切都始于一些可以通过`apt`安装的基本工具。

```
sudo apt install python3 python3-venv system76-cuda-10.0 system76-cudnn-10.0 
```

值得注意的是，我选择了 [PopOS](https://system76.com/pop) 作为开发人员库，这使得安装 CUDA 非常简单。

如果您运行的是基于 Debian 的操作系统，并且安装了 NVIDIA 显示驱动程序，您可以通过执行以下操作来获得 System76 开发人员包。

```
sudo echo "deb http://apt.pop-os.org/proprietary bionic main" | sudo tee -a /etc/apt/sources.list.d/pop-proprietary.list
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-key 204DD8AEC33A7AFF
sudo apt update 
```

## 设置 Python 和 Jupyter 笔记本

安装好基本框架后，是时候设置我的开发环境了。作为一个主要使用强类型语言和编译器进行开发的人，我不确定我是否会主要生活在 python repl 中，但是我发现 Jupyter 非常灵活和有趣。

Jupyter 笔记本是在本地机器和云中探索和运行示例的好方法。在我最初的实验中，我花了大量的时间在 Github 上浏览 Jupyter 的笔记本，在 T2 的谷歌实验室里玩。

`You can think of Jupyter notebooks as a combination of prose, documentation, runnable code, and console output.`

目前，我已经决定将我所有的工作存储在我电脑上的一个目录中，但我可以预见将来会根据需要创建专用的环境。

我为所有内容选择了一个目录，因为我可以让我的 jupyter 服务器保持运行，并使用一个名为 [Juno](https://juno.sh/) 的应用程序从我的 iPad Pro 远程访问我的 [Jupyter](https://jupyter.org/) 服务器。这是我以后要写的东西！

现在开始设置 Python 和 Jupyter。

```
# Create the directory where we will be storing everything
mkdir ~/src/jupyter
cd ~/src/jupyter

# Create a fully isolated python environment - this is similar to gradlew or other wrapper scripts you may have encountered in other languages
python3 -m venv jupyter-env

# This activates the virtual environment - when activated you will see your prompt change to indicate you are in `jupyter-env`. Any commands run after this will be scoped strictly to this environment
source jupyter-env/bin/activate

# Install the dependencies. Again its important to understand these dependencies are not globally available on your system. They are only available to the virtual environment we activated just before this
pip install pandas numpy tensorflow-gpu keras jupyter matplotlib pillow scikit-learn

# Store all of the dependencies into a text file. Make sure you run this command if you add any new dependencies using pip. You can use this file to re-install the dependencies on a new machine using `pip install -r requirements.txt`
pip freeze > requirements.txt

# Launch the notebook - this will start a jupyter server and open your web browser
jupyter notebook 
```

有了这个设置，我可以进行一些学习练习，工具链也不会妨碍我...至少现在还没有:)。