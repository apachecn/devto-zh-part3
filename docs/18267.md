# 在 Pypi 上发布包

> 原文：<https://dev.to/mraza007/publishing-packages-on-pypi-2b37>

作为一名中级/初级 python 开发人员，您总是想知道如何发布自己的 Python 包，以便与您的朋友和同事分享。
在这篇文章中，我将带你了解在 [PyPi](https://pypi.org/) 上发布你自己的包
的过程。

#### 那么 PyPi 是什么呢？

PyPi 是 Python 包索引。这更像是 npm 或家酿，你可以找到不同的
包，如 Flask，Django，Tweepy 等等。大多数包都是开源的
，PyPi 本身也是开源的，由开发人员在空闲时间维护，这
让 PyPi 变得很棒。

#### 让我们开始写我们的第一个包。

这将是一个简单的 python 包，它将计算作为命令行参数传递的数字的平方和立方。

为了开始，我们需要。

*   Python 已安装
*   这将允许我们连接到 pypi 并发布我们的包。
*   这个包将解析我们作为命令行参数传递的参数。

让我们开始编写代码。

```
"""calc

Usage:
    calc.py square <num>
    calc.py cube  <num>
    calc.py (-h | --help)

Options:
    -h --help     Show this screen.

"""
from docopt import docopt 

def square(num):
  print(num**2) 

def cube(num):
  print(num**3)

if __name__ == '__main__':                                                                                                                              
  arguments = docopt(__doc__)
  if arguments['square']:
    square(int(arguments['<num>']))
  elif arguments['cube']:
    cube(int(arguments['<num>'])) 
```

*   所以在编写了简单的包之后，我们需要创建一个名为`setup.py`的文件。这个文件将允许我们安装包，它将被 PyPi 使用。
*   此外，我们需要创建一个 README 文件，其中包含关于软件包安装和使用的说明。

*   这就是我们的 setup.py 文件的样子，它将包含关于我们的包的细节。

```
 import setuptools

with open("README.md", "r") as fh:
    long_description = fh.read()

setuptools.setup(
    name="Calculator",
    version="0.0.1",
    author="Your Name",
    author_email="Your Email",
    description="Description regarding the package",
    long_description=long_description,
    long_description_content_type="text/markdown",
    url="Project_url",
    packages=setuptools.find_packages(),
    classifiers=[
        "Programming Language :: Python :: 3",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent",
    ],
) 
```

*   在创建了一个`setup.py`之后，现在我们将运行这个命令来生成我们的包`.tar`文件。

```
python3 setup.py sdist 
```

*   这将创建一个名为`dist/`的文件夹，其中将包含我们的包，现在我们将使用 twine 上传我们的包。

```
twine upload dist/
# This will prompt username and password for pypi 
```

## 终于你发布了你的第一个 PyPi 包。

我希望你喜欢这篇文章，如果你认为我错过了什么，请随时在 [twitter](https://twitter.com/)
上给我发消息，分享给你的朋友和同事。