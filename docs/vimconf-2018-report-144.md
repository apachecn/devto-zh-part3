# VimConf 2018 年报告

> 原文：<https://dev.to/sasurau4/vimconf-2018-report-144>

## 什么是 VimConf？

VimConf 是在日本召开的 Vim 国际会议。本次会议由 VimConf Jumbikai 组织，主要由 vim-jp 成员组成。
我觉得 VimConf 是全世界唯一一个关于文字编辑的会议。

VimConf 2018 于 11 月 24 日在日本举行。

这次会议是 VimConf 的第六次，这次会议对组织者来说是非常特别和难忘的。因为 Vim 的作者布莱姆·米勒先生要来日本参加会议！

组织者有一个梦想，他们在 2013 年举办第一届 VimConf 时邀请他参加会议。有人说“开个玩笑！”当组织者告诉他们。
但是，梦想已经实现了六年。

我想引用一下 [VimConf 2018 网站](https://vimconf.org/2018/)的句子。这是对大会的最好解释。

> VimConf 是一个向所有人分享你对 Vim 的热爱的地方

## 目录

我解释了每个主题演讲，会议和照明报告的摘要。然后写下我的观感。如果您想了解更多内容，请观看 youtube 上的电影或专题讲座的幻灯片。

### 主题演讲

#### 下一个特征是什么？

*   主持人:松本康弘又名 mattn
*   幻灯片:[主题演讲-下一个功能是什么？](https://docs.google.com/presentation/d/e/2PACX-1vTj2uCbuCffhaLVBZE6biI538GG6jMi1INbku-T9q5hu5W0zGLwuibN3m5xbEuqdUFTDnhdhtikPyTi/pub?start=false&loop=false&delayms=3000&slide=id.p)
*   视频:[https://www.youtube.com/embed/o3NygmN5lHM](https://www.youtube.com/embed/o3NygmN5lHM)

这个主题告诉我们两件事。一个是为什么维姆 jp 开始和他们在做什么。另一个是 mattn 的 3 个新功能提案！

[vim-jp](https://vim-jp.org/) 在日本 vim 用户中非常有名。当我想了解一些关于 Vim 的难点时，我通常会参考该网站。vim-jp 对 vim 的发展和讨论似乎很积极。
让我印象深刻的是，马特恩说“维姆-jp 从一开始就只做了两件事。向 vim 开发小组反馈错误和补丁，并编写补丁。”
今天，vim-jp 是理想的托管 OSS 社区之一。但是他们做的很简单。

我也很惊讶 Vim 在演示中变成了 Web 服务器。Vim 发展成为一个网络应用平台。
如果您想知道演示会上发生了什么，请观看视频！

#### Vim:从 hjkl 到一个插件平台

*   推荐者:布莱姆·米勒
*   幻灯片: [Vim:从 hjkl 到插件平台](https://vimconf.org/2018/slides/Vim_From-hjkl-to-a-platform-for-plugins.pdf)
*   视频:[https://www.youtube.com/embed/ES1L2SPgIDI](https://www.youtube.com/embed/ES1L2SPgIDI)

布拉姆先生是 Vim 的作者。他谈了三件事，Vim 的历史，插件性能调优和(插件支持投票)[[https://github.com/vim/vim/issues/3573](https://github.com/vim/vim/issues/3573)]【T2]他解释了 Vim 从开始到最近是如何发展的。我很惊讶 PDP-11 有多大。我以为那是冰箱而不是电脑。
此外，我对官方依赖管理器、编译 Vim 脚本等新功能感到兴奋。
我很高兴能直接听到 Bram 先生的主题演讲！

### 会话

#### 将插件迁移到标准功能

*   主持人:大苏
*   幻灯片:[将插件迁移到标准功能](https://speakerdeck.com/daisuzu/migrating-plugins-to-standard-features)
*   视频:[https://www.youtube.com/embed/1Lfx7vck7So](https://www.youtube.com/embed/1Lfx7vck7So)

你在 vim 上安装了多少插件？如果你数了一下，觉得可能有点多，这个环节很有帮助。
会话告诉我 vim 在默认情况下有怎样的各种功能。Vim 乍一看似乎很穷，但他们只是在掩饰。你使用 vim 越多，你就越能找到它们！我决定有一天检查我安装的插件并好好整理它们。

#### 模式

*   主持人:辰博宇治久
*   滑动:[模式](https://speakerdeck.com/ujihisa/vimconf-2018)
*   视频:[https://www.youtube.com/embed/3uUYf7eiI8Q](https://www.youtube.com/embed/3uUYf7eiI8Q)

本课程通过 GNU 项目调试器演示了 vim 的实现。没有完全理解会话内容，但是感受到了`:Termdebug`的便捷。
我对演示感到惊讶，因为我不认为 Vim 支持分步执行！

#### (普通)生活中的一天

*   主持人:大仓正文
*   幻灯片:[(普通)维默的一天](https://speakerdeck.com/okuramasafumi/a-day-in-the-life-of-the-ordinary-vimmer)
*   视频:[https://www.youtube.com/embed/H_gjNweFdpc](https://www.youtube.com/embed/H_gjNweFdpc)

这部分讲解了来自一个普通主持人一天的提示。演示没有成功，提示从明天开始非常实用！

#### 现代编辑器 PHP 独立开发环境

*   主持人:宇佐美健太
*   幻灯片:[PHP 的现代编辑器独立开发环境](https://niconare.nicovideo.jp/watch/kn3635)
*   视频:[https://www.youtube.com/embed/LA6ZH_GdzNI](https://www.youtube.com/embed/LA6ZH_GdzNI)

这个会议描述了当今以 PHP 为中心的开发环境。直到这次会议，我才知道 PHP 有很多功能，比如抽象类，特性接口等等。我认出 VimConf 是一个会议文本编辑器。如果 vim 是世界上唯一的文本编辑器，那么 vim 进化到现在了吗？我不这么认为。文本编辑器随着它们相互影响而发展。所以，我们是希望成为更有用、更方便的文本编辑器的朋友。

#### 有效的现代 Vim 脚本

*   主持人:艾丽素
*   幻灯片:[有效的现代 Vim 脚本](https://docs.google.com/presentation/d/e/2PACX-1vQKaWJY8w6QJpebvuzg334RfLDbQHv4-J_06yFxdTzLrrjhE_y5iuzA-JxCCuFdUAZQB2QQsidF_mys/pub?start=false&loop=false&delayms=3000&slide=id.p)
*   视频:[https://www.youtube.com/embed/J5BX1FXnKBw](https://www.youtube.com/embed/J5BX1FXnKBw)

如果你对创建 vim 插件感兴趣，我强烈推荐你看视频，看幻灯片。演讲者一步一步地解释了如何编写现代 vim 脚本。这很容易理解。
我想在演示的激励下陷入黑暗面。
黑暗面再见！

#### Oni-Neovim 的 GUI 化

*   推荐者:阿金
*   幻灯片:？
*   视频:[https://www.youtube.com/embed/MCBHz9o6gAg](https://www.youtube.com/embed/MCBHz9o6gAg)

你有没有想过 vim 会成为一个 IDE？我有。
如果想把 vim 做成 IDE，需要花很多时间，定制`.vimrc`。
管理`.vimrc`非常非常兴奋，但是很费时间。
于是，Oni 解决了这个问题。你可以像 Vim 一样使用 IDE 只需安装 Oni！
这非常简单，也非常棒。你为什么不试试奥尼？

#### Vim 移植到 WebAssembly

*   主持人:里斯德
*   幻灯片: [Vim 移植到 WebAssembly](https://speakerdeck.com/rhysd/vim-ported-to-webassembly-vimconf-2018)
*   视频:[https://www.youtube.com/embed/IczXWo2-MWI](https://www.youtube.com/embed/IczXWo2-MWI)

我们在任何地方都使用 Vim，例如 Mac、Linux 和 Windows 等..今天，新的浪潮正在到来，我们也在网络浏览器上使用 Vim！
试试【https://rhysd.github.io/vim.wasm/】T2T4】这个演示者是如何将 Vim 移植到 WebAssembly 的。WebAssembly 看起来很神奇。
很期待 wasm.vim 的未来作品！

### LTs

Lighitning Talks 也很激动。
谈话很短，但包含各种各样的东西！
查看幻灯片了解更多详情。

*   [vim-org mode 简介](https://takaxp.github.io/event/vimconf2018.html)
*   [Neovim 作为网络浏览器控制器](https://gitpitch.com/notomo/slides/vimconf-2018-lt#/)
*   [VimPhone2018](https://speakerdeck.com/rattcv/vimphone2018)
*   【Nvim 和 vim 在 5 分钟内的差异
*   [vim 历史库](https://www.slideshare.net/k-takata/the-vimhistory-repository-vimconf-2018-lightning-talk)

视频:[https://www.youtube.com/embed/3bJNLu0AkC4](https://www.youtube.com/embed/3bJNLu0AkC4)

### 后党

派对之后棒极了。参与者谈论了很多事情，比如 Emacs、编程语言、他们的工作，当然还有日语和英语的 Vim。食物和饮料都很美味。我对之后的派对很满意。

### 结论

VimConf 2018 很棒很牛逼。我们不知道下次运动会将在何时何地举行。我等不及下一个 VimConf 了！

感谢布拉姆先生，组织者，工作人员和所有相关人员！
感谢您的阅读。如果你发现这篇文章有什么错误，尽管指出来。

最后，我想说每一个。

“维明快乐！”

附:12 月 7 日更新，增加视频。可以在 [vim-jp youtube 频道](https://www.youtube.com/channel/UCSk7TnXVB0BMtY416dmFMuw/featured)查看所有关于 VimConf 2018 的视频！