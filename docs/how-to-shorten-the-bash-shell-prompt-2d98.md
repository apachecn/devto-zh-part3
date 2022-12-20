# 如何缩短 bash shell 提示符

> 原文：<https://dev.to/anuragrana/how-to-shorten-the-bash-shell-prompt-2d98>

[![](img/718ccfe81588c46630954ec5dbada137.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EemeP62A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Acds9VJJfwti7gLY3HTg6-w.png)

因此，有时你在命令终端上工作，你在一个目录中导航，然后再在内部目录中导航，以此类推，直到屏幕宽度的一半被提示字符串填满。下面的例子。

```
> _rana@Brahma: dir1/dir2/dir3/dir4/dir5/dir6/dir7/dir8 $ ls_ 
```

现在我想只显示终端提示字符串中的最后一个目录，这样我就有足够的空间来执行冗长的命令。

为此，我们需要从`.bashrc`文件中修改提示字符串。以下是步骤-

1.  转到您的主目录。
2.  打开`.bashrc`文件。
3.  搜索字符串“PS1”。要快速搜索，请在 vi 中打开文件，键入/PS1，然后按回车键。
4.  现在你会发现下面的代码。(或者类似的)[我正在做 Ubuntu 16.04]

```
> _if [“$color\_prompt” = yes]; then  
> PS1=’${debian\_chroot:+($debian\_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\_ **_w_** _\[\033[00m\]\$ ‘  
> else  
> PS1=’${debian\_chroot:+($debian\_chroot)}\u@\h:\w\$ ‘  
> fi_ 
```

1.  将“w”(粗体突出显示)替换为大写的 w。

```
> _if [“$color\_prompt” = yes]; then  
> PS1=’${debian\_chroot:+($debian\_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]: \[\033[01;34m\]\_ **_W_** _\[\033[00m\]\$ ‘  
> else  
> PS1=’${debian\_chroot:+($debian\_chroot)}\u@\h:\w\$ ‘  
> fi_ 
```

1.  重启终端并在任意目录中导航。您将在终端提示符下看到最后一个目录。

`> _rana@Brahma: dir8$_`

不断学习。

*   [如何开始 Python 编程——初学者指南](https://www.pythoncircle.com/post/671/how-to-start-with-python-programming-a-beginners-guide/)
*   [如何在 pythonanywhere 上免费托管 django 应用](https://www.pythoncircle.com/post/18/how-to-host-django-app-on-pythonanywhere-for-free/)