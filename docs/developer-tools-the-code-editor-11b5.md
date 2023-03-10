# 开发人员工具:代码编辑器

> 原文：<https://dev.to/wuz/developer-tools-the-code-editor-11b5>

欢迎回来，朋友们！

今天，我们将看看代码编辑器。这是软件工程师的饭碗。这就是你如何把你头脑中的想法输入电脑。

有大量的选择，每个人都有自己的看法，哪一个是最好的。Emacs 与 Vim 的争论将会持续到时间的尽头。然而，在一天结束的时候，你必须做出一个最适合你的决定。对我来说，它是 Vim 和 VSCode 的混合体。

也就是说，有几样东西是所有优秀的文字编辑都会有的。(我知道有些人会不同意，但无论如何:P)

> *注意:*不是每个编辑器都在每个操作系统上，所以一定要看看本系列的前一篇文章！

## 主题&语法高亮

[![](img/3cc7e53ebe6f84016ae863db7b32cd67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kLIHWD1r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dankneon.com/vscode/assets/vscode.png)

大多数现代的编辑器(和许多老的编辑器)有能力改变你正在看的东西的颜色。许多都有 UI 和语法主题，它们分别改变编辑器本身的颜色和代码的颜色。你使用的颜色绝对是个人喜好。有浅色主题和深色主题，自然色和霓虹。当选择一个主题时，记住这是你必须一天看几个小时的东西——你想要一些容易阅读的东西，这使得用你主要写的语言工作变得容易。

(*旁注:*如果你正在寻找一个新的主题，去看看 [Dank Neon](https://dankneon.com/) 。我们正在为它和它的开源而努力！)

## 扩展性

很有可能，你的编辑器中并不是所有的东西都是你想要的——一个好的编辑器会有让你改变默认设置的选项。许多都有可扩展的首选项、插件架构，有些，比如 Vim，甚至有自己的编程语言。

## 健康社区

有些人可能喜欢与他们自己构建的或者非常模糊的编辑器一起工作，但是对于我们大多数人来说，这些时间更适合用来学习和发展新的技能。拥有一个好的社区可以让你更容易解决任何问题。这也是参与社区和回馈社会的一个很好的方式——一旦你知道了答案，你就可以在下一次出现的时候帮助别人！

# 一个属于自己的编辑器

既然我们已经介绍了编辑器的一些基本特性，那么让我们来谈谈如何开始使用您的编辑器。首先，找一个你喜欢的。我会在这篇文章的最后放一个编辑器和插件的列表。如果你还没有喜欢的编辑器，向下滚动并比较一些选项！为了便于解释，我将使用 VS 代码来完成剩下的步骤。几乎每一个编辑都有自己的方式去做这些事情。如果您没有使用 VS 代码，您应该能够在标题中 Google 主题加上编辑器名称(即“安装 shell 命令 atom”)。我们走吧！

## 安装外壳命令

一旦你安装了 VS 代码，运行它！马上，您应该有大量的工具和服务可用，但我们想让这个编辑器成为我们自己的！首先要做的是安装 shell 命令——这让您可以运行`code ~/SomeFolder/SomeFile.js`在 VS 代码中打开文件。为此，切换到 VS 代码窗口并点击`(cmd/ctrl)+shift+P`。这将打开命令面板，在这里您可以运行各种命令和编辑器相关的功能。键入`shell`，选择*“安装‘代码’命令路径”*。现在关闭 VS 代码，切换到一个包含可以编辑的文件的文件夹。键入`code <file_name>`，其中`<file_name>`是您想要编辑的文件的名称。如果 VS 代码和那个文件一起弹出来，就是你做的！

## 安装主题

现在我们已经设置了 shell 命令，让我们安装一个主题。前往 [VSCodeThemes](https://vscodethemes.com/) ，浏览一下。我将安装 [Dank Neon](https://dankneon.com/vscode) (它支持一堆不同的编辑器！).回到 VS 代码，点击`(cmd/ctrl)+shift+X`打开扩展侧边栏面板。一旦打开，搜索您想要安装的软件包。

<center>![install button](img/c6cdf80d6e42d40de8b5095fcc5108b3.png)</center>

找到主题后，单击绿色的安装按钮。主题应该会自动更改为您的新主题！如果没有，调出命令调板，键入`Color Scheme`并选择*“首选项:颜色主题”*，然后选择你想要使用的主题。

## 安装语言/工具包

大多数编辑器都附带了对大多数语言的基本支持，但通常您会想要更多针对您的语言的包。再次打开扩展侧边栏面板，搜索你最常用的语言，比如`ruby`、`javascript`等等。对于工具，你可以很容易地找到像`eslint`和`rubocop`这样的东西。浏览一些包，找到你觉得好看的！通常情况下，你会寻找那些拥有你想要的功能，并且已经安装了很多次的东西。查看下面的列表，了解一些好的软件包。你也可以谷歌一下“为 javascript 设置 vscode”这样的东西来找到好的包和教程。

# 高级研究

一旦你开始适应你的编辑器，我强烈推荐你构建一个包。主题是一个很好的起点，但是构建插件是一个很好的方式来分享你的工作流程，并提高你对编辑器内部工作的理解。

# 链接和更多

## 编辑人员

[VS 代码](https://code.visualstudio.com/)

[Atom](https://atom.io/)

[记事本++](https://notepad-plus-plus.org)

[崇高的文字](https://www.sublimetext.com/)

[括号](http://brackets.io/)

## 分机还有更多

注意:Awesome 列表是寻找各种资源的好地方！有疑问的时候，谷歌“牛逼榜< thing >”。

[牛逼 VS 代码](https://github.com/viatsko/awesome-vscode)

[牛逼原子](https://github.com/mehcode/awesome-atom)

[记事本++插件](https://www.guidingtech.com/31357/notepad-plus-plugins/)

[牛逼的括号](https://github.com/viatsko/awesome-brackets)

**编辑:更多来自社区的链接！**

[![glennmen profile image](img/3b2793e46883755ba869d320edc85bd4.png) ](/glennmen) [ Glenn Carremans ](/glennmen) • [<time datetime="2019-02-27T18:37:30Z"> Feb 27 '19 </time>](https://dev.to/glennmen/comment/94ge) 

目前我选择的主题几乎用于所有的东西(iTerm，Android Studio，PHPStorm，...)是德古拉。它是开源的，支持许多不同的工具。

链接:[draculatheme.com/](https://draculatheme.com/)

[![learnbyexample profile image](img/5dd14b9f2fd6970e2f3fc695852ba5e6.png) ](/learnbyexample) [ Sundeep ](/learnbyexample) • [<time datetime="2019-02-28T12:09:56Z"> Feb 28 '19 </time>](https://dev.to/learnbyexample/comment/94oh) 

我使用 Vim，所以这里是我整理的 Vim 资源列表

我也有一本 emacs 的书签[Emacs 资源的终极收藏](https://batsov.com/articles/2011/11/30/the-ultimate-collection-of-emacs-resources/)

[![learnbyexample profile image](img/5dd14b9f2fd6970e2f3fc695852ba5e6.png) ](/learnbyexample) [ Sundeep ](/learnbyexample) • [<time datetime="2019-02-28T12:14:05Z"> Feb 28 '19 </time>](https://dev.to/learnbyexample/comment/94ol) 

还有 VSCode 的 [VSCodium - Free/Libre 开源软件二进制](https://github.com/VSCodium/vscodium)