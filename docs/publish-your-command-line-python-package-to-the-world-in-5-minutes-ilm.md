# 在 5 分钟内向全世界发布您的命令行 Python 包

> 原文：<https://dev.to/mkfeuhrer/publish-your-command-line-python-package-to-the-world-in-5-minutes-ilm>

你有没有创造出令人惊奇的东西，并希望每个人都使用它？

是的，你可以通过在 PyPi 上分享你的包来轻松做到这一点。遵循指南发布您的第一个 python 包。

Python 包索引(PyPI)是 Python 编程语言的软件仓库。PyPI 帮助您找到并安装由 Python 社区开发和共享的软件。它目前有超过 173，000 个项目。

如果你熟悉 python，你一定遇到过-

```
pip install <package-name> 
```

要发布项目最小结构需要以下结构-

```
counter
        /__main__.py
README.md
LICENSE.txt
setup.py 
```

我正在制作一个名为 counter 的包。(你可以用你的)

在 setup.py 中，您需要指定软件包的所有组件。看一下文件

```
 setup(name='counter',
      version='1.0.0',
      description='A Python package for getting list of numbers from 1 to 100',
      url='https://github.com/<username>/<package-name>',
      entry_points={'console_scripts': ['counter= counter.__main__:main']},
      keywords=['counter', 'programming language ranking', 'index', 'programming language'],
      author='Mohit Khare (mkfeuhrer)',
      author_email='mohitfeuhrer@gmail.com',
      license='MIT',
      packages=['counter'],
      install_requires=[
          'requests'
      ],
      ) 
```

大多数字段都是通过关键字名称表示的，如名称、版本等。

*   Url 字段表示代码库 url。
*   字段 entry_points 表示我们的包的入口点。

*   柜台。 **main** :main 指定 **main** 中的主功能。计数器文件夹内的 py。(请用您的包装名称替换计数器)

你可以在这里寻找细节[。](https://setuptools.readthedocs.io/en/latest/setuptools.html#developer-s-guide)

下一步是制作你的许可证

```
MIT License

Copyright (c) <year> <your name>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE. 
```

现在用一些降价文本填充你的 README.md。

```
#counter

A Python package for getting list of numbers from 1 to 100.

### Dependencies

+ requests

## Contributors

- [Mohit Khare](https://github.com/mkfeuhrer) 
```

现在我们开始编写命令行解析器。

先说**主**。派-

```
def main(argv=None):

    """
    :desc: Entry point method
    """
    if argv is None:
        argv = sys.argv

    try:
        parser = create_parser()

if __name__ == '__main__':
    sys.exit(main(sys.argv)) 
```

我们初始化了我们的**主**。py 调用接受参数的主函数。
我们现在只需要一个 create_parser()函数。

# 开发你的解析器

我们将使用 [argparse](https://docs.python.org/3/howto/argparse.html#introducing-positional-arguments) 包进行解析。

```
import argparse

def create_parser():
    parser = argparse.ArgumentParser()
    parser.add_argument('--counttill100',required=False,action='store_true',help='Print count till 100')

    return parser    

def main(argv=None):    

    """
    :desc: Entry point method
    """
    if argv is None:
        argv = sys.argv

    try:
        parser = create_parser()
                args = parser.parse_args(argv[1:])

        # Arguments initialization
        counttill100= args.counttill100

                # Parser check
        if counttill100:
            counttill100fun()

                except KeyboardInterrupt:
        print('\nGood Bye.')
            return 0

def counttill100fun():
    for i in range(1,101):
        print(i)

if __name__ == '__main__':
    sys.exit(main(sys.argv)) 
```

我们添加了 counttill100fun()函数来打印从 1 到 100 的数字。你可以跑

```
python __main__.py --counttill100 
```

耶！！我们成功地构建了解析器。

# 发布您的包

如果您没有帐户，请在 [PyPi](https://pypi.org/) 上注册。

现在，通过在您的根文件夹中运行它来生成分发包。

```
pip install --user --upgrade setuptools wheel
python3 setup.py sdist 
```

将您的包上传到 PyPi，系统会提示您输入用户名和密码。

```
twine upload dist/* 
```

万岁！！你刚刚发布了你的第一个包。

[![Hackerman](img/939ff1ce6c6a5413904a268273f94f2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rQxczL0C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.kym-cdn.com/entries/icons/facebook/000/021/807/4d7.jpg)

您可以通过臭名昭著的命令安装它

```
pip install <your-package-name> 
```

是时候建立一些新的项目并开始做出贡献了。

PS:检查我的包在这里:[最富有的](https://github.com/mkfeuhrer/richest)或者做一个简单的

```
pip install richest 
```

快乐发展！！