# 如何用 Selenium Webdriver 处理 JavaScript/前端错误

> 原文：<https://dev.to/kabisasoftware/how-to-handle-javascript-frontend-errors-with-selenium-webdriver-3f2h>

点文件是位于您的`$HOME`目录中的文本文件，有助于配置您的终端和程序。当您迁移到一台新计算机时，在 git 存储库中拥有点文件使得复制这些配置变得轻而易举。

有很多用各种语言编写的点文件管理器。基本上，这些点文件管理器在主目录中创建指向点文件的符号链接。

但是我们真的需要一个点文件“管理器”来符号链接一些文件吗？

## 迎接 GNU Stow🔨

GNU Stow 是一个 symlink farm 管理器，它使软件包出现在文件系统的不同位置。

我们可以用 [brew](https://brew.sh/) 安装软件:

```
$ brew install stow 
```

## 宾至如归🏠

让我们创建一个存储库`dotfiles`并创建我们的第一个链接到主目录的点文件。

```
$ mkdir ~/dotfiles
$ cd ~/dotfiles
$ git init .
$ echo "A simple dotfile configuration." > .mydotfile
$ git add .mydotfile
$ git commit -m "Create dotfiles repository" 
```

现在我们有一个存储库，你可以很容易地转移。接下来，我们需要将 mydotfile 符号链接到主目录。

```
$ cd ~
$ ln -s ~/dotfiles/mydotfile .mydotfile
$ ls -la | grep mydotfile
lrwxr-xr-x    1 e  ff      20B 29 May 11:11 .mydotfile -> dotfiles/.mydotfile 
```

现在我们已经将存储库中的点文件符号链接到我们的主目录。符号链接是对原始文件的引用，可以编辑和查看。但是，当您删除符号链接时，原始文件保持不变。

我们可以用 Stow 自动执行手动符号链接文件的过程。例如，让我们创建以下结构来设置 neovim 配置文件:

```
$ tree -a ~/dotfiles
.
└── neovim
        └── .config/nvim
        └── .init.vim
    └── .vimrc 
```

使用 Stow，只需运行一个命令就可以链接这些文件。

```
$ cd ~/dotfiles
$ stow neovim 
```

当我们查看我们的主目录时，我们会看到以下内容。

```
$ ls -la ~ | grep vimrc

lrwxr-xr-x     1 e  ff    22B May 29 11:12 .vimrc ->  dotfiles/neovim/.vimrc

$ ls -la ~/。5 月 29 日 11:36 init.vim - >../../dotfiles/neovim/。config/nvim/init.vim 
```

## 
  
让我们反思一下发生了什么📝

1.  我们用我们的`neovim`点文件目录运行 stow。

2.  Stow 查找带有`.init.vim`的`.vimrc`和子目录`.config/nvim/`。

3.  Stow 在当前位置的上一个目录中创建一个符号链接，并在主目录中为`~/.config/nvim/`和符号链接`.init.vim`创建结构

Stow 假设您希望将文件符号链接到执行 stow 命令的目录之上的一个目录。将 git 存储库放在`~/dotfiles`中会让 stow 在`$HOME`目录中为我们创建正确的符号链接。

如果您想删除符号链接，您可以键入`stow -D neovim`，这将解除文件的链接。Stow 会在您将要覆盖现有目录或文件时发出警告。

我正在使用所描述的技术来符号链接我自己的点文件。