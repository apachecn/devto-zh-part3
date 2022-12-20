# 我如何在数百台机器上管理我的点文件

> 原文：<https://dev.to/geewiz/how-i-manage-my-dotfiles-across-hundreds-of-machines-3b0g>

我在 Mac 工作站、云中的开发服务器和公司的 Linux 服务器基础设施上使用的几乎所有命令行工具都将其配置存储在所谓的“点文件”中，这些文件(甚至是整个目录)位于我的用户主目录中，通过在它们的名称前加一个句点(例如`.vimrc`)来隐藏它们。(就像这个例子一样，实际上有相当多的文件以`rc`结尾。这就是为什么它们有时也被称为“rc 文件”。)

在 dev.to 上，乔纳森·卡特问[“你如何在多台机器上管理和同步你的点文件？”](https://dev.to/lostintangent/how-do-you-manage-and-synchronize-your-dotfiles-across-multiple-machines-2n24)因为“多个”在我的例子中意味着“超过 500 台服务器”(我们为 Drupal 和 WordPress 运营一个[管理的高性能托管平台)，我想我应该在一篇简短的博文中回答他的问题。](https://www.freistilbox.com/)

## 点文件管理

我使用的大多数点文件来自我们共享的[团队点文件库](https://github.com/freistil/dotfiles)。我们的 SRE 之间的配置设置有很大的重叠，这使我们可以轻松地共享*并通过 [tmate](https://tmate.io/) 接管*终端会话，而不必纠结于单独的`tmux`或`vim`按键绑定。在终端会话中进行结对工作有一个巨大的优势，那就是它占用的带宽比通过[缩放](https://www.zoom.us)或[松弛](https://www.slack.com)进行屏幕共享少得多。

一些工具仍然允许一定程度的个性化。例如，shell 提示符细节可以有所不同，而不会引起混淆。您还可以拥有自己的 magic shell 和 git 别名。这就是为什么我把我的[个人点文件库](https://github.com/geewiz/dotfiles)放在团队一的上面。

另一个复杂因素是 Mac 上的 BSD 子系统有时表现得与 Linux 有点不同。这就是为什么，对于几个配置文件，我维护替代版本。它们存储在以`tag-`开头的子文件夹中。然后，dotfile 部署会根据主机操作系统安装正确的版本。

## dotfile 部署

那么，如果一组点文件来自两个重叠甚至冲突的 git 存储库，如何在数百台机器的主目录中维护它们呢？诀窍是克隆两个存储库，然后将配置文件符号链接到应用程序所期望的位置。保持这个过程简单无误的关键是 [rcm](https://github.com/thoughtbot/rcm) 。它可以处理多个点文件目录，每个目录都有自己的优先级。您还可以为它提供标记名，告诉它从哪里获取某些点文件。

我在设置新的开发机器时手动安装 rcm(一年只发生一两次)。在我们的服务器上，它通过我们的配置管理软件 Chef 自动安装。

下面是我用来部署我的点文件的脚本:

```
#!/bin/bash

if ! which rcup >/dev/null; then
  echo "Fatal: rcm is not installed on this machine."
  exit 1
fi

os_type=$(uname -s)

# Remove oh-my-zsh
rm -rf ~/.oh-my-zsh

if [[-d ~/.dotfiles]]; then
  cd ~/.dotfiles
  git pull
else
  git clone https://github.com/geewiz/dotfiles.git ~/.dotfiles
fi

if [[-d ~/.freistil-dotfiles]]; then
  cd ~/.freistil-dotfiles
  git pull
else
  git clone https://github.com/freistil/dotfiles.git ~/.freistil-dotfiles
fi

rcup -f -t $os_type -d ~/.dotfiles -d ~/.freistil-dotfiles 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，它克隆了点文件存储库(经过一点清理)，最后使用特定于操作系统的标记(“linux”或“darwin”)调用`rcup`。多亏了一个 a [`post-up`钩子](https://github.com/freistil/dotfiles/blob/master/hooks/post-up)，它甚至启动`vim`从 Github 更新插件。

您可以在我的个人存储库中找到部署脚本的当前版本 [`bin/dotfiles`](https://github.com/geewiz/dotfiles/blob/master/bin/dotfiles) 。因为它的目录被符号链接为`~/.bin`并被添加到我的 shell 搜索路径中，所以我可以随时执行`dotfiles`命令来更新我的本地配置。

至于在新机器上的初始部署，我简单地编写了脚本:

```
curl -L https://raw.githubusercontent.com/geewiz/dotfiles/master/bin/dotfiles | bash 
```

Enter fullscreen mode Exit fullscreen mode

这就是我如何管理和部署我的点文件，以在我所有的工作机器和我们的托管基础设施上有一个一致的设置。

如果你想看我如何很好地使用这些漂亮的点文件，请在 Twitch 上加入我的下一次[现场编码会议](https://fullstacksensei.com/livestream/)！