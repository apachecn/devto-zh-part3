# 设置新的笔记本电脑并升级到 Homebrew 2

> 原文：<https://dev.to/coreyja/setting-up-new-laptop-and-upgrading-to-homebrew-2-1fa0>

*本帖最初发表于[coreyja.com](https://coreyja.com/upgrading-to-brew-2/)T3】*

这篇博客文章将会是我的新笔记本电脑的安装过程，这导致了我的网络文件回购的这个 PR。我需要更新一些东西才能让笔记本电脑运行起来。最大的一次是从家酿 1.x 升级到家酿 2.0

## 设置新机器

总的来说，我真的很高兴移植到一台新机器是如此的容易，这在很大程度上要感谢家酿啤酒和我的回购。有了这两个工具，我就可以安装我需要的所有东西，而且我的大部分配置已经可以工作了，不需要任何额外的工作。

我没有记录我运行的确切命令，但它是这样的

```
cd ~
git init .
git add remote origin https://github.com/coreyja/dotfiles.git
git fetch origin
git checkout --track origin/master
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew bundle 
```

这是在主目录中检查我的`dotfiles` repo。有些人不是这样做他们的点文件的，他们把版本控制文件存储在其他地方，然后复制或符号链接到他们的主目录。相反，我将实际的主目录置于版本控制之下，但是将`.gitignore`设置为白名单，在这里我必须特别添加要跟踪的文件。这对我真的很好，因为我不必维护两个不同的目录。我在这里写了更多关于[的设置。](//blog/2018/01/06/dotfiles-december-2018.html)

我的第一次`brew bundle`没有成功，因为我需要做一些升级！这篇文章的其余部分将是关于我必须做出的改变，以便开始运行！总的来说，我觉得我只需要做一些小的改变，就可以很快地运行起来！

## 自制 2

今年 2 月，家酿社区[发布了家酿 2.0](https://brew.sh/2019/02/02/homebrew-2.0.0/) 🎉 🎉

首先，我要感谢整个家酿社区所做的一切工作！出 2.0 是一个牛逼的里程碑！

### 清除选项

Brew 2 中的一些变化影响了我的点文件和设置。

首先是删除了`Homebrew/homebrew-core`中所有公式的选项。这是默认的 tap，因此我使用的许多选项都不再受支持。有几个我正在使用的选项被我简单地删除了，我不认为我在积极地使用它们。这包括`imagemagick`的`--with-webp`选项和`wget`的`--with-iri`选项。

然而，有一类选择，我仍然想支持！这是我在一些 GNU utils 上使用的`--with-default-names`选项，包括`coreutils`、`grep`和`sed`。这个选项使得我可以使用这些实用程序的自制版本，而不用带`g`前缀。例如，我希望能够使用`sed`而不是`gsed`来使用 GNU `sed`工具。

然而，完成这一点相对来说并不痛苦！我通过在我的`.bash_profile`中添加下面的代码片段来做到这一点。这些直接来自每个 Brew 安装的`CAVEATS`部分

```
# Add GnuCoreUtils to the Path
export PATH="/usr/local/opt/coreutils/libexec/gnubin:$PATH"
export MANPATH="/usr/local/opt/coreutils/libexec/gnubin:$MANPATH"
# Add grep to the Path
export PATH="/usr/local/opt/grep/libexec/gnubin:$PATH"
export MANPATH="/usr/local/opt/grep/libexec/gnubin:$MANPATH"
# Add sed to the Path
export PATH="/usr/local/opt/gnu-sed/libexec/gnubin:$PATH"
export MANPATH="/usr/local/opt/coreutils/libexec/gnuman:$MANPATH" 
```

除了将这些实用程序添加到路径中之外，这实际上还做了一件事情。它还设置了`MANPATH`，这样您就可以执行`man sed`并获得 GNU `sed`工具的正确文档

## 其他 Brewfile 修改

我还对我的 Brewfile 做了一些与升级无关的额外更改。

我移除了几个在安装时有问题并且我不再使用的东西，包括`svg-term`和`heroku`。

我还删除了`gpg-agent`，因为它现在与`gpg`捆绑在一起。

我添加了`go`和`rustup`，因为我的设置依赖于一些 go 和 Rust 实用程序。我以前在我的机器上有这些，但是没有把它们添加到我的点文件中。我还意识到我想要的一些 GUI 工具可以通过`cask`安装，所以我也添加了一些

## 增加了一个基础`.ruby-version`文件

事实上，当我安装新的笔记本电脑时，我很惊讶我没有这样做。但是我没有将`.ruby-version`文件保存在我的主目录中的`git`下，所以我通过添加最新的当前可用版本的 ruby 作为我的默认`.ruby-version`来改变它。我仍然会有依赖于不同版本的 Ruby 的项目，并且有他们自己的`.ruby-version`文件。但是这会给我一个好的默认版本，在机器之间是一致的。