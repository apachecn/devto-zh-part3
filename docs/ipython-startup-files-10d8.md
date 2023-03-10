# IPython 启动文件

> 原文：<https://dev.to/switowski/ipython-startup-files-10d8>

在我工作的一家公司，我是一个由五名开发人员组成的小团队的一员。我们有一个支持轮值表，所以每个星期，我们中的一个人负责处理来自用户的票。除了要求新功能，用户经常要求系统做出只有管理员才能做的改变——删除错误提交的评论、替换文件、编辑元数据等等。其中一些任务可以在浏览器中完成，但其他任务必须通过在 [IPython](https://ipython.org) 中键入命令来完成。实际上，通过 lPython 可以比在浏览器中更快地完成这些任务——特别是如果您以前做过，并且保存了一个可以复制和粘贴的食谱。

在某个时候，我注意到几乎每次启动 IPython 时都会输入两三个命令。这些命令从不同的模块导入函数。输入它们不是什么大问题，尤其是因为你可以用 *ctrl+r* 或箭头在 IPython 历史[中搜索。但是我想要一种自动化的方法。](https://ipython.readthedocs.io/en/stable/interactive/reference.html?#search-command-history)

我的第一个想法是将这些命令放在一个文件中，并在启动 IPython 时执行该文件。正如文档中的[所解释的，你可以很容易地做到这一点:](https://ipython.readthedocs.io/en/stable/interactive/reference.html#command-line-usage) 

```
ipython -i my_commands.py 
```

Enter fullscreen mode Exit fullscreen mode

其中`my_commands.py`包含了我想要运行的所有命令。只要您记得启动包含这个文件的 IPython，这是一个不错的解决方案。我总是忘记这么做。所以我在我的`.bashrc`文件中创建了一个别名，它总是通过用我的命令运行脚本来启动 IPython:

```
alias ipython='ipython -i ~/my_commands.py' 
```

Enter fullscreen mode Exit fullscreen mode

在我发现 IPython 启动文件之前，这种方法对我来说非常有效。IPython 启动文件位于以下目录:`~/.ipython/profile_default/startup`带有一个自述文件，说明您放在这里的所有带有`.py`或`.ipy`扩展名的文件都将在 IPython 启动时执行(更具体地说——每次 IPython 使用这个概要文件启动*——在这种情况下，*默认为*概要文件)。这是一个伟大的解决方案！首先，你可以把所有的启动文件放在同一个地方，而不是试图记住你把它们放在哪里。第二，由于*概要文件*的概念，您可以定义一个新的概要文件用于调试。这个概要文件将导入调试所需的所有模块和函数。*

导入模块并不是使用启动文件的唯一方式。你可以在那里定义一些函数，甚至创建你自己的[魔法函数](https://ipython.readthedocs.io/en/stable/interactive/magics.html)。

下面是一个简短的视频，解释了启动文件在 IPython 中是如何工作的:

[![asciicast](img/def6b7f377fd56c0f3ea6b5c85c66499.png)](https://asciinema.org/a/217923)

图片来自:[像素](https://www.pexels.com/photo/young-game-match-kids-2923/)