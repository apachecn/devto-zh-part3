# 增强命令行能力的 10 个工具

> 原文：<https://dev.to/_darrenburns/10-tools-to-power-up-your-command-line-4id4>

这篇文章是我近年来发现的一些最好的非标准命令行工具系列展示的第一篇。如果您曾经使用过命令行，您可能会发现该页面上至少有一项会让您的生活更轻松。

## `z`，跳来跳去

[![Jump Around](img/fbe156ab9b805cec7a12c6096db91b9f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SpTdcv7z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nmu47j5o0xjqh4b4uo52.gif)

现代网络浏览器中最好的和最普遍的省时功能之一是地址栏的模糊和智能搜索功能。想快速上 Twitter？在你的地址栏中输入“tw ”,然后回车就足够了。

相比之下，通过命令行导航文件系统的标准方式(使用`cd`)似乎有点过时了。令人欣慰的是，`z` ( [GitHub](https://github.com/rupa/z) )将这种浏览器风格的导航带到了命令行中。

经过一段时间的学习，`z`将允许你从任何地方跳到一个目录*，只使用目标目录名的一个子串。`z`将带你去的目录由你给它的字符串参数、你访问目录的*频率*以及你最近访问目录的*频率*决定。他们称之为“频率”。*

不仅提高了导航文件系统的速度，还减少了导航的认知负荷。使用`cd`，您需要准确地回忆目标目录在树中的位置，并计算出到达那里的路径。有了`z`，知道目录的名字就够了。

其他 shells(比如 fish 和 zsh)的`z`端口也很容易获得。`autojump` ( [GitHub](https://github.com/wting/autojump) )是一个类似的项目。

### 安装`z`

*   安装`bash`版本，在 macOS 上([自制](https://brew.sh) ): `brew install z`
*   在 macOS ( [费舍尔](https://github.com/jorgebucaran/fisher) ): `fisher add jethrokuan/z`上安装`fish`外壳[版本](https://github.com/jethrokuan/z)

## `fzf`，快速模糊查找器

安装`fzf` ( [GitHub](https://github.com/junegunn/fzf) )后，可以在任意时间点按`Ctrl + T`打开交互式模糊搜索界面，该界面会递归搜索当前目录内的文件。您可以输入一个搜索词，然后按向上/向下键在结果中移动。如果您按 enter 键，所选结果将被插入到您的终端中:

[![fzf](img/250c85ba022a36ac4400ef5d49d809a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GF4x8lKB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lasfcsdm1yswprwkuybg.gif)

在上面的例子中，我输入了`bat`(但是这可以是任何命令，比如`less`、`cd`等等。)，然后按下`Ctrl + T`。我输入`five`，按回车键，路径`src/five.rs`被插入到我的光标位置。这就节省了(粗略的)选择:键入`src`，按 tab 键，键入`fi`，按 tab 键，这不会扩展到很长或很难记住的路径。

### 安装`fzf`

*   在 macOS 上([自制](https://brew.sh) ): `brew install fzf`
*   `fish` : `fisher add jethrokuan/fzf`的绑定

## `bat`，以语法高亮方式查看文件

如果你想快速查看一个带有完整语法高亮的源文件，`bat` ( [GitHub](https://github.com/sharkdp/bat) )是你的好朋友。`bat`可作为`cat`的替代产品。

[![bat](img/8cec4e0ed9b8e55cf3bad48c62dc7ef7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XCHjdIRn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n28wzi9fctdbodglgfhx.gif)

如果输出足够大(如上例所示)，`bat`会将其输出通过管道传输到`less`，这意味着我们可以免费获得分页！

### 安装`bat`

*   在 macOS 上([自制](https://brew.sh) ): `brew install bat`

## `bench`，对你的代码进行基准测试

对于测试你的代码来说，( [GitHub](https://github.com/Gabriel439/bench) )是一个非常有用的工具。它是用 Haskell 写的，这使得它成为这个页面上最酷的东西。您可以将您可以从终端运行的任何命令传递给它(用引号括起来)，它将通过重复运行该命令来测量执行时间。完成后，它会向您的终端输出有用的统计数据。

[![bench](img/21359d9332ff8ca769ca3f5161949f47.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JD-SztpE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vd9iqp1012k6om51h99b.gif)

这是一种比内置的`time`命令更强大的测量代码执行时间的方法。

`hyperfine` ( [GitHub](https://github.com/sharkdp/hyperfine) )是用 Rust 编写的`bench`的替代品，你可能也会感兴趣。

### 安装`bench`

*   在 macOS 上使用[自制软件](https://brew.sh) : `brew install bench`

## `asciinema` & `svg-term`，用于将您的终端录制成 SVG 动画

我个人博客的[这个帖子版本上的终端剪辑是 SVG 动画(可惜 dev.to 不支持 SVG 上传)！你会注意到它们比这个页面上的 gif 要清晰得多。使用 SVG 而不是视频格式或 gif 有几个巨大的好处:](https://darrenburns.net/posts/tools)

*   无论变焦如何，都有完美的质量🔍
*   我们可以把它们像其他图片一样放在减价文件中😱
*   与视频格式和 gifs 相比，文件更小
*   SVG 动画比视频更酷🔥

为了记录终端，我使用 [`asciinema`](https://asciinema.org) 。用`asciinema rec`开始记录。当你完成后，按下`Ctrl+D`，你可以选择将录像保存到本地或者上传到[asciinema.org](https://asciinema.org)。

[![asciinema_example](img/0d9763add89223cd765395959ad7f9d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qtqLF4SS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1a83p0nleldkd6ug4uvy.gif)

如果你想使用`svg-term` ( [GitHub](https://github.com/marionebl/svg-term-cli) )从你的录音中生成一个 SVG 动画，并且你已经把你的录音上传到 asciinema，你必须通过访问结果链接来公开它。

要将记录转换成 SVG 动画，您可以提供角色的 ID(在将它公开后，可以在 asciinema 页面上找到——ID 在 URL 中)、一个输出文件和许多其他可选参数。例如，为了将在[https://asciinema.org/a/219486](https://asciinema.org/a/219486)的终端记录保存到上面例子中看到的 SVG 文件，我使用了:

```
svg-term --cast=219486 --out ~/somewhere/out.svg --padding 18 --height 8 --width 80 
```

或者，如果你不想把你的录音上传到 asciinema，你可以直接给`svg-term`提供一个本地演员表文件(感谢`svg-term`的作者 Mario Nebl 给我指出这一点):

```
asciinema rec cast.json
cat cast.json | svg-term-cli 
```

### 安装`asciinema` & `svg-term`

*   在 macOS 上安装`asciinema`:`brew install asciinema`
*   在 macOS 上安装`svg-term`:`npm install -g svg-term-cli`

## `wrk`，用于基准测试您的 HTTP APIs

这是一个方便的测试 API 性能的小工具。为了进行演示，我在本地机器的端口 8001 上运行了一个带有单个端点(`GET /hello`)的最小 Python HTTP API 服务器。我们可以使用`wrk`来检查`/hello`端点的执行情况(12 个线程，200 个连接，持续 5 秒):

[![wrk](img/27b6e0457538d0be1fa4af3c4e976022.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7rBidgiO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zwk2fopv1cgw27740j1a.gif)

您可以调整线程数、连接数和测试持续时间，以检查不同负载下的性能。它不能替代诸如 Locust 和 JMeter 之类的性能测试工具，但是它是轻量级的，可以满足很多情况。

不幸的是，`wrk`的命令行界面使得执行 POST 请求有些笨拙。如果您想这样做，您需要编写一个小的 Lua 脚本，并将其作为参数提供给命令(文档中有更多信息)。

### 安装`wrk`

*   在 macOS 上使用[自制软件](https://brew.sh) : `brew install wrk`

## `exa`，替代`ls`

[`exa`](https://the.exa.website) 是`ls`的现代替代品，具有颜色编码输出，看起来更容易，并且有更多的选项来控制输出的呈现方式。

[![Exa](img/9b4190fb00a286a0b318845434ca69c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JFWF-CAU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3eseuz5vx6fc7294tboj.gif)

它支持尊重你的*等功能。通过`--git-ignore`标志 gitignore* 文件，并用`-T`标志将
打印出一个树形结构的目录(见上图)。它甚至显示文件的 git 状态！

### 安装`exa`

*   在 macOS 上使用[自制软件](https://brew.sh) : `brew install exa`

## `fd`，用于查找文件&目录

如果您正在寻找一个文件或目录，您通常会使用`find`命令来执行基于正则表达式的搜索。`fd`([)GitHub](https://github.com/sharkdp/fd))是用 Rust 写的`find`的替代品，它通过使用合理的默认值提供了一个更方便的界面，而且启动更快！

[![fd](img/10b53d0140a9d24313aead8ebc532687.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bWXb7tiQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ojytfn8zszkjw51r8x9x.gif)

它会尊重你的*。gitignore* 文件，它支持并行命令执行，允许您对搜索返回的每个文件或目录执行一个终端命令(并行)。例如(从`fd`文档中)，查找所有。jpg 文件并将其转换为。png 文件并行使用 Unix 的`convert`命令，可以运行:

```
fd -e jpg -x convert {} {.}.png 
```

### 安装`fd`

*   在 macOS 上使用[自制软件](https://brew.sh) : `brew install fd`

## `rg` (ripgrep)，用于在文件中查找字符串

`rg` ( [GitHub](https://github.com/BurntSushi/ripgrep) )是`grep`的一个(*快得多*)的替代品。

[![rg](img/3ef4e173b1e3dfcd044e15d9168e0e75.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nDsogg-Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cmpd8gleajtbj2ycz96n.gif)

`rg`是用 Rust 编写的，它支持 VS 代码文本编辑器中的搜索功能。在基准测试中，它始终优于类似的工具。

### 安装 ripgrep

*   在 macOS 上使用[自制软件](https://brew.sh) : `brew install ripgrep`

## 结论

希望你能在这篇文章中找到有用的东西！如果你对更多类似的内容感兴趣，请在 [Twitter](https://twitter.com/_darrenburns) 和 [DEV](https://dev.to/_darrenburns) 上关注我。

这篇文章最初发表在我的博客上。