# 使用 Pipenv 的 Python 包管理

> 原文：<https://dev.to/email2vimalraj/python-package-management-with-pipenv-i6a>

最初发表于我的[博客](http://www.vimalselvam.com/2019/03/02/python-package-management-with-pipenv/)。

在我的工作中，我必须为基于 Python 的项目设置包管理。差不多 5 年后，我开始接触 Python，发现很多东西都变了。特别是，我们如何使用新的 Pipenv 来管理 python 包。让我们看看如何在 Mac 中使用 Pipenv 来设置项目的包管理。

在开始之前，让我看看我的机器上有哪些版本的 python:

[![python version](img/ef623ea56759c0eea9c8ccb3903882c5.png)](https://i1.wp.com/www.vimalselvam.com/wp-content/uploads/2019/03/python-version.png)

惊喜！我的机器上安装了两个不同的版本。好吧，我想用 **Python 3.7.1** 版本进行开发。

## 创建项目

让我们创建项目并设置环境。

```
mkdir python-demo-project
cd python-demo-project 
```

首先安装 Pipenv:

```
pip3 install pipenv 
```

设置 pipenv 环境以使用 **Python 3.x** 版本。从当前目录运行以下命令:

```
pipenv install --three 
```

上面的命令将首先创建一个虚拟环境，然后创建一个 **Pipfile** ，然后创建 **Pipfile.lock** 。

Pipfile 包含一些有用的信息:

```
[[source]]
name = "pypi"
url = "https://pypi.org/simple"
verify_ssl = true

[dev-packages]

[packages]

[requires]
python_version = "3.7" 
```

重要的区块有`dev-packages`、`packages`和`requires`。任何仅用于开发目的的包将被添加到`dev-packages`下，任何其他用于生产的包将被添加到`packages`下。`requires`部分告诉您所需的最低版本是`3.7`。

首先，让我们安装几个用于生产的包。安装特定版本的软件包。

```
pipenv install pandas==0.24.1 
```

这将安装软件包并使用依赖关系详细信息更新锁定文件。您的**pip 文件**中的`packages`部分现在应该是这样的:

```
[packages]
pandas = "==0.24.1" 
```

让我们安装一个用于开发目的的包。

```
pipenv install numpy==1.16.2 --dev 
```

上述命令将在`dev-packages`部分安装 **numpy** 包。

要使用我们的虚拟环境运行任何命令，我们只需使用 **pipenv** 即可。例如，要打开`python3` shell，只需触发以下命令。

```
~ pipenv run python
Python 3.7.1 (default, Nov 6 2018, 18:46:03)
[Clang 10.0.0 (clang-1000.11.45.5)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

注意它显示的 python 版本。

现在我已经有了包管理。在我以后的文章中，我会分享更多我的心得。继续观看，不要忘记点击 like 并分享，以防您发现这很有用。