# 我如何电子邮件，你也可以:包

> 原文：<https://dev.to/deciduously/how-i-emacs-and-so-can-you-packages-m9p>

在第一篇文章中，我们看了一些基本的用法和导航，并设置了`use-package`以便我们可以轻松地将社区包添加到我们的 Emacs 安装中。

## 呼吸间

我想我这样做有点过火了，但是每当我的一个`use-package`声明超过一行时，我喜欢把它拉到自己的文件中。这样，我只需要在`init.el`中注释/取消注释一行就可以激活/停用一个包。要设置这个，在`.emacs.d`中创建一个目录——我刚刚调用了我的目录`.emacs.d/lisp`。我们可以通过向`init.el`添加以下内容来确保它得到评估:

```
;; Pull in ./lisp/*
(add-to-list 'load-path (expand-file-name "lisp" user-emacs-directory)) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们放在这个目录中的任何`whatever.el` elisp 文件都将对`init.el`可见。

Emacs 生态系统很大，任何给定的问题都有多种解决方案和解决方案集。我喜欢保持我的很少，这只是一套为我工作-我敦促你去探索！值得注意的是，该套件中使用的软件包与 Spacemacs 基于的套件不同。当你做你自己的研究时，它多少可以归结为 [`helm`](https://github.com/emacs-helm/helm) &朋友对[`ivy/swiper/counsel`](https://github.com/abo-abo/swiper)——这是`ivy`路线。我有意想尝试一些不同于我通过 Spacemacs 了解的东西，但这根本不应该被视为一种价值判断。我非常喜欢使用这两种工具。

这些是完成引擎。还记得上一个帖子吗，当我们忘记了`C-x C-s`但仍然奇迹般地知道它是`save-buffer`？有了`ivy`，你只需点击`M-x`，然后疯狂地开始输入`save`，`ivy`就会找到所有可能的东西。它甚至会很有帮助地显示给定命令分配的组合键(如果有的话)。使用 Emacs 这样庞大的工具非常方便！这是一个私人的永远在线门房。

### 常春藤/法律顾问/Swiper

这是一个很好的起点。Ivy 是这里的主要事件，`counsel`和`swiper`分别是通用命令和文件搜索的`ivy`版本。创建名为`init-ivy.el` :
的文件

```
;;; #init-ivy.el
;;; Commentary:
;;; http://oremacs.com/swiper/#installation
;;; https://sam217pa.github.io/2016/08/30/how-to-make-your-own-spacemacs/#fnref:3
;;; https://writequit.org/denver-emacs/presentations/2017-04-11-ivy.html#fnr.2
;;; Code:
(use-package ivy
  :diminish (ivy-mode . "")
  :init (ivy-mode 1) ; globally at startup
  :config
  (setq ivy-use-virtual-buffers t)
  (setq ivy-height 20)
  (setq ivy-count-format "%d/%d "))
