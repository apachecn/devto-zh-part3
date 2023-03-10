# 用 Tmux 构建自定义 IDE

> 原文：<https://dev.to/biros/building-a-custom-ide-with-tmux-2aeg>

## 嗨！

今天，我想分享一个我已经使用了几个月的工具，它在我的日常工作中给了我很大的帮助: [tmux](https://github.com/tmux/tmux/wiki) 。

作为一名软件工匠和 CLI 爱好者，我一直在寻找尽可能高效的最佳工具。

让我向你展示我如何使用 tmux 作为我称之为 T2 的自定义 IDE T3 的基础。

## 为什么 Tmux？

Tmux 是一个很棒的终端复用器，带有一些插件。

您可能想知道为什么需要使用这样的东西。许多终端模拟器自带了拆分术语的内置功能。事实上， *tmux* 在分割和调整窗口大小时要强大得多。
另一个原因是 *tmux* 可以处理任何术语。因此，如果您更改术语，您不需要重新配置所有预设。Tmux 会帮你做到。
你也可以使用 *tmux* 而不用 X，在一个简单的`tty`中。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ tmux ](https://github.com/tmux) / [ tmux](https://github.com/tmux/tmux)

### tmux 源代码

<article class="markdown-body entry-content container-lg" itemprop="text">

# 欢迎来到 tmux！

tmux 是一个终端多路复用器:它允许从一个屏幕上创建、访问和控制多个终端。tmux 可以从屏幕上分离，并继续在后台运行，然后再重新连接。

这个版本可以在 OpenBSD、FreeBSD、NetBSD、Linux、OS X 和 Solaris 上运行。

## 属国

tmux 依赖于 [libevent](https://libevent.org) 2.x，可从[本页面](https://github.com/libevent/libevent/releases/latest)获得。

这也取决于 [ncurses](https://www.gnu.org/software/ncurses/) ，可从[本页](https://invisible-mirror.net/archives/ncurses/)获得。

## 装置

### 从释放 tarball

要从发行版 tarball 构建和安装 tmux，请使用:

```
./configure && make
sudo make install
```

如果安装了 utmp(5 ), tmux 可以使用 utempter 库来更新 utmp(5)——使用`--enable-utempter`运行 configure 来启用它。

### 从版本控制

从版本控制中获取并构建最新版本——注意，这需要`autoconf`、`automake`和`pkg-config`:

```
git clone https://github.com/tmux/tmux.git
cd tmux
sh autogen.sh
./configure && make
```

## 贡献的

Bug…

</article>

[View on GitHub](https://github.com/tmux/tmux)

### 安装它

通过你喜欢的包管理器安装 *tmux* ，或者从来源:
编译

```
$ git clone https://github.com/tmux/tmux.git
$ cd tmux
$ sh autogen.sh
$ ./configure && make 
```

### 配置一下

*Tmux* 一开始可能看起来有点生涩，很难处理。幸运的是，它有一个很好的社区来帮助它的建立。在尝试了许多配置后，我最终选择了 [gpakosz](https://github.com/gpakosz/.tmux) 的配置:

[![tmux](img/e3de386c1617795c77ecbad2fb32fbe7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NjRnHGGD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cloud.githubusercontent.com/assets/553208/19740585/85596a5a-9bbf-11e6-8aa1-7c8d9829c008.gif)

它带有绑定预置和一个很棒的[电力线](https://github.com/powerline/powerline)状态栏外观。

只需浏览一下 [README](https://github.com/gpakosz/.tmux/blob/master/README.md) 即可根据您的需求进行定制！

就我个人而言，我发现在我的状态栏中有正常运行时间是没有用的，所以我把它从配置中删除了。我还添加了一些插件:

*   [tmux 插件/TPM](https://github.com/tmux-plugins/tpm)*tmux 插件管理器*
*   [tmux 插件/tmux 敏感](https://github.com/tmux-plugins/tmux-sensible) *基本设置*
*   [tmux-plugins/tmux-yank](https://github.com/tmux-plugins/tmux-yank)允许复制到系统剪贴板
*   [tmux-plugins/tmux-open](https://github.com/tmux-plugins/tmux-open)*快速打开高亮文件或网址的按键绑定*
*   [chriszarate/tmux-tasks](https://github.com/chriszarate/tmux-tasks)*在状态栏显示(紧急)任务的计数。需要[任务战士](https://github.com/GothenburgBitFactory/taskwarrior)。*

### 使用它

您必须将 *tmux* 设置为您首选终端仿真器中的默认 shell。一旦完成， *tmux* 将在你每次开启新学期时启动。

基本上，当 *tmux* 启动时，它会创建一个空会话，其中包含一个空白窗格和一个状态栏。状态栏分为三部分:左、中、右。如果像我一样，您选择使用上面的配置，左边部分将显示**会话**的名称(如果名称未设置，则显示索引)和正常运行时间。右边部分将显示电池、日期、用户名和主机名。在中间，您将看到**窗口**的标题(或者在聚焦窗格中运行的进程的名称，如果该名称未设置的话)。

基本的是**会话**、**窗口**和**窗格**。每当您打开一个术语(或术语中的一个新标签)时， *tmux* 就会启动一个新会话。新会话包含一个窗口。一个新窗口包含您的`$HOME`目录中的一个窗格。

在 *tmux* 中的绑定是必不可少的:你可以通过使用前缀`Ctrl-a`或`Ctrl-b`来访问它们。

以下是我使用的主要绑定:

*   `<prefix> Ctrl-c`创建新会话
*   `<prefix> c`创建新窗口
*   `<prefix> %`垂直分割当前窗格
*   `<prefix> "`水平分割当前窗格

现在，如果你对绑定感到不舒服， *tmux* 有一个很棒的鼠标模式，允许你选择、切换和调整窗格大小。

### 充分利用

tmux 的杀手锏是**会话**。想象一下，你想建立一个有 3 个窗格的窗口来编辑文件，玩 git 并运行你的测试。只需创建一个新的会话，分割窗格，并根据您的需要调整它们的大小。

*很棒？没那么多。*

由于没有保存会话的内置机制，我使用了 [tmuxp](https://tmuxp.git-pull.com/en/latest/) 工具。这是 *tmux* 必备的会话管理器。

通过您的发行版管理器安装它，或者:

```
$ pip install --user tmuxp 
```

简而言之: *tmuxp* 允许您轻松地从`yaml` / `json`文件创建会话，并在需要时加载它们。它不仅仅是创建窗口&窗格布局:你还可以在每个窗格中运行命令。所以你可以在主窗格中运行`vim`，在另一个窗格中运行`cd`到你的项目目录来玩`git`，在第三个窗格中运行一个文件监视器来触发你的测试:

```
# my-project.yaml
session_name: my project
windows:
- window_name: my custom IDE
  layout: main-vertical
  shell_command_before:
    - pj my-project
  panes:
    - vim
    - git status
    - phpunit-watcher watch 
```

然后只需运行`tmuxp load -y my-project.yaml`。

如果你像我一样，需要建立一个完整的环境来开发你的应用，要知道这是没有限制的。我在当前项目中使用的会话有许多窗口:

*   1 个 **IDE** 窗口:3 个带`vim`的窗格，一个玩`git`的术语和一个运行测试的术语，
*   9 个窗口，每个窗口对应一个我需要用 [jq](https://stedolan.github.io/jq/) 或 [lnav](http://lnav.org/) 进行 grep 的日志文件，
*   为我的前线资产服务的窗口，
*   运行我的存根的窗口(通常是 SpringBoot 应用程序)。

* * *

**就是这样！**

我希望你通过阅读这篇文章学到了一些东西，并且你会看一看 *tmux* 。我不是这方面的专家。我必须说我没有使用 10%的特性，但是这个工具已经成为我的 dev env 的重要部分。我想这对你也一样。

感谢阅读。

再见。