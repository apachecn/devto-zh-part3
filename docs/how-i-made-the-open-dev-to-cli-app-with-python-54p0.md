# 我如何用 Python 开发 open-dev.to CLI 应用程序

> 原文：<https://dev.to/wangonya/how-i-made-the-open-dev-to-cli-app-with-python-54p0>

自从我开始学习 Python 以来，我一直惊讶于你可以用它做多少事情，以及一旦你熟悉了它，它实际上是多么容易。我一直喜欢在终端上工作，所以为了让我的学习更有趣，我学会了如何用 [Click](https://click.palletsprojects.com/en/7.x/) 制作 cli 应用程序，并在去年年底制作了一个 Chuck Norris 笑话应用程序:

[![wangonya](img/f24a24d0096b366df3522b3e07664d45.png)](/wangonya) [## 诺里斯-cli

### kelvin wango nya 12 月 27 日 181 分钟读数

#showdev #beginners #python #cli](/wangonya/norris-cli-4ifk)

我在 PyPi 上发布了这个应用程序，作为一个 Python 初学者，你可以想象我在 PyPi 上看到 T2 做的东西时的喜悦。PyPi！😄牛逼的感觉。

昨天，我不得不暂停工作，决定打开 Dev.to。如果我不需要它来做任何事情，我喜欢关闭浏览器，以避免分心。所以我切换工作区，打开浏览器，点击 Dev.to 书签，我就在那里了。这个想法突然出现在我的脑海中:如果我有一个从终端打开 dev.to 的命令，我就可以绕过这一切。我之前已经在我的终端上了，所以这看起来是个好主意😄。当然，这可以在任何网站上实现——我只是想到了 Dev.to，因为这是让我首先经历这个过程的原因。

[![wangonya](img/f24a24d0096b366df3522b3e07664d45.png)](/wangonya) [## 从终端打开 Dev.to🎉

### kelvin wango nya 2019 年 4 月 9 日 11 分钟读数

#showdev #python #cli](/wangonya/open-dev-to-from-your-terminal-33g2)

像往常一样，社区非常支持！

[![tomasforsman profile image](img/e632e4a52785185d0f10e65d191ed007.png) ](/tomasforsman) [ Tomas Forsman ](/tomasforsman) • [<time datetime="2019-04-09T22:08:47Z"> Apr 9 '19 </time>](https://dev.to/tomasforsman/comment/a2dk) 

这很有趣=)你应该补充一下这个过程，你需要做些什么，以及如何制作一个包来分享。

[![wangonya profile image](img/a75901410487c81d9e7040e9efcb1600.png) ](/wangonya) [ Kelvin Wangonya ](/wangonya) • [<time datetime="2019-04-10T03:59:07Z"> Apr 10 '19 </time>](https://dev.to/wangonya/comment/a2gm) 

我会在下一篇文章中这样做。谢谢！😊

所以我是这样做的。

如果您想继续，现在是时候创建一个新目录，`cd`并创建您的虚拟环境。

```
$  mkdir open-dev.to
$  cd open-dev.to
$  virtualenv venv # pip install virtualenv if you don't have it
$  source venv/bin/activate
(venv) $  mkdir app
(venv) $  touch app/__init__.py # this is where we'll write our code
(venv) $  touch requirements.txt # this will hold the project's requirements 
```

Enter fullscreen mode Exit fullscreen mode

## `click`&`webbrowser`模块

整个事情的核心是这两行代码:

```
import click
import webbrowser 
```

Enter fullscreen mode Exit fullscreen mode

[`webbrowser`](https://docs.python.org/3/library/webbrowser.html) 提供了打开浏览器的权限， [`click`](https://click.palletsprojects.com/en/7.x/) 使其成为 cli app。

python 自带了`webbrowser`，所以我们只需要安装`click`。

```
(venv) $  pip install click
(venv) $  pip freeze > requirements.txt # add the installed module to our requirements file 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经有了我们需要的东西，我们可以继续写代码了。将以下代码添加到`app/__init__.py`。我将解释每一行的作用。

```
import click
import webbrowser

@click.command()
@click.option('--tag', '-t', help='add a tag')
def main(tag):
    """
    Open a new dev.to browser tab on the browser
    """
    if tag:
        url = 'https://dev.to/t/{}'.format(tag)
    else:
        url = 'https://dev.to'

    webbrowser.open(url, new=2)

if __name__ == "__main__":
    main() 
```

Enter fullscreen mode Exit fullscreen mode

真的就这么简单！即使是初学者也能理解这段代码的作用。

**`@click.command()`**
这个点击装饰器把`main()`函数转换成 cli 命令。没有它，它只是一个普通的 python 函数。但是有了它，它就变成了可以作为命令行实用程序调用命令。

**`@click.option('--tag', '-t', help='add a tag')`**
`@click.option()`扩展命令，使其能够接受额外的参数。在这种情况下，`--tag`或其简写版本`-t`是允许的。`help`一文解释了这个论点的作用。

**`def main(tag):`**
这是我们应用程序的入口，所有神奇的事情都发生在这里。该函数(现在是一个命令)带有一个可选的`tag`参数，它决定了要打开的 url，如下面几行的`if else`所示。

**`"""Open a new dev.to browser tab on the browser"""`**
[![comment](img/8ddf2c5a4b5cf46d4bad2a50c0fe6ac2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cGKrY5Sa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.redd.it/iuy9fxt300811.png)

**`webbrowser.open(url, new=2)`**
打开请求的网址。`new=2`确保这是在一个新的标签中完成的。

最后，当脚本运行时，`if __name__`块运行`main()`函数。

有了上面的代码，只需运行`__init__.py`来看看它是如何工作的。

```
$  python app/__init__.py 
```

Enter fullscreen mode Exit fullscreen mode

一个新的 Dev.to 选项卡应该会自动在您的浏览器上打开。

```
$  python app/__init__.py --help
Usage: __init__.py [OPTIONS]

  Open a new dev.to browser tab on the browser

Options:
  -t, --tag TEXT  add a tag
  --help          Show this message and exit. 
```

Enter fullscreen mode Exit fullscreen mode

让我们尝试传入一个标签。

```
$  python app/__init__.py --tag react 
```

Enter fullscreen mode Exit fullscreen mode

`https://dev.to/t/react`应该是打开了😀。

但是我们不想每次都跑。在下一篇文章中，我将展示如何使用`setuptools`来捆绑脚本，以便我们可以在本地安装它，并使用简单的`dev.to`命令来运行脚本，而不是漫长的`python app/__init__.py`。我还将展示如何在 PyPi 上发布一个包。

[![eliot](img/1c519c1c1e633229120ec3de94373499.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2r_PgtVF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/profile_images/1048098761552142336/goBBdAW1.jpg)