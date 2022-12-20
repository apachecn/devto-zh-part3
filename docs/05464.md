# Shells:如何在命令中使用修改后的环境

> 原文：<https://dev.to/nabbisen/shells-how-to-use-an-environmental-variable-in-commands-4mfd>

### 简介

我喜欢鱼🐟壳牌因其便捷的汽车建议和卓越的性能。

然而，我在从[切换到](https://www.gnu.org/software/bash/)时遇到了一个稍微严重的问题。
我不知道如何在命令中使用修改过的环境。

例如，我在搜索如何更改主子目录的名称，发现了这样的 bash 解决方案🤔:

```
LANG=C xdg-user-dirs-gtk-update 
```

这意味着为`LANG`环境运行设置为“C”的`xdg-user-dirs-gtk-update`命令。
在 fish shell 中运行相同的命令失败...🥴

然而，我不喜欢总是使用额外的步骤，如`bash`命令或`set`。

我写这篇文章是为了演示如何解决这个问题。

*另外:就在今天，我发现了 fish prints 的大错误消息。天哪！：

> fish:不支持使用“=”。要在修改后的环境中运行' xdg-user-dirs-gtk-update '，请使用' env LANG = C xdg-user-dirs-GTK-update…'

### 正文

为了设置一个修改过的环境，在 bash 中可以在顶部使用`SOME_ENV=value`格式:

```
EDITOR=vi git commit 
```

关于 [ksh](http://www.kornshell.org/) / [zsh](https://www.zsh.org/) 也是一样。

但不是关于鱼的！

没有任何解决办法吗？是的。如果我们使用 [`env`](https://en.wikipedia.org/wiki/Env) 命令，那么是可能的😊：

> *`SOME_VAR=1 command`产生一个错误:`Unknown command "SOME_VAR=1"`。*
> 使用`env`命令。
> `env SOME_VAR=1 command`
> 你也可以在一个块中声明一个局部变量:

`env SOME_ENV=value`格式工作得很好！！

```
env SOME_ENV=vi git commit 
```

tcsh 也是如此。

### 结论

| 壳 | 版本 | 语法 |
| --- | --- | --- |
| **痛击** | 5.0.2 | `SOME_ENV=value command`*<small>*注意:如果`env` 加在命令的顶部，似乎会被忽略。</small>* |
| **ksh** | 5.2.14 |
| **zsh** | 5.7.1 |
| **鱼** | 3.0.2 | `**env** SOME_ENV=value command` |
| **tsch** | 6.20.0 |

### 推荐人和我的感谢

*   [https://fishshell.com/docs/current/faq.html#faq-single-env](https://fishshell.com/docs/current/faq.html#faq-single-env)
*   [http://hyperpolyglot.org/unix-shells](http://hyperpolyglot.org/unix-shells)

快乐经营🐈