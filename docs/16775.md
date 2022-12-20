# 启动您的命令行，第 3 部分

> 原文：<https://dev.to/_darrenburns/power-up-your-command-line-part-3-4o53>

这是*启动你的命令行*系列的第三部分。在本文中，我将展示五个实用程序，它们使得使用命令行解决常见问题变得稍微容易一些。

这篇文章最初发表在我的博客上。

## `tig`，用于交互式浏览您的 git 回购

`tig` ( [GitHub](https://github.com/jonas/tig) )是一种使用交互式界面浏览 Git 库的好方法，无需离开命令行。

[![tig](img/908bd58cea87aa2e2ed592527af74d19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9iZ0KLlH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/begtel0euvo9zd0ppire.gif)

使用起来简单直观，并且有不同的视图，比如你的仓库、暂存区(可以让你快速改变)、日志等等。

感谢雷纳托苏埃罗([@雷纳托苏埃罗](https://twitter.com/renatosuero))在 DEV 上把我介绍给`tig`。

### 安装`tig`

*   在装有([自制软件](https://brew.sh))的 macOS 上:`brew install tig`

## (`fpp`)，用于快速选择文件

PathPicker ( [GitHub](https://github.com/facebook/PathPicker) )是脸书的一个库，用于在命令行上快速选择文件。下面的动画示例摘自 PathPicker 文档。

[![fpp](img/46335217b238f0751b55f3f067183156.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NG6D3NEB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/498ik89ukpwhiu2fxncn.gif)

从 [PathPicker 网站](http://facebook.github.io/PathPicker/):

> PathPicker 接受来自 git 命令、grep 结果、搜索的各种输入输出——几乎任何东西。解析输入后，PathPicker 为您提供了一个很好的 UI 来选择您感兴趣的文件。之后，您可以在您喜欢的编辑器中打开它们，或者执行任意命令。

感谢 Nikolay du Bina([@ Nikola yid](https://twitter.com/nikolayid))建议这个实用程序。

### 安装路径选择器

*   在装有([自制软件](https://brew.sh))的 macOS 上:`brew install fpp`

## `tldr`，获取如何使用 CLI 工具的实用示例

[`tldr`](https://tldr.sh) ( [GitHub](https://github.com/tldr-pages/tldr/) )让你快速访问实用实例(简体，" TL；dr“版本的手册页”)来了解如何使用命令行工具。

[![tldr](img/4ba7f0471f835b89aca43e07a7e9ce60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SAeqVvg5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ha3htknkv4qu7uenk2qn.gif)

这些例子由社区在`tldr` [GitHub 库](https://github.com/tldr-pages/tldr)中维护。

### 安装`tldr`

*   使用 npm(推荐):`npm install -g tldr`
*   在装有([自制软件](https://brew.sh))的 macOS 上:`brew install tldr`

## `gron`，用于探索 JSON

`gron` ( [GitHub](https://github.com/tomnomnom/gron) )将 JSON 文本转换成离散的赋值，以便更容易找到您需要的内容。我特别喜欢将它与`fzf`(在本系列前面提到过)结合使用来交互式地探索 API:

[![gron-with-fzf](img/8b44d1f45aef6c1de91330d34876705a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AIyC9I69--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l01ti86m1p15homi04na.gif)

你也可以使用`gron`来帮助你转换 JSON 对象([例子](https://github.com/tomnomnom/gron/blob/master/ADVANCED.mkd))。不过这不是`gron`的主要用例，使用像 [`jq`](https://stedolan.github.io/jq/) 这样的专用工具可能会更好。

### 安装`gron`

*   在装有([自制软件](https://brew.sh))的 macOS 上:`brew install gron`

## thefuck，用于快速纠正命令行输入错误

如果你输错了一个命令，输入`fuck` ( [GitHub](https://github.com/nvbn/thefuck) )，你会得到一个可供选择的修正列表。

[![thefuck](img/178c9f7cbed4bca9b67312ab6b196b52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FAJWdHLj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7f5s5xt1whw5me9ogd9k.gif)

不幸的是，这个命令的名字有点像 NSFW，所以你可以用别的名字来代替它。

### 安装推杆

*   在装有([自制软件](https://brew.sh))的 macOS 上:`brew install thefuck`

## 奖励:解释 shell，用于解释 shell 命令

如果你已经有了一个复杂的 shell 命令，并且想在不浏览`man`或`tldr`页面的情况下理解它的功能，你可以使用[解释 shell](https://explainshell.com) :

[![explain_shell](img/9929f3edfce957cd610d5516f557559e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--669lTixL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3whyhkn8c3bgfwym8mn2.png)

## 结论

希望你在这个页面上找到了你感兴趣的东西！如果你对更多类似的内容感兴趣，请在 [Twitter](https://twitter.com/_darrenburns) 和 [DEV](https://dev.to/_darrenburns) 上关注我。