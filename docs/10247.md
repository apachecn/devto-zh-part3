# 用 pyenv 从源代码(debian / MX Linux)编译并安装 vim 8.1

> 原文：<https://dev.to/karlredman/compile-and-install-vim-v81-from-source-with-pyenv-5cjc>

*   这篇文章是为高级用户准备的——但也是为了帮助那些想运行尖端 vim 的人。

*   Vim 8.1 提供了`:terminal`。这是**大新闻**，并开放了一些非常酷的插件，如[为(neo)vim](https://github.com/iamcco/markdown-preview.nvim) 的 markdown 预览插件。此外，如果你想让[de complete . nvim](https://github.com/Shougo/deoplete.nvim)正常工作，你需要编译 vim 来对抗 python3。我发现让这些插件工作的最单一/主要的用户方式是从源代码编译 Vim。这是我觉得对单一系统来说最不碍事的方法。

*   这个 HowTo 将把 vim v8.1 添加到您的系统中的'/usr/local '下，并将其设置为缺省值(使用 python 3.7.2 和其他常用的必需品——根据需要进行调整)

*   这个方法尽可能地遵循“不要破坏 debian”的原则。

*   免责声明:！！在虚拟机中测试，否则风险自负！！！

*   测试对象:

    *   MX Linux v18.1
    *   Debian Stretch v9.8
*   参考资料:

    *   [从源代码 Valloric/YouCompleteMe Wiki 构建 Vim](https://github.com/Valloric/YouCompleteMe/wiki/Building-Vim-from-source)
    *   从源代码构建 Vim 8。
    *   [Home pyenv/pyenv Wiki GitHub](https://github.com/pyenv/pyenv/wiki#how-to-build-cpython-with---enable-shared)
    *   [如何用 pyenv (Python 版本 3.4.2)安装 ipython qtconsole 堆栈溢出](https://stackoverflow.com/questions/28165637/how-to-install-ipython-qtconsole-with-pyenv-python-version-3-4-2)
    *   [c -在非标准路径配置中编译具有静态链接 python 支持的 vim 堆栈溢出](https://stackoverflow.com/questions/40311073/compiling-vim-with-statically-linked-python-support-in-a-non-standard-path-confi)

### 安装并设置 pyenv

`pyenv`提供用户级的本地化 python 版本安装。

*   参考: [pyenv/pyenv-installer:该工具用于安装`pyenv`和好友。](https://github.com/pyenv/pyenv-installer)

```
curl https://pyenv.run | bash 
```

*   config:添加到您的`~/.bashrc`文件并重新登录(终端或桌面环境)

```
# Load pyenv automatically by adding
# the following to ~/.bashrc:

export PATH="/home/karl/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)" 
```

*   安装 python 版本

```
# list available versions
pyenv install -l |less
# insta a version
pyenv install 3.7.2 
```

*   为全局设置 python 版本

```
pyenv global 3.7.2 
```

### vim 编译安装脚本

```
#! /usr/bin/env bash

# Install vim from source on a debian based system where pyenv is used at thhe user level.
## The python version must already be installed via pyenv
## `pyenv install 3.7.2`
## It is not necessary remove debian installed vim packages

# fail on any error (fix things as needed)
set -e

# Convenience variables
FULL_VERSION="3.7.2"
MM_VERSION="3.7"
VIM_CONENSED_VER="81"
VIM_TAG="v8.1"
BUILT_BY="Karl N. Redman <karl.redman@gmail.com>"

# Install dependencies (note no python-dev here)
sudo apt install -y libncurses5-dev libgnome2-dev libgnomeui-dev \
libgtk2.0-dev libatk1.0-dev libbonoboui2-dev \
libcairo2-dev libx11-dev libxpm-dev libxt-dev \
ruby-dev lua5.1 liblua5.1-dev libperl-dev git

# Checkout vim
git clone https://github.com/vim/vim.git
cd vim

# make sure the repo is clean
make distclean

# (optional) Checkout the specific desired version
## umcomment to use specific tagged version
# git pull --tags
# git checkout tags/${VIM_TAG}

# Run `Configure`
./configure --with-features=huge \
            --enable-multibyte \
            --enable-rubyinterp=yes \
            --with-x \
            --enable-perlinterp=yes \
            --enable-luainterp=yes \
            --enable-gui=gtk2 \
            --enable-cscope \
            --prefix=/usr/local \
            --with-compiledby="${BUILT_BY}" \
            --enable-python3interp=yes \
            --with-python3-config-dir=$HOME/.pyenv/versions/${FULL_VERSION}/lib/python${MM_VERSION}/config-${MM_VERSION}m-x86_64-linux-gnu \
            --includedir=$HOME/.pyenv/versions/${FULL_VERSION}/include/

# Make and set the runtime directory (non default to avoid conflicts)
make VIMRUNTIMEDIR=/usr/local/share/vim/vim${VIM_CONENSED_VER}

## (optional) uncomment to remove current vim if desired
# sudo apt remove -y vim vim-runtime gvim vim-tiny vim-common vim-gui-common vim-nox

# install (to usr local)
sudo make install

# update alternatives to set our new build as default
sudo update-alternatives --install /usr/bin/editor editor /usr/local/bin/vim 1
sudo update-alternatives --set editor /usr/local/bin/vim
sudo update-alternatives --install /usr/bin/vi vi /usr/local/bin/vim 1
sudo update-alternatives --set vi /usr/local/bin/vim

echo "done." 
```

## 就是这样！

*   重新登录(如有必要)和`vim --version`应该报告`v8.1`！现在你可以试用`:terminal`来确保它的工作...并利用即将推出的新插件！