# 开发人员工具:命令行

> 原文：<https://dev.to/wuz/developer-tools-the-command-line-3f66>

又见面了！

欢迎来到本系列的下一篇文章！今天我们将讨论一些命令行和终端模拟器。同样，这是一个在不同操作系统之间变化很大的话题，所以请查看本系列的第二篇文章以获得一些好的选择！让我们直接跳进来吧！

# 终端仿真器

让我们来一次回忆之旅。在计算的早期，并不是每个人都有自己的电脑。取而代之的是，一个机构可能有一台允许其员工使用的电脑。要访问这台计算机，你可能需要一个基本的显示器和一个键盘设置，比如 [VT100](https://en.wikipedia.org/wiki/VT100) 。这种设置被称为终端，它允许你输入由主计算机处理的命令。跳到今天，你可能会使用类似 Terminal.app、iTerm、kitty 或 Gnome 终端的东西。这些被称为终端模拟器，它们是一种软件，被设计成像老式的学校终端一样，允许你从计算机输入和输出。唯一不同的是，你正在使用的计算机就在你面前，而不是在校园的另一栋大楼里。

你可能想知道为什么我们使用一些模仿旧概念的东西——我们现在不是有 GUI 和工具吗？在某些方面，你是对的——许多旧工具已经被更图形化的替代工具所取代。但事实证明，文本仍然是与计算机互动的一种很好的方式。在我们将优秀的语音命令嵌入所有软件之前，文本是做很多事情的最快方式。

# 外壳

现在我们知道我们可以用什么样的程序通过文本与计算机进行交互。shell 是读取文本并决定做什么的程序。它接受一个类似于`git commit -m 'Add the things!'`的字符串，并决定它应该路由到`git`并需要传递参数`commit -m 'Add the things!'`。他们拿走一个程序，然后像壳一样包装它的执行。😉

有许多不同的 shell 程序，它们都有自己的特性。许多人添加小部件和简化来提高您的工作效率，这通常隐藏了 shell 程序的实际功能。有些用奇特的方式与您以前的命令进行交互，而另一些将 Python 等编程语言直接集成到 shell 中。同样值得注意的是，除了是可以运行的程序之外，每个 shells 都是一种编程语言，允许您编写命令行函数和程序。

## 痛击

Bash 是大多数 Unix 操作系统的默认 SHell。它有很多很棒的功能，比如制表符补全和别名。这是我使用的 shell——我喜欢它，因为那里有很多很棒的资源，而且它不会做太多事情。这是优秀 shellss 的基准，其他许多 shell 也部分基于 Bash 提出的思想。Bash 现在也集成在 Windows 上，这使得切换计算机/操作系统更加容易。

## zsh

[zsh](http://zsh.sourceforge.net/) (z-shell)基本上是 Bash 的一个更具交互性的版本。除了制表符补全之外，它还具有提前键入搜索和自动补全功能。大多数使用 zsh 的人都会安装 [ohmyzsh](https://ohmyz.sh/) ，这为您的 shell 提供了一些非常好的默认设置。对于新开发人员来说，这通常是一个不错的选择，因为它有很多用户友好的特性和一个很好的社区。

## 鱼

鱼和 zsh 很像。它有很多很棒的用户友好的特性。Fish 有很好的语法高亮，可以好看！Julia Evans 有一篇关于 T2 鱼的好处的文章。我在考虑将来换成这个来玩玩。

托德喜欢鱼，并在评论中分享道:

[![eidsonator profile image](img/6bd24143987e48c8e5a9c033b2861aeb.png) ](/eidsonator) [ Todd Eidson ](/eidsonator) • [<time datetime="2019-03-01T19:10:13Z"> Mar 1 '19 </time>](https://dev.to/eidsonator/comment/95k8) 

鱼+1。我已经用了快 2 年了。我是你的超级粉丝。还有 [oh my fish](https://github.com/oh-my-fish/oh-my-fish) 进行包管理和修改。

## xonsh

xonsh 是跨平台的，并将 Python 深度集成到 shell 体验中。如果您喜欢 Python 而不喜欢编写 shell 脚本，这可能是一个不错的选择！

## 其他人

有许多外壳程序。人们对这些事情有很多看法。尝试一下，看看你最喜欢什么节目。搜索“bash 的替代品”是找到一些起点的好地方。

# 命令行提示

既然我们已经知道了什么是终端模拟器，以及 shell 是如何工作的，那么让我们来讨论一下命令提示符。命令提示符是您将要键入命令的地方——它提示您进行输入，这些输入将被传递给 shell，shell 将决定如何响应。您很可能会得到某种输出，可能是另一个字符串或图形程序打开。就像 shell 一样，使用命令提示符时有很多可能性。它们的显示方式通常由 shell 处理，但基本的方法是设置一个名为 PS1 的环境变量。环境变量是告诉您的 shell 如何运行的全局变量——它们描述了它运行的环境。

* * *

如果你有兴趣阅读更多关于这些术语的内容，这个 StackOverflow 的答案真的很不错。

# 一些基本命令

*   `cd <directory>` -改为`<directory>` -例如:`cd ~/Projects`
*   `ls <directory>` -列出`<directory>`中的文件-例如:`ls ~/Projects`
*   `cp <file> <location>` -将`<file>`复制到`<location>` -例如:`cp ~/Projects/test.txt ~/textfile.txt`

如果你想了解更多，我建议你查看一下这个教程。

# 赋予你的外壳超能力

一旦你有了一个 shell，你会想要升级它来适应你的工作流程！

## 好看！

首先，让我们从一个好看的提示开始。在大多数 shells 中，这是通过更改 PS1 环境变量来实现的。开始在您的个人文件夹中创建一个文件(您可以打开终端并键入`cd ~`)。让我们创建一个名为`.bash_prompt`的文件。如果你安装了 VS 代码，只需运行`code .bash_prompt`。

一旦文件打开，把这个放在顶部:

```
#!/usr/bin/env bash

# Shell prompt
## Based on wuz/.files (github.com/wuz/.files)

bold="\[\e[1m\]"
reset="\[\e[0m\]"
black="\[\e[30m\]"
blue="\[\e[34m\]"
green="\[\e[32m\]"
cyan="\[\e[36m\]"
red="\[\e[31m\]"
yellow="\[\e[33m\]"
purple="\[\e[35m\]"
brown="\[\e[33m\]"
gray="\[\e[37m\]"
dark_gray="\[\e[90m\]"
light_blue="\[\e[94m\]"
light_green="\[\e[92m\]"
light_cyan="\[\e[96m\]"
light_red="\[\e[91m\]"
light_purple="\[\e[95m\]"
light_yellow="\[\e[93m\]"
white="\[\e[97m\]" 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们一堆颜色来处理。为了记录，这些颜色在 [ANSI 转义码](https://en.wikipedia.org/wiki/ANSI_escape_code)中有描述。

现在我们已经设置了一些颜色，让我们做一个新的提示。将此内容直接添加到您已设置的内容下方。

```
 # ^ rest of file ^

dir_chomp() {
  if [ $PWD == $HOME ]; then echo "~";
  else local ns="${PWD//_/ }"
    local nh="${ns/#$HOME/\~}";
    local wd="${nh%/*}";
    local wdnhrd=`echo $wd | sed -e "s;\(/.\)[^/]*;\1;g"`
    echo "$wdnhrd/${ns##*/}";
  fi
}

# Set the terminal title and prompt.
PS1="${bold}${light_green}\$(dir_chomp)${white}";
PS1+="${blue}${bold}\n→ ${reset}"
export PS1; 
```

Enter fullscreen mode Exit fullscreen mode

这里我们创建一个名为 dir_chomp 的函数，它获取一个类似于`~/Projects/Work/app`的目录，并将其转换为`~/P/W/app`。如果你不喜欢这样，用`PS1="${bold}${light_green}\w${white}";`代替下面这一行:`PS1="${bold}${light_green}$(dir_chomp)${white}";`。这将显示完整的目录路径。我们还使用上面定义的变量设置了一些颜色。有了这些变量，他们会让后面的所有字符都变成那个颜色，直到它们到达一个`${reset}`或另一个颜色变量。

现在我们已经有了，你可以输入`source ~/.bash_prompt`，你应该可以看到你的新提示！试着用`cd`导航，看看`→`字符上方的目录变化。如果你喜欢，运行`code .bashrc`并把它添加到文件的末尾:

```
# end of ~/.bashrc

source ~/.bash_prompt 
```

Enter fullscreen mode Exit fullscreen mode

现在它应该坚持你的编辑。

## 别名

如果您经常使用命令行，您最终会一遍又一遍地运行相同的命令。有时它可以帮助缩短这些命令，这就是别名的用武之地。您可以将这些内容直接添加到您的`~/.bashrc`文件中。他们长这样:

```
alias short='longer command --here' 
```

Enter fullscreen mode Exit fullscreen mode

下面是我化名的一些例子(随便偷！):

```
alias d='docker'
alias di='docker images'
alias da='docker ps -a'
alias drma='docker rm -f $(docker ps -q)'

alias ip="dig +short myip.opendns.com @resolver1.opendns.com"
alias ipl="hostname -I | cut -d' ' -f1"
alias ips="ifconfig -a | grep -o 'inet6\?  \(addr:\)\?\s\?\(\(\([0-9]\+\.\)\{3\}[0-9]\+\)\|[a-fA-F0-9:]\+\)' | awk '{ sub(/inet6? (addr:)? ?/, \"\"); print }'"

alias c="code ."

alias be="bundle exec" 
```

Enter fullscreen mode Exit fullscreen mode

现在你看起来不错，而且更有效率了！您可以做很多定制来定制您的命令提示符！我有一些 git 状态工具。要了解更多信息，请查看 [Awesome Bash](https://github.com/awesome-lists/awesome-bash) 列表，或者尝试在谷歌上搜索“awesome zsh”或“awesome fish shell”来查看其他 shell 列表！

黑客快乐！