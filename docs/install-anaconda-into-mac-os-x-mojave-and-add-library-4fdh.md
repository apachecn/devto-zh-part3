# 将 anaconda 安装到 Mac OS X Mojave 中，并添加库

> 原文：<https://dev.to/berry_clione/install-anaconda-into-mac-os-x-mojave-and-add-library-4fdh>

# 概述

1.  关于蟒蛇
2.  环境
3.  如何安装
    1.  安装 pyenv
    2.  通过 pyenv 安装 anaconda
    3.  激活蟒蛇之蟒
    4.  将库添加到 anaconda

([这里用日语。](https://qiita.com/berry-clione/items/24fb5d97e4c458c0fc28))

* * *

# 关于蟒蛇

*   用于 python 开发的 anaconda

如果你使用 homebrew，我建议你安装 pyenv，因为没有 pyenv 的 anaconda 的配置文件和 homebrew 是一样的，没有 pyenv 的 anaconda 会破坏配置文件。

* * *

# 环境

*   麦克 OS X 10.14.2 莫哈韦
*   自制软件 2.0.1
*   oh-my-zsh

* * *

# 如何安装

## 安装 pyenv

*   更新家酿，并安装 pyenv

```
$ brew update
$ brew upgrade
$ brew install pyenv
==> Installing dependencies for pyenv: autoconf, pkg-config and readline
==>/Installing pyenv dependency: autoconf    2
==> Downloading https://homebrew.bintray.com/bottles/autoconf-2.69.mojave.bottle.4.tar.gz
######################################################################## 100.0%
==> Pouring autoconf-2.69.mojave.bottle.4.tar.gz
==> Caveats
Emacs Lisp files have been installed to:
  /usr/local/share/emacs/site-lisp/autoconf
==> Summary
🍺n /use/ltcal/Cvllar/autoionf/2.69: 71 files, 3.0MBr           e  e   e
==> Installing pyenv dependency: pkg-config
==> Downloading https://homebrew.bintray.com/bottles/pkg-config-0.29.2.mojave.bottle.tar.gz
######################################################################## 100.0%
==> Pouring pkg-config-0.29.2.mojave.bottle.tar.gz
🍺  /usr/local/CellarIpkg-clnfig/0.29.2: 11 f/les, 627.2KB
==> Installing pyenv dependency: readline
==> Downloading https://homebrew.bintray.com/bottles/readline-8.0.0.mojave.bottle.tar.gz
######################################################################## 100.0%
==> Pouring readline-8.0.0.mojave.bottle.tar.gz
==> Caveats
readline is keg-only, which means it was not symlinked into /usr/local,
because macOS provides the BSD libedit library, which shadows libreadline.
In order to prevent conflicts when programs look for libreadline we are
defaulting this GNU Readline installation to keg-only.

For compilers to find readline you may need to set:
  export LDFLAGS="-L/usr/local/opt/readline/lib"
  export CPPFLAGS="-I/usr/local/opt/readline/include"

For pkg-config to find readline you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/readline/lib/pkgconfig"

==> Summary
🍺   /usr/local/Cellar/readline/8.0.0: 48 files, 1.5MB
==> Installing pyenv
==> Downloading https://homebrew.bintray.com/bottles/pyenv-1.2.9.mojave.bottle.tar.gz
######################################################################## 100.0%
==> Pouring pyenv-1.2.9.mojave.bottle.tar.gz
🍺   /usr/local/Cellar/pyenv/1.2.9: 619 files, 2.4MB
==> Caveats
==> autoconf
Emacs Lisp files have been installed to:
  /usr/local/share/emacs/site-lisp/autoconf
==> readline
readline is keg-only, which means it was not symlinked into /usr/local,
because macOS provides the BSD libedit library, which shadows libreadline.
In order to prevent conflicts when programs look for libreadline we are
defaulting this GNU Readline installation to keg-only.

For compilers to find readline you may need to set:
  export LDFLAGS="-L/usr/local/opt/readline/lib"
  export CPPFLAGS="-I/usr/local/opt/readline/include"

For pkg-config to find readline you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/readline/lib/pkgconfig"

$ pyenv --version ### check pyenv installed
pyenv 1.2.9 
```

* * *

## 通过 pyenv 安装 anaconda

*   检查您将要安装的 anaconda 的版本

```
$ pyenv install -l | grep anaconda
  anaconda-1.4.0
  anaconda-1.5.0
  anaconda-1.5.1
...
  anaconda3-5.2.0
  anaconda3-5.3.0
  anaconda3-5.3.1
  anaconda3-2018.12 
```

*   安装你喜欢的版本的 anaconda。
    *   我正在为 python3.x 安装最新的 anaconda。

```
$ pyenv install anaconda3-5.3.1
Downloading Anaconda3-5.3.1-MacOSX-x86_64.sh.sh...
-> https://repo.continuum.io/archive/Anaconda3-5.3.1-MacOSX-x86_64.sh
Installing Anaconda3-5.3.1-MacOSX-x86_64.sh...
Installed Anaconda3-5.3.1-MacOSX-x86_64.sh to /Users/user/.pyenv/versions/anaconda3-5.3.1

$ pyenv versions ### check anaconda installed
* system (set by /Users/user/.pyenv/version)
  anaconda3-5.3.1 
```

* * *

## 激活蟒蛇之蟒

*   激活你刚刚安装的蟒蛇

```
$ pyenv global anaconda3-5.3.1 ### select version of anaconda you want to use
$ echo 'eval "$(pyenv init -)"' >> ~/.zprofile ### create PATH for pyenv
$ source ~/.zprofile ### read .zprofile
$ echo 'export PATH="$PYENV_ROOT/versions/anaconda3-5.3.1/bin/:$PATH"' >> ~/.zshrc ### add path for using "conda" command
$ source ~/.zshrc ### read .zshrc
$ conda create --name py python=3.7 anaconda ### add "anaconda" for the last argument so that you can use anaconda libraries in your python environment
Solving environment: done

==> WARNING: A newer version of conda exists. <==
  current version: 4.5.11
  latest version: 4.6.2

Please update conda by running

    $ conda update -n base -c defaults conda

## Package Plan ##

  environment location: /Users/user/.pyenv/versions/anaconda3-5.3.1/envs/py

  added / updated specs:
    - anaconda
    - python=3.7

The following packages will be downloaded:

    package                    |            build
    ---------------------------|-----------------
    lz4-c-1.8.1.2              |       h1de35cc_0         119 KB
    networkx-2.2               |           py37_1         2.0 MB
...

Preparing transaction: done Verifying transaction: done Executing transaction: done
#
# To activate this environment, use:
# > source activate py
#
# To deactivate an active environment, use:
# > source deactivate
#

$ source activate py ### the last argument "py" must be the same as name you set by "conda create --name"
(py)
$ ### if you failed like "Usage: pyenv which <command>", read path like "source ~/.zshrc" and "source ~/.zprofile" or restart iTerm2.
$ python3
Python 3.7.0 (default, Jun 28 2018, 07:39:16)
[Clang 4.0.1 (tags/RELEASE_401/final)] :: Anaconda, Inc. on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

*   更新 anaconda

```
$ conda update conda
$ python3 # check version updated
Python 3.7.2 (default, Dec 29 2018, 00:00:04)
[Clang 4.0.1 (tags/RELEASE_401/final)] :: Anaconda, Inc. on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

* * *

## 将库添加到水蟒中

1.  ## 搜索要安装的库。

    T2`$ anaconda search -t conda [library_name]`

2.  ## 获取安装命令。

    T2`$ anaconda show [Package the name you searched]`

3.  ## 输入刚刚得到的安装命令。

    T2`$ conda install --channel [path] [package name]`

4.  ## 出错了就卸载。

    T2`$ conda uninstall [package name]`

*   例如，安装 better_exceptions

```
$ anaconda search -t conda better_exceptions
Using Anaconda API: https://api.anaconda.org
Packages:
     Name                      |  Version | Package Types   | Platforms       | Builds
     ------------------------- |   ------ | --------------- | --------------- | ----------
     brianjmcguirk/better_exceptions_fork | 0.2.1.post6 | conda           | osx-64          | py37_0
                                          : Pretty and helpful exceptions, automatically
     conda-forge/better_exceptions_fork | 0.2.1.post6 | conda           | noarch          | py_0
                                          : Pretty and helpful exceptions, automatically
     jiayi_anaconda/better_exceptions |    0.2.1 | conda           | linux-64        | py36_0
                                          : Pretty and helpful exceptions, automatically
Found 3 packages

Run 'anaconda show <USER/PACKAGE>' to get installation details
$ anaconda show conda-forge/better_exceptions_fork
Using Anaconda API: https://api.anaconda.org
Name:    better_exceptions_fork
Summary: Pretty and helpful exceptions, automatically
Access:  public
Package Types:  conda
Versions:
   + 0.2.1.post6

To install this package with conda run:
     conda install --channel https://conda.anaconda.org/conda-forge better_exceptions_fork
$ conda install --channel https://conda.anaconda.org/conda-forge better_exceptions_fork
Collecting package metadata: done Solving environment: done

## Package Plan ##

  environment location: /Users/user/.pyenv/versions/anaconda3-5.3.1

  added / updated specs:
    - better_exceptions_fork
...
$ python3
Python 3.7.2 (default, Dec 29 2018, 00:00:04)
[Clang 4.0.1 (tags/RELEASE_401/final)] :: Anaconda, Inc. on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import better_exceptions
>>>
$ # conda uninstall better_exceptions 
```

* * *

# 烦恼

*   我不能输入 python2 命令。

```
$ python2
zsh: command not found: python2 
```

*   python 的路径可能会被更改。

```
$ ls /usr/bin/python
/usr/bin/python*
$ /usr/bin/python
Python 2.7.10 (default, Aug 17 2018, 19:45:58)
[GCC 4.2.1 Compatible Apple LLVM 10.0.0 (clang-1000.0.42)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

* * *