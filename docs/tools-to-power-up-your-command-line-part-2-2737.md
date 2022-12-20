# 5 个增强命令行能力的工具(系列的第 2 部分)

> 原文：<https://dev.to/_darrenburns/tools-to-power-up-your-command-line-part-2-2737>

这是“*启动您的命令行*”系列的第二部分，它展示了优秀的非标准工具，这些工具将使使用命令行变得更加容易和愉快。

## `peco`，交互式过滤任何东西

将任何命令的输出通过管道传输到`peco` ( [GitHub](https://github.com/peco/peco) ，您将能够交互式地过滤它以找到您需要的信息。你可以把它想象成一种互动形式的`grep`，当你输入的时候，结果会实时更新，这是一个更具探索性的搜索过程。

[![peco-processes](img/88463a2f0ae28c126849c129b2556bb4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S9ZDMT_k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2dtk7o5v8xubnzffhok7.gif)

您可以使用箭头键在搜索结果中上下移动，并使用 enter 键选择一个结果。当您按 enter 键时，该命令会将结果输出到您的终端。

### 安装`peco`

*   在装有自制软件的 macOS 上:`brew install peco`

## `hexyl`，一个十六进制的浏览器

如果您想检查一个二进制文件，通常可以通过查看该文件的十六进制表示来完成。`hexyl` ( [GitHub](https://github.com/sharkdp/hexyl) )就是这样一个命令行工具。

[![hexyl](img/b0a2e0d888329834a52d844e7c412d89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X4pvExVH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x6r3lg9fsing46ol4fq8.gif)

该视图分为三列:

1.  一个*偏移量*列，它告诉你文件中有多少字节。
2.  一个*十六进制*列(它本身中间有一个分隔线——在示例 gif 中不可见)，包含文件的十六进制表示。
3.  一个*表示*列(也被一分为二，在示例 gif 中没有显示)，它包含文件的文本表示。

根据字节类型(空、ASCII、非 ASCII 等)对`hexyl`中的每个字节进行颜色编码。)这确实有助于提高可读性。

*额外提示*:如果查看二进制文件或大型文本文件，输出通常会对您的屏幕来说太大，因此您可以将来自`hexyl`的输出通过管道传输到`bat`或`less`中，让您能够通过它进行分页！如果您使用`less`，您需要使用`--raw-control-chars` / `-r`标志，以使输出正确着色:

[![hexyl-less](img/fae51716ee801c452eed528d764b5995.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1Y7uYnBj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k271fbip61yswdp1yg0w.gif)

`hexyl`是用 Rust 编写的，由[大卫·彼得](https://github.com/sharkdp)开发，他也写了`bat`、`fd`和`hyperfine`，所有这些我在这个系列的第一部分都提到过。

[![sharkdp image](img/49ed04b07194febb209f224b75c09339.png)](/sharkdp)

## [大卫·彼得](/sharkdp)

[![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)sharkdp](https://github.com/sharkdp)[![external link icon](img/7ad9ad23055d49c106b927d92662ca16.png)https://David-Peter . de](https://david-peter.de)

### 安装`hexyl`

在 macOS 上安装`hexyl`需要几个步骤:

1.  从 GitHub 下载二进制文件:`curl -L -o hexyl.tar.gz https://github.com/sharkdp/hexyl/releases/download/v0.3.0/hexyl-v0.3.0-x86_64-apple-darwin.tar.gz`
2.  提取 tarball: `tar -xf hexyl.tar.gz`
3.  将其添加到您的`PATH` : `mv hexyl-v0.3.0-x86_64-apple-darwin/hexyl /usr/local/bin`
4.  制作可执行文件:`chmod +x /usr/local/bin/hexyl`

## `pomo`，一个番茄计时器

番茄工作法是提高你工作效率的好方法。如果你以前没听说过，它是这样工作的:

1.  在 *25 分钟*里，你*工作*，不允许有任何分心。
2.  在 *5 分钟*里，你*放松*，做任何你想做的事情(只要不是工作)😁).
3.  重复上述步骤 4 次(你可以调整这个数字来适应自己)，然后休息 *15 分钟*

如果你遵循上面的计划，理论是你会在相当短的时间内完成相当多的工作。它可能不是对每个人都有效，但我个人可以保证它的有效性！

[![pomo](img/a268dd55493217b70942ee02af0f76c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eUsjP3jW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2qdj1c0yq9mww11soap5.gif)

GitHub 是一个简单的，可配置的 CLI 工具，帮助你通过番茄工作法管理你的时间。

### 安装`pomo`

安装这个程序需要遵循几个步骤(在 macOS 上):

1.  从 GitHub 下载二进制文件:`curl -L -o pomo https://github.com/kevinschoon/pomo/releases/download/0.6.0/pomo-0.6.0-darwin-amd64`
2.  授予权限:`chmod +x pomo`
3.  将它放在您的`PATH`上，这样您就可以在任何地方使用它:`mv pomo /usr/local/bin`
4.  初始化数据库:`pomo init`

## `ncdu`，分析并释放磁盘空间

如果你的电脑上有很多项目，并且最近没有清理你的磁盘，你几乎肯定会发现一个占用磁盘空间的流氓文件夹(我发现旧项目中的一些`node_modules`文件夹特别容易受到影响)。 [`ncdu`](https://dev.yorhel.nl/ncdu) 是我最喜欢的修复工具。事实上，在为下面的`ncdu`创建演示的过程中，我释放了超过 10GiB 的磁盘空间！

[![ncdu](img/867dec7cce084a0cef99a1597938cb36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_h8qC3mq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lkoffquem4zz1nqre9ez.gif)

要使用它，运行`ncdu`。它将扫描你运行它的目录下的所有子目录，所以如果你在你的主目录下运行它，它可能需要一些时间。

您可以使用交互式界面，使用箭头键浏览您的文件系统，或者使用`vim`中的导航键，如果您喜欢的话。

### 安装`ncdu`

*   在装有[自制软件](https://brew.sh) : `brew install ncdu`的 macOS 上

## HTTPie，是`curl`的现代替代品

[HTTPie](https://httpie.org) ( `http`)是`curl`的一个更简单(意味着我不必每次都谷歌它，不像用`curl`)，功能丰富且漂亮的替代品，它将让你从命令行调用 HTTP 上的 API。这是迄今为止我所评论过的最受欢迎的实用程序，并且拥有出色的[文档](https://httpie.org/doc)。

[![httpie](img/a76ea6489e49157e7619311d1765535c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yEIOdF7I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n5spphjp7tm5xqm5lwsw.gif)

来自`http`命令的输出是它真正区别于 cURL 的地方。JSON 响应非常漂亮地打印了语法高亮，使它们更具可读性。如果你更喜欢使用图形用户界面来与 API 交互，你可能会喜欢失眠症，邮递员或 Paw (Paw 需要许可证，并且仅适用于 macOS)。

### 安装 HTTPie

*   在装有自制软件的 macOS 上:`brew install httpie`

## 结论

感谢阅读！在本系列的下一篇文章中，我还想提到几个工具，但是如果你有任何建议，请在评论中告诉我。如果你对更多类似的内容感兴趣，请在 [Twitter](https://twitter.com/_darrenburns) 和 [DEV](https://dev.to/_darrenburns) 上关注我。

最初[发布在我的博客](https://darrenburns.net/posts/more-tools)上。