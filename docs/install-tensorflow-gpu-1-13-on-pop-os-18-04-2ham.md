# 在 Pop OS 18.04 上安装 Tensorflow GPU 1.13

> 原文：<https://dev.to/arandilopez/install-tensorflow-gpu-1-13-on-pop-os-18-04-2ham>

最初发表于 [arandilopez.dev](https://arandilopez.dev/articles/2019/04/09/install-tensorflow-gpu-1-13-on-pop-os-18-04/?utm_source=devto) 。也用[西班牙语](https://arandilopez.dev/articles/2019/04/05/instalar-tensorflow-gpu-1-13-en-pop-os-18-04/?utm_source=devto)读。

砰！_OS 是基于 Ubuntu 的令人难以置信的 Linux 发行版。但是更令人惊奇的是流行音乐！_OS 是它对 Nvidia 和 Tensorflow 显卡的支持，选择 Pop 是一个很棒的功能！_OS 作为发行版。在这篇文章中，我将展示用 python3 获得 Tensorflow GPU 1.13 的步骤。

砰！_OS 18.04 支持带有 ISO 映像的显卡，该映像拥有 System76 维护的最新专有驱动程序。最新的驱动程序是版本 418，你可以用命令`nvidia-smi`检查你的操作系统上安装的版本。

```
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 418.56       Driver Version: 418.56                              |
|-------------------------------+----------------------+----------------------+ 
```

砰！_OS 有一个[元包](https://support.system76.com/articles/install-tensorflow/)用于原生安装 Tensorflow 和 CUDA，但是这个元包安装的是 Tensorflow 的 1.9 版本。

## 配置 Python 3

首先，我们安装必要的包来开始我们的 python3 开发，我们将安装 pip 和 venv 来管理我们的 python 项目的依赖性。

```
$ sudo apt install python3-dev python3-pip python3-venv 
```

## 安装 CUDA 10.0

要安装 Tensorflow 的最后一个稳定版本 1.13，我们需要有 Nvidia 418 驱动程序，并为 CUDA 和 Tensorflow 安装以下依赖项。

```
$ sudo apt install autogen autogen-doc cmake cmake-data guile-2.0-libs libblas3 libgc1c2 libgfortran4 libjsoncpp1 liblapack3 libnvidia-compute-418 libopts25 libopts25-dev librhash0 libuv1 python3-numpy 
```

现在安装必要的 CUDA 包。

```
$ sudo apt install system76-cuda-10.0 system76-cudnn-10.0 
```

> 有 10.1 版本的 CUDA 包，但是它们不支持 Tensorflow 1.13。[看到这里。](Https://github.com/tensorflow/tensorflow/issues/26209#issuecomment-479127128)

## [T1】安装 Tensorflow GPU](#install-tensorflow-gpu)

我们将为我们的项目创建一个新文件夹

```
$ mkdir ~/test_tensorflow_gpu
$ cd test_tensorflow_gpu 
```

我们开始了一个新的虚拟环境，用于我们对这个项目的依赖。

```
$ python3 -m venv venv
$ source venv/bin/activate 
```

> 激活虚拟环境后，我们可以分别以`python`和`pip`的身份运行`python3`和`pip3`。

安装 Tensorflow。安装支持 CUDA 的 Tensorflow 的正确包是`tensorflow-gpu`。

```
(venv)
$ pip install --upgrade tensorflow-gpu 
```

测试 Tensorflow 的安装。

```
(venv)
$ python -c "import tensorflow as tf; print(tf.test.is_built_with_cuda());"
True # <-- expected output 
```