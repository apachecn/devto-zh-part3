# 如何在 Ubuntu 18.04 上安装 virtualenv

> 原文：<https://dev.to/serhatteker/how-to-install-virtualenv-on-ubuntu-18-04-2jdi>

当我们的 Linux 发行版只提供特定版本的 Python 及其包时，我们可能会遇到问题，而我们实际上需要更新或更旧的版本。

我们可以在服务器上安装新版本的 Python，但是这会更复杂，因为当我们试图编译我们需要的所有东西时，会有一些依赖问题。

虚拟环境使得这非常容易管理和设置，我们可以在每个环境中有不同版本的 Python 和包，并且它将与主系统隔离。

在 Ubuntu 18.04 或者 16.04 以后的版本上安装虚拟环境是一件相当简单的事情，不需要超过 10 分钟就可以完成。

## 安装 pip

如果你还没有安装 pip 3

```
$   sudo apt-get install python3-pip 
```

Enter fullscreen mode Exit fullscreen mode

## instal virtualenv

```
$   sudo pip3 install virtualenv 
```

Enter fullscreen mode Exit fullscreen mode

## 建立新的 virtualenv

```
$   cd $YOUR_PROJECT_DIRECTORY
$   virtualenv .venv 
```

Enter fullscreen mode Exit fullscreen mode

。venv 或任何`$NAME`为您的 virtualenv

## 激活您的 virtualev

```
$   source .venv/bin/activate 
```

Enter fullscreen mode Exit fullscreen mode

你会看到你的`.venv`被激活

```
(.venv) ~/project$ 
```

Enter fullscreen mode Exit fullscreen mode

## 关闭你的虚拟钥匙

```
$   deactivate 
```

Enter fullscreen mode Exit fullscreen mode

## 安装新的软件包

激活您的虚拟后

```
$  pip install <some-package> 
```

Enter fullscreen mode Exit fullscreen mode

### P.S:

你也可以为你的`virtualenv`使用`Python2.7`解释器，但是我不建议这样做，因为 Python 2.7 **将在 2020 年 1 月 1 日**之后不再维护。但是如果你愿意:

```
$   virtualenv -p /usr/bin/python2.7 .venv 
```

Enter fullscreen mode Exit fullscreen mode