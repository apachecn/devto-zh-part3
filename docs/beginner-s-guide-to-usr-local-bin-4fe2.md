# 可执行文件及其存储位置的初学者指南

> 原文：<https://dev.to/hbalenda/beginner-s-guide-to-usr-local-bin-4fe2>

也许我应该更不好意思承认这一点，但我总是在安装 CLIs 时向上帝祈祷，这通常是有效的。但我最近买了一台新电脑，所以我不得不安装很多东西，当我按照 Sublime 3 的 OS X CLI 安装说明时，我读到了这个:

> 第一个任务是创建一个到 subl 的符号链接。假设您已经将 Sublime Text 放在“应用程序”文件夹中，并且路径中有一个~/bin 目录，您可以运行:
> 
> `ln -s "/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl" ~/bin/subl`

现在我想说，复制和粘贴我不完全理解的东西，我没有罪，但有时大脑的空间就这么多。看起来我们正在从 Sublime 3 打包的`subl`文件创建一个到`~/bin`的符号链接，我们将能够从命令行执行它，这感觉足够熟悉了。所以我`mkdir bin`复制/粘贴他们的命令，得到的命令没有找到。

然后我就想，是啊，也许我真的不知道这是怎么回事。我有时不也把这种东西放在`/usr/local/bin`里吗？

所以我做了一些研究，并通过运行`man hier`查看了文件系统层次手册页，天哪，我们得到了一些令人困惑的重复目录名:

现代 OS X 文件系统:

*   `/`文件系统的根目录
*   对单用户和多用户环境都很重要的用户实用程序
*   `/dev`设备文件
*   `/etc`系统配置文件
*   `/mach_kernel`内核可执行文件(引导时加载到内存中的操作系统)
*   对单用户和多用户环境都很重要的系统程序和管理实用程序
*   `/tmp`临时文件
*   `/usr`系统范围的只读文件。所有安装的软件都放在这里。
    *   操作系统提供的常用工具、编程工具和应用程序。
    *   操作系统提供的档案库。
    *   `/local`系统范围的只读文件，但仅限于用户(即您)提供的文件。
    *   `/bin`可执行文件
    *   `/lib`图书馆
*   `/var`

因此，操作系统提供的可执行文件和库将位于`/bin`、`/sbin`、`/usr/bin`和`/usr/lib`中。除此之外，我们希望在系统范围内安装的任何东西都应该对所有用户可用，应该放在`/usr/local`中，可执行文件(如我尝试 symlink 的 sublime CLI)应该放在`/usr/local/bin`中。

所以，我**可以**编辑原始命令，将 Sublime CLI 安装到这个:

```
sudo ln -s "/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl" /usr/local/bin/subl 
```

但是如果我们仔细看看最初的 Sublime 命令，它们指向的是`~/bin`，它是`/Users/<username>/bin`的简写。在这里存储只需要对当前用户可用的可执行文件是一个常见的惯例，更好的做法是避免使用 sudo，并在不需要的时候在根目录下安装。但是为了使用它，我需要将`~/bin`(或者，`$HOME/bin`)添加到我的路径变量(存储可执行文件的目录列表)中，方法是编辑我的`.bash_profile`以包含:

```
export PATH="$HOME/bin:$PATH" 
```

现在运行原始崇高命令将按预期工作。瞧啊。现在我可以`subl`尽情享受了，甚至更好的是，我对我的`/usr`目录中发生的事情有了更多的了解。

旁注:关于为什么在`/`和`/usr`中都有`/bin`和`/lib`的一些历史，我推荐看看 2010 年罗布·兰德利的[这篇文章。剧透一下，这是在有限的磁盘空间下工作的遗留问题，不再是理由了！回想起来，这种分裂持续存在的原因已经被反复解释过了！如果这还不能让你对自己作为一个开发者感觉更好，我不知道还有什么能让你感觉更好。](http://lists.busybox.net/pipermail/busybox/2010-December/074114.html)