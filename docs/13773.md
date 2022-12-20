# 幕后的一瞥——我如何写博客

> 原文：<https://dev.to/shindakun/a-look-behind-the-curtain---how-i-blog-6b0>

# 写博客

我这个星期一直很忙，所以我还没有一个标准的帖子准备好。相反，我想我应该写一点关于我目前的设置和我的张贴“工作流程”。

* * *

## 机器

让我们先把无聊的事情处理掉。我在 Mac Book Air 上写了大部分代码和附带的帖子。

```
➜  ~ system_profiler SPSoftwareDataType SPHardwareDataType
Software:

    System Software Overview:

      System Version: macOS 10.14.2 (18C54)
      Kernel Version: Darwin 18.2.0
      Boot Volume: Macintosh HD
      Boot Mode: Normal
      Computer Name: Steve’s MacBook Air
      User Name: Steve (steve)
      Secure Virtual Memory: Enabled
      System Integrity Protection: Enabled
      Time since boot: 18 days 20:31

Hardware:

    Hardware Overview:

      Model Name: MacBook Air
      Model Identifier: MacBookAir6,2
      Processor Name: Intel Core i7
      Processor Speed: 1.7 GHz
      Number of Processors: 1
      Total Number of Cores: 2
      L2 Cache (per Core): 256 KB
      L3 Cache: 4 MB
      Memory: 8 GB
      Boot ROM Version: 110.0.0.0.0
      SMC Version (system): 2.13f15
      Serial Number (system): XXXXXXXXXXXX
      Hardware UUID: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
```

Enter fullscreen mode Exit fullscreen mode

## 环境

我用 [iTerm2](https://www.iterm2.com/) 搭配 ZSH[哦我的 ZSH](https://github.com/robbyrussell/oh-my-zsh) 。虽然它不是太定制化。我只是坚持默认的主题和一些有用的插件。

```
ZSH_THEME="robbyrussell"

plugins=(
  git
  colored-man
  colorize
  github
  vagrant
  virtualenv
  pip
  python
  osx
  vscode
  fzf
  golang
  httpie
  zsh-syntax-highlighting
) 
```

Enter fullscreen mode Exit fullscreen mode

iTerm 2 正在使用日晒黑暗和[操作符 Mono](https://www.typography.com/fonts/operator/styles/) 。 [VSCode](https://code.visualstudio.com/) ，我目前的编辑器选择，也是使用曝光黑暗和运营商单声道。我也使用 Vim 进行快速编辑(没错，它也使用了[日晒深色](https://github.com/altercation/vim-colors-solarized))。

我没有一个完全定制的网络文件回购，因为我没有在这个方向上做太多。不过，我确实使用了一些" [Mathias 的点文件](https://github.com/mathiasbynens/dotfiles/blob/master/.macos)"合理的 Mac 默认设置。也许我会花一点时间去挖掘，看看是否有值得分享的东西。

[![vscode](img/59667afe3c187a0072421c89b85b1d36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_FYEoVfO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oedn01yx5lullu11nexi.png)

在桌面上，我有一个文件夹巧妙地取名为`writerings`。这个文件夹是 GitLab 上的一个私人回购，所以当我心情不好的时候，如果我碰巧坐在我的 Windows PC 上，我可以访问这些文件。基本上，每个帖子只是一个降价文件，我有两个目录- `non-public`和`public`。非公开持有任何飞行中的想法或其他尚未完成的职位。

## 博客流量

最重要的事情是音乐！我目前正在通过 Monotrail 的[进行“实时”日常堵塞。#48 可能是目前为止我最喜欢的。](https://www.youtube.com/channel/UCjEIsPm7n8mm8Q10rMhg76g/about)

[https://www.youtube.com/embed/rljmxq2OLkE](https://www.youtube.com/embed/rljmxq2OLkE)

我试着边学边做，所以我试着做一些小项目。我的时间比我希望的要少一些，但是你要做什么呢？我将要发布的代码位于写作目录之外的一个单独的文件夹中。这个文件夹也是受版本控制的，保存在一个私有的 repo 中。一旦我心中有了想法，我会从最基本的代码开始。这成为我第一篇文章的基础，也是我尝试学习围棋系列文章的基础。

然后我将原始代码粘贴到一个包含我的 [markdown 帖子模板](https://gist.github.com/shindakun/f5fe5160e59b32cf56aefbbc9bf1b45e)的新文件中。从那时起，我一直在尝试一种自上而下的风格，基本上是对代码进行评论，指出什么发生了变化，我是否真的学到了什么，或者我们将来可能会期望什么。我通常会试着为这篇文章打下一个良好的基础，然后回去添加链接。除了标题，我通常不会添加太多图片，主要是因为我没有太多的空闲时间。

到目前为止，所有这些都完全发生在 [dev.to](https://dev.to/shindakun) 之外。一旦我认为一切都处于良好状态，我会将 markdown 复制并粘贴到 v1 dev.to 编辑器中，并保存帖子的未发布版本。然后是时候给它几个重新阅读。我无法告诉你我有多频繁地捕捉到一些小事情，打字错误，或者更大的事情，比如我刚刚进入的未完成的想法...

## 迭代

一旦一个帖子被上传，所有的东西都会被存入 GitLab，迭代过程开始。从我们上一次的地方开始，我首先决定是否有一个特性或某些东西明显需要修复。我的目标是将这个项目向前推进一点，足以让我有另一个帖子，但不会太远，以至于我跳过了前面。我很幸运地收到了一些非常好的反馈，所以我试着将它们结合到修改后的代码中，并在新项目中牢记在心。

## 展望未来

我正试图计划在我们“完成”静态站点生成器之后，我们可能会进入什么。所以我在考虑加入一个真正的纸笔记本和笔，这样我就可以在写代码和帖子时记下想法，也可以记下任何主题的想法。

## 下次

好了，快速浏览一下我是如何写文章的。我们将在下周的某个时候回到我们的静态站点生成器！

* * *

| 喜欢这篇文章吗？ |
| --- |
| 给我买杯咖啡怎么样？ |

* * *