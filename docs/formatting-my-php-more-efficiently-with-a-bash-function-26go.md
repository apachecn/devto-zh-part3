# 用 Bash 函数更有效地格式化我的 PHP

> 原文：<https://dev.to/alexmacarthur/formatting-my-php-more-efficiently-with-a-bash-function-26go>

很长一段时间以来，我一直在开发一个 PHP 应用程序，直到最近，它还没有明确定义的编码标准。在某个时候，决定实施新 PSR 协议，并以递增的方式实施。当一个文件被触摸时，格式化为 PSR-2。

为了在命令行中做到这一点，我一直在使用 [PHP-CS-Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer) 。它的工作原理非常简单。调用命令，传递文件，指定标准:

```
php-cs-fixer /path/to/file.php --rules=@PSR2 
```

Enter fullscreen mode Exit fullscreen mode

但是很快，输入所有这些字符加起来，这变得更加困难，因为我需要按住`SHIFT` + `2`才能写出一个`@`。我好苦啊

## 将复杂性包装在一个函数中

为了补救这一点，我转向编写一个简单的 Bash 函数来包装我反复做的一些工作。这是一步好棋。事情是这样的:

在我的`~./zshrc`文件中，保存下面的函数声明。如果你没有使用[哦，我的 ZSH](https://ohmyz.sh/) ，这将被放在你的`~/.bashrc`文件中，或者你正在使用的任何 shell 的配置文件中。

```
function fphp {
    php-cs-fixer fix $1 --rules=@PSR2
} 
```

Enter fullscreen mode Exit fullscreen mode

要实际使用这个函数，您需要重新加载您的配置文件。

```
source ~/.zshrc 
```

Enter fullscreen mode Exit fullscreen mode

之后，你就可以跑了！不需要指定一个长的命令名，粘贴到文件路径中，然后设置一个标准，只需运行这个命令就可以得到相同的结果。

```
fphp /path/to/file.php 
```

Enter fullscreen mode Exit fullscreen mode

## 处理多条路径

这就好得多了，但有时，您可能希望一次按文件路径格式化几个不同的文件。使用我现在的 PHP-CS-Fixer 版本(2.14.0 ),技术上可以做到这一点，但是需要[一些我不想处理的额外工作](https://github.com/FriendsOfPHP/PHP-CS-Fixer/issues/2390)。如果不做这些额外的工作，就会抛出下面的错误:

```
php-cs-fixer /path/to/file.php /path/to/some/other/file.php --rules=@PSR2

In ConfigurationResolver.php line 579:
For multiple paths config parameter is required. 
```

Enter fullscreen mode Exit fullscreen mode

令人欣慰的是，由于我们想要的功能都被包装在一个函数中，当我们提供多条路径时，增强它来处理多条路径是相对容易的。基本上，无论我们的函数给定了什么参数，对每个参数运行相同的命令。

```
function fphp {
    for filePath in "$@"
    do
        php-cs-fixer fix $filePath --rules=@PSR2
    done
} 
```

Enter fullscreen mode Exit fullscreen mode

运行`source ~/.zshrc`，现在我们可以轻松地一次格式化多个文件:

```
fphp /path/to/file.php /path/to/some/other/file.php 
```

Enter fullscreen mode Exit fullscreen mode

## 我们只触及了表面

显然，我在这里的用例是非常具体的，还有无数其他的方法来攻击函数化(只是杜撰出来的；它最好坚持)可以优化您的命令行工作流程。举一个我最先想到的例子，我的队友和*好友* [好友雷诺写了关于使用它们](https://medium.freecodecamp.org/bash-shortcuts-to-enhance-your-git-workflow-5107d64ea0ff)来彻底改变你如何在你的项目中使用 Git。

无论你的切入点是什么，尽你的一份力，慷慨地分享你的发现！首先，如果你有过去对你有帮助的 Bash 相关技巧，请在这里分享！