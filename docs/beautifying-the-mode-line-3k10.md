# 美化模式线

> 原文：<https://dev.to/gonsie/beautifying-the-mode-line-3k10>

有一些漂亮的 Emacs 模式包，包括华丽的 [doom-modeline](https://github.com/seagle0128/doom-modeline) 。因为我不希望我的主题依赖于任何外部包，所以我实现了一些我喜欢的特性。这一切的结果都可以在我的 dot files[中找到。](https://github.com/gonsie/dotfiles/blob/master/emacs/theme.el#L75)

[![dusk mode line](img/76161fec898a3cc4c49f5985693a8baa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pcUrlhRS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://gonsie.cimg/modeline-shot.png)

## 大小

末日模型的一大优点是它的尺寸。这可以通过在模式线表面周围形成一个框来近似表示(确保为活动和非活动模式线设置该框)。我用了一个在线渐变生成器来挑选接近我所用背景的颜色。

```
(set-face-attribute 'mode-line nil
                    :background "#353644"
                    :foreground "white"
                    :box '(:line-width 8 :color "#353644")
                    :overline nil
                    :underline nil)

(set-face-attribute 'mode-line-inactive nil
                    :background "#565063"
                    :foreground "white"
                    :box '(:line-width 8 :color "#565063")
                    :overline nil
                    :underline nil) 
```

## Git 信息

在模式行中获得 git 分支比它应该的要难。有许多 emacs 内置的版本控制变量和函数，但是没有一个直接获取分支信息。主要问题是`vcs-hooks.el`包重载了函数和变量名。这意味着您必须获得调用函数或返回变量的完全正确的 elisp 语法。

这是一个做错事的函数调用:

```
(vc-mode)

Version Control minor mode.
This minor mode is automatically activated whenever you visit a file under
control of one of the revision control systems in `vc-handled-backends'.
VC commands are globally reachable under the prefix `\[vc-prefix-map]':
\{vc-prefix-map} 
```

这将返回变量的值:

```
vc-mode

Git:develop 
```

幸运的是，`doom-modeline`包做对了。

```
(defun vc-branch ()
  (let ((backend (vc-backend buffer-file-name)))
    (substring vc-mode (+ (if (eq backend 'Hg) 2 3) 2))))
(vc-branch)

develop 
```

这个函数切断版本控制前缀(比如“Git:”或者“Hg:“)。

## 右对齐的文本

我也喜欢一些信息是右对齐的。这段代码片段填充空格以获得相同的效果。注意，我只把主要模式名放在右边

```
'(:eval (propertize
         " " 'display
         `((space :align-to (- (+ right right-fringe right-margin)
                               ,(+ 3 (string-width mode-name))))))) 
```

确保用`'(:eval )`换行，以确保当模式名改变时，该间距被重新评估。

## 总结

总的来说，我对这个模式很满意。实现很简单，但是在终端 Emacs 中看起来很不错，在 GUI 中也非常棒。