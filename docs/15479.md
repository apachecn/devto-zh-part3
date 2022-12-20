# 如何创建托管在私有 Github repo 上的 PIP 包

> 原文：<https://dev.to/rf_schubert/how-to-create-a-pip-package-and-host-on-private-github-repo-58pa>

我开发了几个项目和几个类来集成多个服务和服务器。这些类是我的业务，现在我需要一个简单的方法来允许所有项目使用它，下载它和它的所有更新。

最好的方法是什么？嗯，我们每次都用`pip`下载成千上万的包到我们的项目中，这太棒了。

那么，我们能用自己的`private`回购做些什么呢？让我们在`Github`上主持它的项目，看看会发生什么。

我正在写的这篇文章将分享我的经验。直到写这篇文章的那一天，我也不像你一样知道它将如何工作。

让我们开始我们的研究案例。

## 第一步

基于本教程:[https://python-packaging . readthedocs . io/en/latest/minimal . html](https://python-packaging.readthedocs.io/en/latest/minimal.html)我将创建我的第一个包，准备好进入我的私有 pip repo。

### 创建根路径并启动 git

首先，我将创建一个名为`ptolemaios`的路径，它是我的主类名，并在其上启动一个新的 git。

```
$ mkdir ptolemaios
$ cd ptolemaios
$ git init
> Initialized empty Git repository in '...ptolemaios/.git/' 
```

Enter fullscreen mode Exit fullscreen mode

#### 文件夹结构

教程上说我需要这个文件夹结构:

```
ptolemaios/
    ptolemaios/
        __init__.py
    setup.py 
```

Enter fullscreen mode Exit fullscreen mode

因此，让我们在我的`ptolemaios`文件夹中再创建一个名为`ptolemaios`的文件夹...听起来有点混乱。

```
$ mkdir ptolemaios 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建基础档案

让我们创建文件`setup.py`和`__init__.py`

```
$ touch setup.py
$ touch ptolemaios/__init__.py 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我有了教程的确切结构。让我们提交它并继续下一步。

```
$ git add .
$ git commit -am "created basic package structure files and folders"
> 2 files changed, 0 insertions(+), 0 deletions(-)
> create mode 100644 ptolemaios/__init__.py
> create mode 100644 setup.py 
```

Enter fullscreen mode Exit fullscreen mode

#### MVP 包

因为我想学习如何做，并检查它是否工作，所以我将从创建一个函数开始，这个函数只是在我的屏幕上打印一些东西。

让我们打开我们的`__init__.py`文件并编辑它

```
def my_cool_test_method():
    print('It works!') 
```

Enter fullscreen mode Exit fullscreen mode

让我们打开我们的`setup.py`并配置关于包
的基本信息

```
from setuptools import setup

setup(
    name='ptolemaios',
    version='0.0.1',
    description='My private package from private github repo',
    url='git@github.com:rfschubert/ptolemaios-sdk-package.git',
    author='Raphael Schubert',
    author_email='raphael.schubert@digitalbankscorp.com',
    license='unlicense',
    packages=['ptolemaios'],
    zip_safe=False
) 
```

Enter fullscreen mode Exit fullscreen mode

您可以用您想要的所有信息自定义这些信息。

让我们提交它

```
$ git commit -am "updated setup.py and created my_cool_test_method"
> 2 files changed, 19 insertions(+) 
```

Enter fullscreen mode Exit fullscreen mode

我已经在 github 上创建了我的私有回购协议，所以现在，我将把它添加到 project 中，并将所有提交推送到我的回购协议中

```
$ git remote add origin git@github.com:rfschubert/ptolemaios-sdk-package.git
$ git push -u origin master
> Counting objects: 9, done.
> Delta compression using up to 8 threads.
> Compressing objects: 100% (6/6), done.
> Writing objects: 100% (9/9), 1.00 KiB | 514.00 KiB/s, done.
> Total 9 (delta 0), reused 0 (delta 0)
> To github.com:rfschubert/ptolemaios-sdk-package.git
>  * [new branch]      master -> master
> Branch 'master' set up to track remote branch 'master' from 'origin'. 
```

Enter fullscreen mode Exit fullscreen mode

现在，我想将它添加到我正在运行的项目中，看看是否可行，我将打开我机器上的任何项目，并尝试使用`pip`从`GitHub`安装它。

#### 用`pip`安装

让我们在我们想要安装的项目中打开我们的终端并运行这个命令

```
$ pip install git+ssh://git@github.com/rfschubert/ptolemaios-sdk-package.git
> Collecting git+ssh://git@github.com/rfschubert/ptolemaios-sdk-package.git
>   Cloning ssh://git@github.com/rfschubert/ptolemaios-sdk-package.git to > /private/var/folders/7z/....
> Installing collected packages: ptolemaios
>   Running setup.py install for ptolemaios ... done
> Successfully installed ptolemaios-0.0.1 
```

Enter fullscreen mode Exit fullscreen mode

这里我们没有什么重要的东西。我在哪里找到这个`URL`？

这是一个基本的克隆 url，只做了很少的修改。

我的`ssh`克隆网址是:`git@github.com:rfschubert/ptolemaios-sdk-package.git`，在`ssh`网址上看`:`，改成`/`，在你的`ssh url`上加`git+ssh://`

现在你应该有`git+ssh://git@github.com/rfschubert/ptolemaios-sdk-package.git`，就是这样！就做`pip install git+ssh://git@github.com/rfschubert/ptolemaios-sdk-package.git`开心就好！

让我们在我的项目中测试它。

我就上`Django console`来简单测试一下。

```
import ptolemaios

ptolemaios.my_cool_test_method()
# It works! 
```

Enter fullscreen mode Exit fullscreen mode

厉害！我现在可以在我的项目上有私人包。

### 退后一步

嗯，在我的电脑上，一切都运行得很好，但这就是为什么我有从私人存储库中获取数据的`ssh`键。我的`Heroku` app 会怎么下载？

首先，我将运行命令来更新我的`requirements.txt`文件

```
$ pip freeze > 'requirements.txt' 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我查看它的内部，它只有我的包名，而没有 GIT url

```
...
ptolemaios==0.0.1
... 
```

Enter fullscreen mode Exit fullscreen mode

您需要将这行代码修改成预期的样子:

```
...
git+ssh://git@github.com/rfschubert/ptolemaios-sdk-package.git
... 
```

Enter fullscreen mode Exit fullscreen mode

因此，当您的服务器部署时，它将从存储库中获取当前版本。

我找到了一个答案，可能对你们中的一些人有用，但对我没用，我需要做更多的研究，以后再更新这个文件。

您可以在`GitHub`上创建一个`Machine User`并将其添加到您的私有 repos 中，在您的服务器上生成一个`ssh`密钥并将其添加到`Machine User`中，每次您部署时，您的服务器都将拥有`ssh`密钥并被允许下载它。

### 如何部署到 Heroku？

我还不知道，但我会弄清楚的，我会回来的...让我们再做一些研究...