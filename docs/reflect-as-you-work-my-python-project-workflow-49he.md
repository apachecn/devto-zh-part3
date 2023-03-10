# 工作时反思:我的 Python 项目工作流

> 原文：<https://dev.to/flopi/reflect-as-you-work-my-python-project-workflow-49he>

[学徒模式](http://shop.oreilly.com/product/9780596518387.do)中的一种学徒模式是**边工作边反思**。这个模式是关于反思你是如何有规律地工作的。这样做通常可以让开发人员注意到他们的实践是如何变化的，甚至是如何没有变化。这不仅仅是观察你自己。正如书中所说，

> “不引人注目地观察你团队中的熟练工和大师级工匠。反思他们使用的实践、过程和技术，看看它们是否能与你经历的其他部分联系起来。即使作为一名学徒，你也可以通过密切观察更有经验的工匠的工作来发现新颖的想法。”第 36 页

我一直在思考自己的做法和周围人的做法。当我创建新的 Python 项目时，我所遵循的工作流程特别突出，因为我是通过与另一位工程师坐在一起学习的。我注意到他们做了什么，并问了一些问题。然后，我回到自己的办公桌，一边做更多的笔记，一边自己尝试。我多次遵循由此产生的工作流程，现在这些步骤从我的指尖轻松地流过。

我认为可能有方法来优化这个工作流程，但首先我要把它记在这里，以供未来的潜在读者和未来的我回顾！

附注:我在这里提到的许多额外细节都是从我的同事那里学来的。非常感谢他们与我分享他们所知道的💓

* * *

# 先决条件

*   `pyenv`已安装。

# 新 Python 项目清单

1.  安装特定的 Python 版本。
2.  创建项目目录。转到目录。
3.  为项目设置 Python 版本。
4.  创建一个虚拟环境。
5.  激活虚拟环境。
6.  安装依赖项。
7.  保存包。
8.  运行代码。

注意:这个工作流程应该可以在 MacOS 上运行。

* * *

## 先决条件:安装`pyenv`。

Mac OS X 自带 Python 2.7。如果你没有修改任何东西，你应该可以打开一个终端窗口，输入`python --version`，得到一些 2.7 版本。不过，您可能不想使用 OS(操作系统)附带的 Python 版本。出现这种情况有很多原因，比如版本可能已经过时。我甚至发现一个重要的图书馆不见了。

不仅要避免使用机器自带的 Python 版本，在工作中还需要同时安装几个不同版本的 Python。例如，由于某种库依赖性，可能一个代码库正在使用旧版本的 Python。升级您为该项目使用的 Python 版本可能需要对该项目进行一些重构，而您并没有优先考虑这些重构。与此同时，您可能会在其他项目中使用较新的 Python 版本，因为您想要利用闪亮的新特性。

对于 Python 开发人员来说，在机器上安装多个 Python 版本是一个现实的场景。有效地管理这些版本非常重要。

这里有关于如何安装`pyenv` [的说明](https://github.com/pyenv/pyenv)。

> 当您运行类似于`python`或`pip`的命令时，您的操作系统会在目录列表中搜索以该名称命名的可执行文件。这个目录列表位于一个名为`PATH`的环境变量中，列表中的每个目录用冒号分隔...

`pyenv`的工作原理是在你的`PATH`前面插入一个由*垫片*组成的目录，这样当你调用`python`或`pip`时，这些垫片是你的操作系统首先找到的。然后，你输入的命令被拦截并发送给`pyenv`，它根据一些规则决定你的命令使用哪个版本的 Python。

按照说明安装`pyenv`。即使您使用 Homebrew 安装，也要确保遵循基本 GitHub Checkout 下的其余安装后步骤。当我安装的时候，我发现我有一个`.bashrc`和`.bash_profile`。[这里](http://www.joshstaiger.org/archives/2005/07/bash_profile_vs.html)是一篇关于它们之间的区别以及何时使用其中一个文件的文章。如果按照说明输入`pyenv`后，没有得到如下结果，请返回并确保设置了另一个 bash 文件:

```
flo at MacBook-Pro in ~ $ pyenv
pyenv 1.2.8
Usage: pyenv <command> [<args>]

Some useful pyenv commands are:
   commands    List all available pyenv commands
   local Set or show the local application-specific Python version
   global      Set or show the global Python version
   shell       Set or show the shell-specific Python version
   install Install a Python version using python-build
   uninstall   Uninstall a specific Python version
   rehash      Rehash pyenv shims (run this after installing executables)
   version     Show the current Python version and its origin
   versions    List all Python versions available to pyenv
   which       Display the full path to an executable
   whence      List all Python versions that contain the given executable

See `pyenv help <command>' for information on a specific command.
For full documentation, see: https://github.com/pyenv/pyenv#readme 
```

Enter fullscreen mode Exit fullscreen mode

## 第一步:安装特定的 Python 版本。

假设我正在创建一个脚本，它将在 web 浏览器中打开最新的 xkcd 漫画。我准备用 Python 3.7.0 运行一下。

```
flo at MacBook-Pro in ~ $ pyenv install 3.7.0
python-build: use openssl from homebrew
python-build: use readline from homebrew
Downloading Python-3.7.0.tar.xz...
-> https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tar.xz
Installing Python-3.7.0...
python-build: use readline from homebrew
Installed Python-3.7.0 to /Users/flo/.pyenv/versions/3.7.0 
```

Enter fullscreen mode Exit fullscreen mode

## 第二步:创建项目目录。转到目录。

```
flo at MacBook-Pro in ~ $ mkdir Documents/comic-creator
flo at MacBook-Pro in ~ $ cd Documents/comic-creator/ 
```

Enter fullscreen mode Exit fullscreen mode

## 第三步:为项目设置 Python 版本。

先看文件夹里的文件，甚至是隐藏文件(`-la`会显示隐藏文件)。

```
flo at MacBook-Pro in .../comic-creator $ ls -la
total 0
drwxr-xr-x   2 flo  staff    64 Apr 12 21:12 .
drwx------+ 33 flo  staff  1056 Apr 12 21:12 .. 
```

Enter fullscreen mode Exit fullscreen mode

现在，为项目设置 Python 版本。现在你可以看到一个隐藏文件(隐藏文件以一个点开始)。往里面看`.python-version`，可以看到我们设定的版本。

```
flo at MacBook-Pro in .../comic-creator $ pyenv local 3.7.0
flo at MacBook-Pro in .../comic-creator $ ls -la
total 8
drwxr-xr-x   3 flo  staff    96 Apr 12 21:16 .
drwx------+ 33 flo  staff  1056 Apr 12 21:12 ..
-rw-r--r--   1 flo  staff     6 Apr 12 21:16 .python-version
flo at MacBook-Pro in .../comic-creator $ cat .python-version 
3.7.0 
```

Enter fullscreen mode Exit fullscreen mode

## 第四步:创建虚拟环境。

正如您的机器上可能安装了几个 Python 版本一样，您也可能安装了不同版本的 Python 包。想象一下你的一个项目的依赖图是这样的:

```
requests==2.21.0
  - certifi [required: >=2017.4.17, installed: 2019.3.9]
  - chardet [required: >=3.0.2,<3.1.0, installed: 3.0.4]
  - idna [required: >=2.5,<2.9, installed: 2.8]
  - urllib3 [required: >=1.21.1,<1.25, installed: 1.24.1] 
```

Enter fullscreen mode Exit fullscreen mode

在另一个项目中，你可能使用不同版本的`requests`，它依赖于不同版本的`certifi`。通过使用虚拟环境，我们可以保持包安装按项目隔离。

> 虚拟环境是一种 Python 环境，其中安装的 Python 解释器、库和脚本与其他虚拟环境中安装的解释器、库和脚本以及(默认情况下)安装在“系统”Python(即作为操作系统的一部分安装的系统)中的任何库相隔离。 [Python venv 文档](https://docs.python.org/3/library/venv.html)

因此，首先，您可以(再次)验证我们为项目正确地设置了 Python 版本。现在，通过调用`venv`创建一个虚拟环境，并调用这个新环境`venv`。您现在可以看到环境已经创建。

```
flo at MacBook-Pro in .../comic-creator $ python --version
Python 3.7.0
flo at MacBook-Pro in .../comic-creator $ python -m venv venv
flo at MacBook-Pro in .../comic-creator $ ls
venv 
```

Enter fullscreen mode Exit fullscreen mode

## 第五步:激活虚拟环境。

往里面看`venv`。然后，往里面看`venv/bin`。`bin`代表*二进制*。在类 Linux/Unix 系统中，运行系统所需的可执行程序在 [`/bin`](http://linfo.org/bin.html) 中找到。同样，Python 可执行程序也存储在`bin`中。

用`source`激活虚拟环境。

> `source`是一个 Unix 命令，它根据在当前上下文中执行的命令评估文件...通常“当前上下文”是一个终端窗口，用户在交互会话期间在其中键入命令。`source`命令可以缩写成一个点(。)在 Bash 和类似的 POSIX-ish shell 中。[维基百科](https://en.wikipedia.org/wiki/Source_(command))

这意味着，如果您打开一个新的终端窗口，您将需要再次获取`activate`文件来激活该窗口中的虚拟环境！还要注意，你可以输入`. venv/bin/activate`，它会做和`source venv/bin/activate`完全一样的事情。

```
flo at MacBook-Pro in .../comic-creator $ ls venv/
bin        include    lib        pyvenv.cfg
flo at MacBook-Pro in .../comic-creator $ ls venv/bin/
activate         activate.csh     activate.fish    easy_install     easy_install-3.7 pip              pip3             pip3.7           python           python3
flo at MacBook-Pro in .../comic-creator $ source venv/bin/activate 
```

Enter fullscreen mode Exit fullscreen mode

我们来看看`activate` :

```
flo at MacBook-Pro in .../comic-creator using virtualenv: venv $ cat venv/bin/activate
# This file must be used with "source bin/activate" *from bash*
# you cannot run it directly

deactivate () {
    # reset old environment variables
    if [ -n "${_OLD_VIRTUAL_PATH:-}" ] ; then
        PATH="${_OLD_VIRTUAL_PATH:-}"
        export PATH
        unset _OLD_VIRTUAL_PATH
    fi
    if [ -n "${_OLD_VIRTUAL_PYTHONHOME:-}" ] ; then
        PYTHONHOME="${_OLD_VIRTUAL_PYTHONHOME:-}"
        export PYTHONHOME
        unset _OLD_VIRTUAL_PYTHONHOME
    fi

    # This should detect bash and zsh, which have a hash command that must
    # be called to get it to forget past commands.  Without forgetting
    # past commands the $PATH changes we made may not be respected
    if [ -n "${BASH:-}" -o -n "${ZSH_VERSION:-}" ] ; then
        hash -r
    fi

    if [ -n "${_OLD_VIRTUAL_PS1:-}" ] ; then
        PS1="${_OLD_VIRTUAL_PS1:-}"
        export PS1
        unset _OLD_VIRTUAL_PS1
    fi

    unset VIRTUAL_ENV
    if [ ! "$1" = "nondestructive" ] ; then
    # Self destruct!
        unset -f deactivate
    fi
}

# unset irrelevant variables
deactivate nondestructive

VIRTUAL_ENV="/Users/flo/Documents/comic-creator/venv"
export VIRTUAL_ENV

_OLD_VIRTUAL_PATH="$PATH"
PATH="$VIRTUAL_ENV/bin:$PATH"
export PATH

# unset PYTHONHOME if set
# this will fail if PYTHONHOME is set to the empty string (which is bad anyway)
# could use `if (set -u; : $PYTHONHOME) ;` in bash
if [ -n "${PYTHONHOME:-}" ] ; then
    _OLD_VIRTUAL_PYTHONHOME="${PYTHONHOME:-}"
    unset PYTHONHOME
fi

if [ -z "${VIRTUAL_ENV_DISABLE_PROMPT:-}" ] ; then
    _OLD_VIRTUAL_PS1="${PS1:-}"
    if [ "x(venv) " != x ] ; then
        PS1="(venv) ${PS1:-}"
    else
    if [ "`basename \"$VIRTUAL_ENV\"`" = "__" ] ; then
        # special case for Aspen magic directories
        # see http://www.zetadev.com/software/aspen/
        PS1="[`basename \`dirname \"$VIRTUAL_ENV\"\``] $PS1"
    else
        PS1="(`basename \"$VIRTUAL_ENV\"`)$PS1"
    fi
    fi
    export PS1
fi

# This should detect bash and zsh, which have a hash command that must
# be called to get it to forget past commands.  Without forgetting
# past commands the $PATH changes we made may not be respected
if [ -n "${BASH:-}" -o -n "${ZSH_VERSION:-}" ] ; then
    hash -r
fi 
```

Enter fullscreen mode Exit fullscreen mode

## 第六步:安装依赖项。

如果你没有遇到过“dependencies ”,这个词通常用来表示某物依赖于另一物...有道理。在我们的例子中，我们的 Python 项目将依赖于安装尚未与 Python 3.7.0 捆绑在一起的各种库。

这是我们代码的样子:

```
import json
import sys
import webbrowser

import requests

# url of latest xkcd comic URL = 'http://xkcd.com/info.0.json'

if __name__ == '__main__':
    response = requests.get(URL)
    if response.status_code == requests.codes.ok:
        content = json.loads(response.text)
        print('Comic is located at {}'.format(content['img']))
        webbrowser.open(content['img'])
    else:
        print('Error: \n {}'.format(response.text))
        sys.exit() 
```

Enter fullscreen mode Exit fullscreen mode

在项目中创建一个文件`comic_popup.py`,并添加以下代码。如果你试图运行代码，你会得到一个错误。`requests`模块未安装。让我们安装它。

```
flo at MacBook-Pro in .../comic-creator using virtualenv: venv $ touch comic_popup.py
flo at MacBook-Pro in .../comic-creator using virtualenv: venv $ ls
comic_popup.py venv
flo at MacBook-Pro in .../comic-creator using virtualenv: venv $ pip install requests
Collecting requests
  Using cached https://files.pythonhosted.org/packages/7d/e3/20f3d364d6c8e5d2353c72a67778eb189176f08e873c9900e10c0287b84b/requests-2.21.0-py2.py3-none-any.whl
Collecting chardet<3.1.0,>=3.0.2 (from requests)
  Using cached https://files.pythonhosted.org/packages/bc/a9/01ffebfb562e4274b6487b4bb1ddec7ca55ec7510b22e4c51f14098443b8/chardet-3.0.4-py2.py3-none-any.whl
Collecting urllib3<1.25,>=1.21.1 (from requests)
  Using cached https://files.pythonhosted.org/packages/62/00/ee1d7de624db8ba7090d1226aebefab96a2c71cd5cfa7629d6ad3f61b79e/urllib3-1.24.1-py2.py3-none-any.whl
Collecting idna<2.9,>=2.5 (from requests)
  Using cached https://files.pythonhosted.org/packages/14/2c/cd551d81dbe15200be1cf41cd03869a46fe7226e7450af7a6545bfc474c9/idna-2.8-py2.py3-none-any.whl
Collecting certifi>=2017.4.17 (from requests)
  Using cached https://files.pythonhosted.org/packages/60/75/f692a584e85b7eaba0e03827b3d51f45f571c2e793dd731e598828d380aa/certifi-2019.3.9-py2.py3-none-any.whl
Installing collected packages: chardet, urllib3, idna, certifi, requests
Successfully installed certifi-2019.3.9 chardet-3.0.4 idna-2.8 requests-2.21.0 urllib3-1.24.1
You are using pip version 10.0.1, however version 19.0.3 is available.
You should consider upgrading via the 'pip install --upgrade pip' command. 
```

Enter fullscreen mode Exit fullscreen mode

## 第七步:保存包。

请注意当您输入`pip freeze`时打印的内容。该命令以需求格式({library-name}={version})输出已安装的软件包。在下一行中，*使用 [`>`](https://en.wikipedia.org/wiki/Redirection_(computing)) 将输出重定向到一个名为`requirements.txt`的文件中。如果文件已经存在，单个`>`将覆盖文件的内容。使用`>>`将追加到一个已经存在的文件的内容。*

你不必调用这个文件`requirements.txt`,但这是大多数 Python 开发人员使用的，所以要遵守约定！更多关于需求文件[这里](https://pip.pypa.io/en/stable/user_guide/)。

您可能还注意到,`requests`并不是唯一由`pip freeze`输出的库。其他的库是`requests`所依赖的库，所以当你安装`requests`时，你必须安装其他的库才能让`requests`工作。这些其他的库被称为*传递依赖*。

```
flo at MacBook-Pro in .../comic-creator using virtualenv: venv $ pip freeze
certifi==2019.3.9
chardet==3.0.4
idna==2.8
requests==2.21.0
urllib3==1.24.1
You are using pip version 10.0.1, however version 19.0.3 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
flo at MacBook-Pro in .../comic-creator using virtualenv: venv $ pip freeze > requirements.txt
You are using pip version 10.0.1, however version 19.0.3 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
flo at MacBook-Pro in .../comic-creator using virtualenv: venv $ cat requirements.txt 
certifi==2019.3.9
chardet==3.0.4
idna==2.8
requests==2.21.0
urllib3==1.24.1
flo at MacBook-Pro in .../comic-creator using virtualenv: venv $ pip --help

Usage:   
  pip <command> [options]

Commands:
  install                     Install packages.
  download                    Download packages.
  uninstall                   Uninstall packages.
  freeze                      Output installed packages in requirements format.
  list                        List installed packages.
  show                        Show information about installed packages.
  check                       Verify installed packages have compatible dependencies.
  config                      Manage local and global configuration.
  search                      Search PyPI for packages.
  wheel                       Build wheels from your requirements.
  hash                        Compute hashes of package archives.
  completion                  A helper command used for command completion.
  help                        Show help for commands.

General Options:
  -h, --help                  Show help.
  --isolated                  Run pip in an isolated mode, ignoring environment variables and user configuration.
  -v, --verbose               Give more output. Option is additive, and can be used up to 3 times.
  -V, --version               Show version and exit.
  -q, --quiet                 Give less output. Option is additive, and can be used up to 3 times (corresponding to WARNING, ERROR, and CRITICAL logging levels).
  --log <path>                Path to a verbose appending log.
  --proxy <proxy>             Specify a proxy in the form [user:passwd@]proxy.server:port.
  --retries <retries>         Maximum number of retries each connection should attempt (default 5 times).
  --timeout <sec>             Set the socket timeout (default 15 seconds).
  --exists-action <action>    Default action when a path already exists: (s)witch, (i)gnore, (w)ipe, (b)ackup, (a)bort).
  --trusted-host <hostname>   Mark this host as trusted, even though it does not have valid or any HTTPS.
  --cert <path>               Path to alternate CA bundle.
  --client-cert <path>        Path to SSL client certificate, a single file containing the private key and the certificate in PEM format.
  --cache-dir <dir>           Store the cache data in <dir>.
  --no-cache-dir              Disable the cache.
  --disable-pip-version-check
                              Don't periodically check PyPI to determine whether a new version of pip is available for download. Implied with --no-index.
  --no-color                  Suppress colored output 
```

Enter fullscreen mode Exit fullscreen mode

## 第八步:运行代码。

就是这样。您现在应该可以运行代码了。一旦安装了所需的依赖项，您就可以运行它，但是不要忘记保存您的需求！

```
flo at MacBook-Pro in .../comic-creator using virtualenv: venv $ python comic_popup.py 
Comic is located at https://imgs.xkcd.com/comics/election_commentary.png 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您将代码保存到 repo 中，任何人都可以提取代码并运行它。添加一个`README.md`并包含使用哪个版本的 Python 来运行代码。下一个开发人员将设置正确的 Python 版本，并通过运行`pip install -r requirements.txt`来安装需求。

不要在回购中包含`.python-version`文件，因为该文件是特定于`pyenv`的，其他开发人员可能有他们自己的方式来管理 Python 版本。

> 作为一个经验法则，我不包括特定于我的文件，比如各种 ide(集成开发环境)的配置文件，因为它们会使存储库变得混乱。通过添加和配置一个`.gitignore`文件来忽略存储库中的这些文件。

* * *

这是我开始一个 Python 项目时的开发工作流程！我在我认为合适的地方加入了一些开发人员的最佳实践。我还尽可能解释了我认为合适的上下文。我鼓励你尝试做同一件事的不同方法，看看每种方法的利弊。

有什么问题尽管问！我很乐意谈论最佳实践以及对您有效的方法。我们工作流程的许多部分都是约定俗成的，或者因为这是我们最初学习的方式，或者我们并不了解更多。我很想收到你的来信！