(provide 'init-ivy)
;;; init-ivy.el ends here. 
```

Enter fullscreen mode Exit fullscreen mode

在这个相同的文件中，我还设置了`counsel`。这个包用更加用户友好的版本覆盖了一些内置的 Emacs 命令。把这个加到最后的评论上面:

```
;; Override the basic Emacs commands
(use-package counsel
  :bind* ; load when pressed
  (("M-x"     . counsel-M-x)
   ("C-s"     . swiper)
   ("C-x C-f" . counsel-find-file)
   ("C-x C-r" . counsel-recentf)  ; search for recently edited
   ("C-c g"   . counsel-git)      ; search for files in git repo
   ("C-c j"   . counsel-git-grep) ; search for regexp in git repo
   ("C-c /"   . counsel-ag)       ; Use ag for regexp
   ("C-x l"   . counsel-locate)
   ("C-x C-f" . counsel-find-file)
   ("<f1> f"  . counsel-describe-function)
   ("<f1> v"  . counsel-describe-variable)
   ("<f1> l"  . counsel-find-library)
   ("<f2> i"  . counsel-info-lookup-symbol)
   ("<f2> u"  . counsel-unicode-char)
   ("C-c C-r" . ivy-resume)))     ; Resume last Ivy-based completion 
```

Enter fullscreen mode Exit fullscreen mode

不要太担心马上记住这里的所有东西——当你需要的时候，它们就在这里。有一段时间，我的桌子上放着一张索引卡，上面有一些最方便的卡片。在上一篇文章中，我们讨论了“保存”操作，这比你可能习惯的多了一个完整的按键——这是因为`C-s`是为在给定文件中搜索文本而保留的。查看[视频演示](https://www.youtube.com/watch?v=VvnJQpTFVDc)。

### 插曲:等等，总共有模式

是的，但是它们不是 Vim 模式！在 Emacs 中，`mode`决定了 Emacs 如何从语义上理解当前缓冲区中的文本。这些分为两类，`major`和`minor`——每个缓冲器有一个主模式，也可以有多个次模式。一个主要模式可能是类似于`clojure-mode`的东西——这个文本只是 Clojure 代码，而不是其他类型的代码，但是也可以启用`ivy-mode`和`spellcheck-mode`，因为功能可以堆叠。

好了，现在`init-ivy.el`已经被添加到`lisp/`中，我们可以把它添加到`init.el` :

```
(require 'init-ivy) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！用`C-x C-e`计算那个`require`表达式将会读取我们的新文件并为我们设置 Ivy。

### 飞行检查

我喜欢的另一个包是 [flycheck](https://www.flycheck.org/en/latest/) ，它提供动态语法检查。它有问题行的指示器、弯曲的下划线和弹出工具提示——所有现代语法检查器的标志。这个宣言更简单:

```
;;; lisp/init-flycheck.el
(use-package flycheck
  :init (global-flycheck-mode))
(provide 'init-flycheck) 
```

Enter fullscreen mode Exit fullscreen mode

并且在`init.el` :

```
(require 'init-flycheck) 
```

Enter fullscreen mode Exit fullscreen mode

有些语言需要特殊的设置，但大多数东西开箱即用。

### 公司

对`flycheck-mode`的完美补充是 [`company-mode`](https://company-mode.github.io/) ，它提供了文本补全。在你打字的时候，它会提出建议。您可以使用`M-n`和`M-p`滚动浏览它们，并使用回车键进行选择。也有更多的方法与它互动——查看文档寻找 deets。

在`lisp/init-company.el` :

```
(use-package company
  :config
  (add-hook 'after-init-hook 'global-company-mode))
(provide 'init-company) 
```

Enter fullscreen mode Exit fullscreen mode

当然还有`init.el`里的`(require 'init-company)`。现在我们开始感觉像一个真正的 IDE 了！

### 哪个键

这可能是我最喜欢的一个了。Ivy 为我们提供了一些很好的补充，但是您仍然需要知道从哪里开始——这对于发现可用的内容来说不是很好。当你开始一个列出所有可用信息的命令时，哪个键会弹出一个窗口。在我们的`save-buffer`示例中，当您键入第一个`C-x`时，您会看到一个大窗格，详细列出了在`C-x`之后可用的每个组合，以及组合和命令名。这就是我如何找到新的组合来学习的，这对唤醒你的记忆很有帮助。

我的`init-which-key.el` :

```
(use-package which-key
  :init
  (which-key-mode)
  :config
  (which-key-setup-side-window-right-bottom)
  (setq which-key-sort-order 'which-key-key-order-alpha
    which-key-side-window-max-width 0.33
    which-key-idle-delay 0.05)
  :diminish which-key-mode)

(provide 'init-which-key) 
```

Enter fullscreen mode Exit fullscreen mode

根据你的喜好调整这些，这些设置对我有用。当然，别忘了`init.el`里的`(require 'init-which-key)`！

### 智能对

这个次要模式有助于管理您的括号。它有许多用于操作括号表达式的[工具](https://github.com/Fuco1/smartparens)——不管你使用什么编程语言，这都是一个巨大的帮助。

`lisp/init-smartparens.el` :

```
(use-package smartparens
  :config
  (require 'smartparens-config)
  (add-hook 'lisp-mode-hook #'smartparens-strict-mode))
(provide 'init-smartparens) 
```

Enter fullscreen mode Exit fullscreen mode

我添加了一个钩子，当我处于特定的次要模式时，它会激活一个更严格的版本——这也是你需要自己调整的地方！我实际上也在`rust-mode`中使用了`smartparents-strict-mode`——我们将在后面讨论语言特有的东西。

现在你已经知道进入`init.el`的程序了！

### 纽特里

这是我最后一个包裹。Neotree 是我从 Vim 学到的一个习惯——它显示了目录树的图形化概览，您可以使用它在文件之间切换。ide 感觉他们应该拥有的另一个好处——尽管在大多数情况下，我发现自己在项目中调用`C-x C-f`或`C-x b`来导航。

`lisp/init-neotree.el` :

```
(use-package neotree
  :init
  (require 'neotree)
  :config
  (setq neo-theme (if (display-graphic-p) 'icons 'arrow))
  (setq neo-smart-open t)
  )
(provide 'init-neotree) 
```

Enter fullscreen mode Exit fullscreen mode

我撒谎了-那是倒数第二个。我也用 [`find-file-in-project`](https://github.com/technomancy/find-file-in-project) 。

```
(use-package find-file-in-project) 
```

Enter fullscreen mode Exit fullscreen mode

## 按键绑定

下一步是设置你自己的键绑定。为此我们可以使用`global-set-key`。我设置的第一个是激活`neotree`的键——把这个加到你的`init.el` :

```
(global-set-key [f8] 'neotree-project-dir) 
```

Enter fullscreen mode Exit fullscreen mode

为了实现这种行为，我将从 emacs wiki 中窃取的以下代码片段放在`lisp/bl-fns.el`中，以便于 NeoTree 在 git 项目打开时尝试使用它的根目录:

```
(defun neotree-project-dir ()
  "Open NeoTree using the git root."
  (interactive)
  (let ((project-dir (ffip-project-root))
    (file-name (buffer-file-name)))
    (if project-dir
    (progn
      (neotree-dir project-dir)
      (neotree-find file-name))
      (message "Could not find git project root."))))

(provide 'bl-fns) 
```

Enter fullscreen mode Exit fullscreen mode

很简单，对吧？现在 F8 键将切换纽特里窗口。酷毙了。我为自己添加的另一个有用的键绑定是:

```
(global-set-key (kbd "C-c q") (lambda ()
                       (interactive)
                   (other-window -1))) 
```

Enter fullscreen mode Exit fullscreen mode

`kbd`宏允许你使用方便的简写来定义组合。这个组合`C-c q`，将切换回前一个活动窗口。我一般只打开两三个，发现自己经常用这个。

我也喜欢这个`company-complete` :
的简写

```
(global-set-key (kbd "C-c h") 'company-complete) 
```

Enter fullscreen mode Exit fullscreen mode

## 语言特定包

### Clojure

对于 clojure，我用[苹果酒](https://github.com/clojure-emacs/cider) :

```
;; init-clojure.el
(use-package clojure-mode)
(use-package cider)
(provide 'init-clojure) 
```

Enter fullscreen mode Exit fullscreen mode

苹果酒本身就是一大堆蠕虫——我会在另一篇文章中回头再谈这个问题！

### 生锈

Rust 还在用 flycheck 和 cargo 等工具进行设置:

```
;; init-rust.el
(use-package rust-mode)
(use-package flymake-rust)
(use-package racer)
(use-package company)
(use-package cargo
  :config
  (add-hook 'rust-mode-hook 'cargo-minor-mode))
(use-package flycheck-rust)
(with-eval-after-load 'rust-mode
  (add-hook 'flycheck-mode-hook #'flycheck-rust-setup))
(provide 'init-rust) 
```

Enter fullscreen mode Exit fullscreen mode

老实说，Rust 是我迁移到 VSCode 的最大驱动力 Emacs 中的 Rust 太棒了，VSCode 中的 Rust 无与伦比。上面的工作很好，但我不能真诚地推荐这个设置*而不是*使用来自 VSCode 的 RLS。

### 更一些

第四，JavaScript/HTML/CSS 和 Reason/OCaml 我用零配置:

```
(use-package forth-mode)
(use-package js2-mode)
(use-package reason-mode)
(use-package web-mode)
(use-package ocp-indent) 
```

Enter fullscreen mode Exit fullscreen mode

和....这就是我给你的全部！这组包提供了一个完整的多语言 IDE，没有太多的膨胀。

要更新您已安装的软件包，运行`M-x list-packages` -这将刷新最新的软件包列表。然后只需键入`U` (shift-u)来升级任何过时的。