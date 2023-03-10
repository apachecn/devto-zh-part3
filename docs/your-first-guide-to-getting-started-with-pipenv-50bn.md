# 您的第一本“pipenv”入门指南

> 原文：<https://dev.to/yukinagae/your-first-guide-to-getting-started-with-pipenv-50bn>

# “pipenv”是什么？

其他编程语言中有许多包管理器工具，例如:

*   红宝石
    *   大错
*   服务器端编程语言（Professional Hypertext Preprocessor 的缩写）
    *   设计者
*   NodeJS
    *   npm
    *   故事
*   锈
    *   货物

如果你知道以上任何一种工具，就很容易理解它是什么。
`pipenv`和那些工具一样，只是 Python 的一个包管理工具。

# 为什么是“pipenv”？

官方文档[Pipenv:Python Dev Workflow for Humans](https://pipenv.readthedocs.io/en/latest/)和这篇博客文章[为什么 Python Dev 应该使用 Pipenv](https://opensource.com/article/18/2/why-python-devs-should-use-pipenv) 会告诉我们为什么我们应该使用 Pipenv。

“之前”pipenv 工作流的两个主要问题如下:

*   不再需要单独使用`pip`和`virtualenv`！
*   `requirements.txt`有问题:参见[更好的 Pip 工作流程](https://www.kennethreitz.org/essays/a-better-pip-workflow)
    *   没有指定版本->开发/生产环境每次都有不同的版本！
    *   带版本指定-> `--upgrade`硬！

如果我们回顾一下 Python 包管理工具的历史，之前有很多工具，这是很可怕的。

*   包管理器
    *   易于安装
    *   点
*   许多许多虚拟环境工具
    *   virtualenv(虚拟环境)
    *   文夫
    *   pyenv
    *   pyenv-virtualenv
    *   等等

终于，`pipenv`来拯救我们了！

## 设置管道

检查 Python 版本(应该是 3.x)

```
$ python --version
Python 3.6.0 
```

检查 pip 版本

```
$ pip --version
pip 18.1 
```

安装管道

```
brew install pipenv 
```

检查管道版本

```
$ pipenv --version
pipenv, version 2018.11.26 
```

成功:pipenv 已安装！

## pipenv 工作流

安装依赖项(pipenv 自动检测`requirements.txt`并解决依赖项)

```
$ cd [YOUR PROJECT]
$ pipenv install 
```

应该创建`Pipfile`和`Pipfile.lock`

*   Pipfile:所有已安装软件包的列表

(Pipfile 示例)

```
[[source]]
name = "pypi"
url = "https://pypi.org/simple"
verify_ssl = true

[dev-packages]

[packages]
jupyter = "*"
seaborn = "*"
numpy = "*"
pandas = "*"
plotly = "*"
scipy = "*"
six = "*"
sklearn = "*"
pyspark = "*"
flake8 = "*"
autopep8 = "*"
Flask = "*"

[requires]
python_version = "3.7" 
```

*   Pipfile.lock:维护一个适当的依赖关系和版本的子包

(Pipfile.lock 示例)

```
{  "_meta":  {  "hash":  {  "sha256":  "xxxxxxxxxxxxxxxxxxxxxxxx"  },  "pipfile-spec":  6,  "requires":  {  "python_version":  "3.7"  },  "sources":  [  {  "name":  "pypi",  "url":  "https://pypi.org/simple",  "verify_ssl":  true  }  ]  },  "default":  {  "appnope":  {  "hashes":  [  "sha256: xxxxxxxxxxxxxxxxxxxxxxxx",  "sha256: xxxxxxxxxxxxxxxxxxxxxxxx"  ],  "markers":  "sys_platform == 'darwin'",  "version":  "==0.1.0"  },  "attrs":  {  "hashes":  [  "sha256: xxxxxxxxxxxxxxxxxxxxxxxx",  "sha256: xxxxxxxxxxxxxxxxxxxxxxxx"  ],  "version":  "==19.1.0"  },  "autopep8":  {  "hashes":  [  "sha256: xxxxxxxxxxxxxxxxxxxxxxxx"  ],  "index":  "pypi",  "version":  "==1.4.4"  ... 
```

现在，运行您的 python 脚本(当您使用`pipenv run` )
时可以使用已安装的包)

```
$ pipenv run python [YOUR PYTHON MAIN SCRIPT]
Pipfile.lock not found, creating…
Locking [dev-packages] dependencies…
Locking [packages] dependencies…
✔ Success! 
Updated Pipfile.lock (980232)!
Installing dependencies from Pipfile.lock (980232)…
  🐍   ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 73/73 — 00:01:22
To activate this project's virtualenv, run pipenv shell.
Alternatively, run a command inside the virtualenv with pipenv run. 
```

感谢阅读！如果你喜欢这篇文章，请随意点击那个按钮👏。

# 参考文献

*   [Pipenv:面向人类的 Python 开发工作流](https://pipenv.readthedocs.io/en/latest/):官方文档
*   [Python 开发者为什么要用 Pipenv](https://opensource.com/article/18/2/why-python-devs-should-use-pipenv) :为什么要用 Pipenv？
*   pipenv 的搭便车指南
*   使用 pipenv 进行 Python 依赖管理 : pipenv 指南
*   [关于 Pipenv 的五个神话](https://medium.com/@grassfedcode/five-myths-about-pipenv-698c5f198e4b)