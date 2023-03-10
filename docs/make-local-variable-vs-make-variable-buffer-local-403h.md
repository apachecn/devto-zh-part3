# 生成局部变量与生成变量缓冲区局部变量

> 原文：<https://dev.to/zonuexe/make-local-variable-vs-make-variable-buffer-local-403h>

这两个函数名容易混淆，但它们是不同的函数。

这篇文章的内容在 **GNU Emacs Lisp 参考手册**的[创建 Buffer-Local](https://www.gnu.org/software/emacs/manual/html_node/elisp/Creating-Buffer_002dLocal.html) 中有解释。

## TL；速度三角形定位法(dead reckoning)

*   Emacs 中的 **Local** 表示变量可以为每个缓冲区保存独立的值。
*   `make-local-variable`**暂时** 使变量在当前缓冲区中局部化
*   `make-variable-buffer-local`确保变量被 **always** 设置为局部变量。

Emacs Lispers 针对 **Emacs 24.3** 和更高版本应该只使用`defvar-local`和`setq-local`。看一下[它的实现(Emacs 24.3 中增加的`subr.el`)](https://github.com/emacs-mirror/emacs/blob/emacs-26.0.91/lisp/subr.el#L120-L133)，你会发现两个函数的区别。

```
(defmacro setq-local (var val)
  "Set variable VAR to value VAL in current buffer."
  ;; Can't use backquote here, it's too early in the bootstrap.
  (declare (debug (symbolp form)))
  (list 'set (list 'make-local-variable (list 'quote var)) val))

(defmacro defvar-local (var val &optional docstring)
  "Define VAR as a buffer-local variable with default value VAL.
Like `defvar' but additionally marks the variable as being automatically
buffer-local wherever it is set."
  (declare (debug defvar) (doc-string 3))
  ;; Can't use backquote here, it's too early in the bootstrap.
  (list 'progn (list 'defvar var val docstring)
        (list 'make-variable-buffer-local (list 'quote var)))) 
```

## `make-local-variable`

以前我在我的`init.el`里是这样写的:

```
(defun my/enh-ruby-mode-hook ()
  "Enhanced Ruby Modeでだけ ac-ignore-case を t にしたいお"
  (set (make-local-variable 'ac-ignore-case) t)) 
```

使代码变得简单。

```
(defun my/enh-ruby-mode-hook ()
  "Enhanced Ruby Modeでだけ ac-ignore-case を t にしたいお"
  (setq-local ac-ignore-case t)) 
```

如果你是 Lisp 包的作者，请慎重考虑使用这个函数。是否要在本地临时设置*为*？

## `make-variable-buffer-local`

这是 Lisp 软件包作者独有的功能。

```
;;;###autoload
(progn
  (defvar copy-file-on-save-dest-dir nil
    "Path to deployment directory or convert (mapping) function.")
  (make-variable-buffer-local 'copy-file-on-save-dest-dir)
  (put 'copy-file-on-save-dest-dir 'safe-local-variable #'stringp)) 
```

这个函数减少了函数名的重复。

```
;;;###autoload
(progn
  (defvar-local copy-file-on-save-dest-dir nil
    "Path to deployment directory or convert (mapping) function.")
  (put 'copy-file-on-save-dest-dir 'safe-local-variable #'stringp)) 
```

对于以这种方式定义的变量，没有必要使用`setq-local`。请放心使用`setq`。

当您提供可以为每个目录/项目定制的变量时，不要忘记`safe-local-variable`的定义。[目录变量](https://www.gnu.org/software/emacs/manual/html_node/emacs/Directory-Variables.html)比`defcustom` + `setq-local`更好的主意。

...最糟糕的是，`safe-local-variable`和 [`defcustom :type`](https://www.gnu.org/software/emacs/manual/html_node/elisp/Customization-Types.html) 的符号不同。

## 后记

用`defvar-local`和`setq-local`替换*怪双胞胎*，意味着放弃对 **Emacs 24.2** 以下版本的支持。

**Emacs 24.3** 于 2013 年 3 月 10 日发售，但是[我停止支付*烂奶*T5。](https://github.com/emacs-php/php-mode/pull/467)