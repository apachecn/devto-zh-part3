# 我的 EMACS 自定义缩进功能使用“标签宽度”

> 原文：<https://dev.to/emgodev/my-emacs-custom-indent-function-uses-tab-width-34p8>

*我认为图像来自这里:[https://github.com/jcsalomon/smarttabs](https://github.com/jcsalomon/smarttabs)T3】*

我最近花了大量时间定制我的 EMACS 初始化配置。它始于试图理解 Elisp 中更多的迭代过程，所以我阅读了循环；其中大部分都是简单的例子，但是我为我刚才所做的工作感到自豪。

因此，我试图让积极缩进包使用 2 个空格的缩进-人工智能使用本地模式缩进宽度-这并不困难，但我把我的 init 的一部分用于设置这些宽度，我想我至少应该更新我的自定义缩进函数。这是一个我映射到我的空格键的自定义交互函数，*插入* (s)两个空格用于快速缩进。我用它来快速缩进我的代码(回车+空格或行首+空格)。一个恐怖的过程！不管怎样，现在它使用了一个 while 循环和全局 tab-width 变量来*插入*空格。

```
; Previously
;(defun space-times-2 ()
(defun insert-space-indent ()
  (interactive)
  (setq scoped-indent-width tab-width)
  (while (> scoped-indent-width 0)
    (insert " ")
    (setq scoped-indent-width (- scoped-indent-width 1))
  )
) 
```

Enter fullscreen mode Exit fullscreen mode

我认为这很令人兴奋，到处学习 Elisp，这是一种与 web 语言非常不同的语法。如果这是有趣的或有帮助的，我会分享更多我一直在学习的 Elisp/EMACS 的东西。

#candevtobemytwitter