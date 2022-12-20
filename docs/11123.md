# 我怎么做，你也怎么做

> 原文：<https://dev.to/deciduously/how-i-emacs-and-so-can-you-4434>

Emacs 是一个*整体*。这是一个很好的工具，但是，从来没有人让我坐下来告诉我怎么用它。我想这是一个该死的耻辱，我花了这么长时间才找到它，所以拉一把椅子-我们要为我们设置一些编辑器。

第一篇文章将介绍基本的用法和配置，接下来我将介绍我发现最有帮助的包。

和很多人一样，我是通过 [Spacemacs](http://spacemacs.org/) 来到 emacs 的，这是一个 Emacs 的发行版，预配置了一堆东西，并有自己独立的抽象包集。实际上，很棒。它还很好地集成了`evil`模式，支持 Vim 键绑定，让 Vim 用户更感兴趣。我已经对 Vim 有了一些了解，并犹豫是否要取消这一进展，但是对 Emacs 太好奇了，所以不能不尝试一下。Spacemacs 很棒，但它是一个庞然大物——有很多，比我需要使用的还要多。几个月后，我意识到我并没有真正学到任何关于 Emacs 的东西——我仍然在使用 Vim 绑定，只是从`.spacemacs`中添加和删除了一些层——并没有真正的 Emacs 研究。

所以，我重新开始。我得到了一个空白的`~/.emacs.d`,并开始从头开始构建我想要的编辑器，这一次我认真阅读了 Emacs 教程，以获得“真正的”绑定。我现在混合使用 VSCode 和 Emacs，但是我很高兴我花了时间去了解这个工具到底有多强大，并且现在仍然在 VSCode 中使用 Emacs 键绑定而不是 Vim。

...是的，我在 Emacs 上写了这篇文章。是的，我听到了。

如果你真的打算使用 Emacs，你应该花时间浏览一下教程。在此之前，这里有一个快速概述。

## 快速概述

Emacs 是通过命令的组合来操作的。像 Vim 一样，它提供了一个从键盘控制文本编辑器的方案，尽可能少地离开主行。与 Vim 不同，它有单独的*模式*供你切换进出，Emacs 使用组合键序列。这里没有模式切换，你使用修饰键来指示一个编辑器动作。例如，我们用“Ctrl-x，Ctrl-s”来保存当前的缓冲区，这是你正在处理的当前打开的文本。你可以通过思考“执行保存”来记住它——“Ctrl-x”前缀用于*执行*许多命令。这些序列在 Emacs 中如此常见，以至于有一个简写——这个命令可以写成`C-x C-s`。资本 C 是控制，另一个最常见的是资本`M`。这很可能是您的“alt”键。需要记住的一个很好的组合是`M-x`，它允许您通过名字执行任何 Emacs 命令。Emacs 命令只是 Emacs Lisp 函数，你可以自己写，但是有很多内置的。我们新的最好的朋友`C-x C-s`是恰当命名的`save-buffer`的简写，如果你已经完全忘记了组合，你可以在必要时总是`M-x save-buffer`。

如果这一切听起来会很多，那是因为它绝对是。你怎么知道它就叫这个名字？还有什么？不要烦恼！在下一篇文章中，我们将安装一些非常有用的包，让我们直观地探索这些命令树。一切都很好，我保证，[别慌](https://proxy.duckduckgo.com/iu/?u=https%3A%2F%2Fcatallassi.files.wordpress.com%2F2014%2F04%2Fthe-hitchhikers-guide-to-the-galaxy-dont-panic-1280x1024-wallpaper_www-wall321-com_50.jpg&f=1)！

这里有一些有用的命令。

### 运动

```
C-f Move [f]orward one character
M-f Move [f]orward one word

C-b Move [b]ackward one character
M-b Move [b]ackward one word

C-n Move to the [n]ext line
C-p Move to the [p]revious line

C-a Move to the [a]ft of the line (okay that's a little forced - the beginning of the line)
C-e Move to the [e]nd of the line

M-< Move to the top of the file
M-> Move to the bottom of the file 
```

Enter fullscreen mode Exit fullscreen mode

### 复制/剪切/粘贴

```
Move cursor to beginning of region

C-Spc to set marker

Move cursor to the end of the region

C-w to cut the marked region or
M-w to copy the marked region

Move cursor to target

C-y to paste region 
```

Enter fullscreen mode Exit fullscreen mode

### 文件/窗口/缓冲区

```
C-x C-s save current buffer
C-x C-f Open file
C-x C-c Save and quit emacs
C-x b List open buffers (This will let you select one of them)
C-x 1 Delete all other open windows (This is useful for getting rid of one-off messages that spawn windows)
C-/ Undo - keep going to keep undoing 
```

Enter fullscreen mode Exit fullscreen mode

### 高级:

```
C-M-f Move forward over a balanced expression (words count!  try this one on a bunch of different kinds of files)
C-M-b Move backward over a balanced expression
C-M-k Kill balanced expression forward
C-M-Spc Mark the end of the next s-expression
C-M-n Move forward a parenthetical group
C-M-p Move backward a parenthetical group 
```

Enter fullscreen mode Exit fullscreen mode

一个荣誉提名奖:`C-k [k]ill line`。这将杀死从光标到行尾，也将文本拉入缓冲区。然后可以粘贴用`C-y`杀死的东西。举个例子，我移动我所在行的一个常见模式是`C-a C-k`跳到开始并杀死它，然后用`C-n`或`C-p`和`C-y`把它放到别的地方。自测:`M-< C-Spc M-> M-w`是做什么的？

每个命令都很容易记忆。它不需要很长时间就能让它们在你的手指下。当我学习一个新的动作时，我会在脑海中大声说出我想要的动作。另外，`M`版本通常是您最喜欢的`C`命令的“更抽象”版本。当探索一个新的库时，这通常是一件好事——许多人会定义具有相似特征的组合。

其中一些需要三个键- `M->`还需要一个移位键。古怪，对吧？这肯定需要一些练习，但最终你永远不需要离开家排的位置。

更棒的是，这些组合键随处可见！如果你的系统有`bash`，打开一个终端——`C-f`、`C-b`、`C-a`、`C-e`都工作。任何使用`readline`的东西也会使用这些命令的子集。这可能是常识，但在我尝试 Emacs 之前，我对此一无所知。至少让我大吃一惊。

这只是可用命令的一小部分。你可以制作窗口(尝试使用`C-x 2`或`C-x 3`来水平或垂直分割窗口)并做各种有趣的事情(查看[`C-x z`](https://www.gnu.org/software/emacs/manual/html_node/emacs/Repeating.html))——我不能指望在这里做到所有的公正，所以我不会尝试。我绝对推荐通读教程，看看 [`manual`](https://www.gnu.org/software/emacs/manual/html_node/emacs/index.html#Top) 。这篇文章的重点是配置，这一切应该让你开始和运行。如果需要的话，你也可以退回到箭头键和鼠标来跳来跳去，但是强迫自己不这样做是值得的！

现在我们将开始浏览我的个人`init.el`。请抑制你的激动，我们才刚刚开始。

## init.el

Emacs 实际上是一个 lisp 解释器，捆绑了一个可靠的文本编辑器。我一直认为整个“Emacs vs. Vim”的争论有点可笑——它们完全不同。Vim 适用于使用文本编辑器的时候，Emacs 感觉更像是驱动一个高度可定制的 IDE。没有“Notepad++ vs IntelliJ”的战火正在进行，为什么 Vim 和 Emacs 之间要有一场呢？

不管怎样，美好始于一个叫做`init.el`的文件。这是一个 ELisp 文件，它位于您的`emacs.d`目录中，并在启动时进行评估。我的开始是设置一些变量。这些是我的喜好，季节口味:

```
(setq delete-old-versions -1 ) ; delete excess backups silently
(setq version-control t )
(setq vc-make-backup-files t )
(setq vc-follow-symlinks t )
(setq backup-directory-alist `(("." . "~/.emacs.d/backups")) )
(setq auto-save-file-name-transforms '((".*" "~/.emacs.d/auto-save-list/" t)) )
(setq inhibit-startup-screen t )
(setq ring-bell-function 'ignore ) ; silent bell on mistakes
(setq coding-system-for-read 'utf-8 )
(setq coding-system-for-write 'utf-8)
(setq sentence-end-double-space nil)
(setq-default fill-column 80) ; toggle wrapping text at this column
(setq initial-scratch-message "EEEEEEEEEEEmacs...macs...(macs)... Hi Ben." ) ; You should probably change this
(global-display-line-numbers-mode t )
(menu-bar-mode -1) ; no need for the menu bars - we've got key combos for that!
(toggle-scroll-bar -1)
(tool-bar-mode -1) 
```

Enter fullscreen mode Exit fullscreen mode

@yorodm 善意地建议以下更完整的 UTF-8 配置:

[![yorodm profile image](img/f7543c011ce6b7a1013bed9a56986163.png) ](/yorodm) [ Yoandy Rodriguez Martinez ](/yorodm) • [<time datetime="2019-03-08T14:05:47Z"> Mar 8 '19 </time>](https://dev.to/yorodm/comment/9979) 

嗨，本！！！！很高兴看到你进入了完整的 Emacs 模式！！！

由于那里可能会有一些 Windows 用户，而 UTF-8 在 Emacs 中获得正确是一种痛苦，这里是我自己的`init.el`的一个片段。

```
 ;; UTF-8 please
  (setq locale-coding-system 'utf-8) ; pretty
  (set-terminal-coding-system 'utf-8) ; pretty
  (set-keyboard-coding-system 'utf-8) ; pretty
  (set-selection-coding-system 'utf-8) ; please
  (setq-default buffer-file-coding-system 'utf-8-unix)
  (setq-default default-buffer-file-coding-system 'utf-8-unix)
  (set-default-coding-systems 'utf-8-unix)
  (prefer-coding-system 'utf-8-unix)
  (when (eq system-type 'windows-nt)
    (set-clipboard-coding-system 'utf-16le-dos)) 
```

Enter fullscreen mode Exit fullscreen mode

谢谢你，安迪！

还记得我之前说过 Emacs 是 Lisp 解释器吗？这是严肃的事情。您不需要重新启动编辑器来进行更改，甚至不需要重新加载整个缓冲区。您可以在任何一个带括号的 s 表达式的末尾使用`C-x C-e`,让 Emacs 立即对其求值。哦*是啊*。试着在你眼前打开和关闭滚动条。你也可以使用`M-x eval-buffer`来重新加载整个东西，或者只是标记一个区域，然后使用`M-x eval-region` -你知道吗？

这一部分可读性很强。你使用`setq`来设置变量的值。任何设置为值`-1`的东西就像设置为`false`——我禁用了菜单栏和工具栏以及所有默认打开的额外东西。其中的所有功能也通过无尽的键盘命令树暴露出来。

现在是打包的时候了！

### 使用-包装

Emacs 中的包是强大的，这种强大也带来了一些复杂性。要驯服野兽，我推荐一个叫 [`use-package`](https://github.com/jwiegley/use-package) 的工具。它是一个宏，可以让你以一种整洁的方式划分你的包声明和设置每个包的配置。要用 Emacs 包管理器设置它，将以下内容添加到`init.el` :

```
;; use-package setup
(require 'package)
(setq package-enable-at-startup nil) ; dont do it immediately
(setq package-archives '(("org"       . "http://orgmode.org/elpa/")
             ("gnu"       . "http://elpa.gnu.org/packages/")
             ("melpa"     . "https://melpa.org/packages/")))
(package-initialize)

;; Bootstrap use-package
(unless (package-installed-p 'use-package)
  (package-refresh-contents) ; update archives
  (package-install 'use-package)) ; grab the newest use-package

;; Define packages
(require 'use-package)

;; Always download if not available
(setq use-package-always-ensure t) 
```

Enter fullscreen mode Exit fullscreen mode

别忘了`M-x eval-buffer`！

### 试探一下

为了检查它是否工作正常，让我们添加一个包。一个好的开始是[全图标](https://github.com/domtronn/all-the-icons.el)。这将安装一堆图标和字体——任何地方都不再有空白方块。添加以下内容:

```
(use-package all-the-icons) 
```

Enter fullscreen mode Exit fullscreen mode

将光标放在该行的末尾，点击`C-x C-e`，Emacs 将会安装这个包。它工作是因为我们设置了`(setq use-package-always-ensure t)`。这个特殊的包有一个一次性的设置步骤——现在继续执行`M-x all-the-icons-install-fonts`,这样你就再也不用担心它了。

你应该可以走了！这是一块非常空白的石板——请阅读第 2 部分，以便更有成效地使用它！

哦，对了...上面的自测答案:`M-< C-Spc M-> M-w`会复制整个缓冲区。我本打算等下一封邮件，但我就是等不到。emacs*就是这么刺激*。