# 使用自动热键在 Windows 10 上重新映射和设置全局热键

> 原文：<https://dev.to/nickjj/remap-and-set-global-hotkeys-on-windows-10-with-auto-hotkey-4h50>

**本文最初发布于 2019 年 3 月 12 日:[https://nickjanetakis . com/blog/remap-and-set-global-hotkeys-on-windows-10-with-auto-hotkey](https://nickjanetakis.com/blog/remap-and-set-global-hotkeys-on-windows-10-with-auto-hotkey)**

* * *

Windows 10 附带了 50 多个分配给 Windows 键的全局热键组合，例如可以按下`Win + e`打开 Windows 文件资源管理器，这非常方便，但我个人认为大多数预先分配的热键都没有那么有用。

全局热键是一个适用于整个系统的热键，并不局限于特定的应用程序。这意味着，如果你从任何地方按下它，该动作将被触发，在这篇文章中，我们将讨论如何设置我们自己的自定义全局热键，甚至能够覆盖 Windows 自带的热键，而不必重新启动或触摸注册表。

## 下载自动热键(AHK)

我们将要使用的工具叫做[自动热键(AHK)](https://github.com/Lexikos/AutoHotkey_L) 这是一个用于 Windows 的开源工具，可以让你自动完成各种事情，但是重新映射按键和设置全局热键是它做得非常好的事情。

你可以从他们的下载页面下载。我建议在撰写本文时获取最新的稳定版本 1.1.x。然后下载完再安装。

值得注意的一点是，与大多数运行 AHK 脚本的“现代”应用程序不同，我们将在本文中创建的应用程序只使用 2.5mb 的 RAM，根本不使用任何空闲的 CPU 资源。

## 创作 AHK 剧本

AHK 是由脚本语言驱动的，启动并运行它不需要做任何疯狂的事情。你只需创建你的脚本，然后运行 AHK 文件。

因此，在你电脑上的任何地方创建一个名为`hotkeys.ahk`的新文件。你也可以选择别的名字，只要是以`.ahk`结尾的。

#### 重新映射按键和设置全局热键

以`;`开头的行被认为是注释，被 AHK 忽略。T3】

```
; Remap hotkeys
CapsLock::Esc
Esc::CapsLock

; Global hotkeys
#c::Run, explorer.exe D:\courses\deploying
#i::Run, C:\Program Files\GIMP 2\bin\gimp-2.10.exe
#n::Run, explorer.exe D:\src\sites\nickjanetakis
#m::Run, foobar2000.exe
#t::Run, %UserProfile%\AppData\Local\wsltty\bin\mintty.exe --WSL="Ubuntu-18.04" --configdir="%UserProfile%\AppData\Roaming\wsltty" -~
#+t::Run, *RunAs %UserProfile%\AppData\Local\wsltty\bin\mintty.exe --WSL="Ubuntu-18.04" --configdir="%UserProfile%\AppData\Roaming\wsltty" -~
#w::Run chrome.exe
#+w::Run chrome.exe -incognito 
```

Enter fullscreen mode Exit fullscreen mode

有很多事情是我的设置所特有的，所以让我们来分解一下，因为很有可能你最终会修改大部分的全局热键来为你工作。

##### 重映键:

自从我大约一个月前开始使用 Vim 以来，我做的一件事就是用 caps lock 重新映射我的退出键。我真的很喜欢这种改变，只用了一天左右就适应了。

要用 AHK 重新映射键，您可以添加如下行:`KEY_OLD::KEY_NEW`。你可以在 AHK 官方网站上查看的完整列表。

值得一提的是，您需要绑定重映射的两端。在本例中，我们将 caps lock 重新映射为 escape，但是我们也需要将 escape 重新映射为 caps lock。

最终效果是两个键将被交换，就像你在键盘上交换它们一样。此外，如果您的 caps lock 键有灯，按下 escape 将切换它，这样您就不会失去该功能。

##### 设置全局热键:

我们来分解一下第一行:`#c::Run, explorer.exe D:\courses\deploying`。

总体模式是:

*   `#`修饰键([这里有一个完整的列表](https://autohotkey.com/docs/KeyList.htm#modifier)
*   `c`要绑定的键
*   设置重新映射(AHK API 的一部分)
*   你想做的事情(AHK API 的一部分)
*   `explorer.exe`您想要运行的程序
*   `D:\courses\deploying`选择性地设置程序的参数

你可以把这想象成我们正在绑定`Win + c`来运行 Windows 文件资源管理器程序并打开一个特定的目录。

##### 组合修饰语:

我们可以在最后一行的`#+w::Run chrome.exe -incognito`中看到一个例子。

我已经将`Win + w`设置为打开一个常规的 Chrome 窗口，但是`Win + Shift + w`打开的是隐名模式的 Chrome。当我想录制课程时，这很方便，因为我不希望我的个人设置出现在视频上。

##### 以管理员身份启动程序:

你会注意到我有两个热键来启动一个终端窗口。第二个包括`#+t::Run, *RunAs`。`*RunAs`允许你以管理员身份运行程序。

偶尔我还会使用 travel，在 Windows 上运行 travel 需要以管理员身份运行，因为我从 WSL 内部与`vagrant.exe`交互，所以需要以管理员身份启动终端，这就是我创建这个快捷方式的原因。

##### 改变热键为你工作:

我正在启动一些你可能没有的目录路径和程序，但是我把它们包括在这里，所以你可以看到我用全局热键启动的是什么类型的东西。

你应该用对你有用的东西来代替它们。

但是在添加热键的时候，这里有两个小技巧你可能会觉得很方便。

1.  我喜欢把我的钥匙按字母顺序排列，这样更容易浏览。

2.  我喜欢用键来描述我正在运行的程序的类型，而不是它的名字。比如我用“I”打开一个图片编辑器，恰好是 GIMP。

    在另一个例子中，如果我切换到 Fire Fox，我的键绑定仍然是`Win + w`来打开一个网络浏览器，而不是必须改变它来匹配程序的名称。

同样值得指出的是，我更喜欢使用`explorer.exe`而不是`Explorer`，因为很明显我正在启动一个应用程序。另外，使用`.exe`风格，你可以传入参数(比如我正在用 Chrome 做什么)。如果我用了`Chrome -incognito`，那就不行了。

##### AHK 语法高亮显示在你最喜欢的代码编辑器中:

你的代码编辑器有可能支持 AHK 语法高亮显示，不管有没有插件。我建议检查一下，因为 AHK 是一种合法的脚本语言。

[![AutoHotKey syntax highlight vim](img/0e54f70741b7ea4fc45f53110c33ca80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FiMDuDk7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickjanetakis.com/assets/blog/autohotkey-syntax-highlight-vim-5792a95ee327c665951e7919a68d4c5f09d865459e94c1da1b8a314d87ca6709.jpg)

Vim 默认支持自动热键。

## 运行 AHK 脚本

现在我们已经准备好了脚本，剩下唯一要做的就是运行它。

当你安装 AHK 的时候，它会注册一个`ahk`文件扩展名。如果您看到文件有一个“H”图标，那么您就可以开始了。你所要做的就是双击它来运行它。

如果你没有看到，那么右键单击你的文件，进入“打开方式”，然后从要使用的应用程序列表中选择自动热键。

运行后，它会出现在你的系统托盘中(在你的时钟旁边)。此时你可以试试你的热键。只要脚本中没有语法错误，它们应该可以工作。

#### 编辑后重新载入 AHK 脚本

如果你决定编辑你的脚本来添加更多的热键，你可以右击你的系统托盘中的脚本，并从菜单中选择“重新加载脚本”。没必要重启。

##### 其他有趣的 AHK 特色:

自动热键不仅仅是一个热键脚本，这就是为什么它有其他有用的工具，如窗口间谍，让你找到一堆关于任何窗口的低级细节。

这个窗口间谍是你脚本的右键菜单选项。

例如，如果您想知道一个已经打开的程序的确切可执行文件名称，您可以运行窗口间谍，启用右上角的“跟随鼠标”,然后将鼠标悬停在您想要了解更多信息的程序上。

它将在顶部第一个文本框中显示`ahk_exe`行。这将是您在热键脚本中运行的程序的名称。

#### 在 Windows 启动时运行您的 AHK 脚本

你的热键脚本只有在运行时才会起作用，默认情况下，AHK 脚本不会在 Windows 启动时启动，所以我们必须自己来做。

1.  在 Windows 资源管理器中找到您的`hotkeys.ahk`文件，并复制文件本身
2.  点击`Win + r`运行 Windows 启动程序(如果你还没有覆盖:D 键的话)
3.  运行`shell:startup`
4.  右击任意位置并选择`Paste shortcut`将其粘贴到启动文件夹中
5.  将文件重命名为`hotkeys.ahk`，因为`- Shortcut`部分没有用

如果你做的正确，图标的左下方应该有一个小箭头。这是你真实脚本的快捷方式(类似于符号链接)。

`Start Menu > Programs > Startup`路径是一个特殊的路径，当 Windows 启动时，这里的任何东西都会自动启动。

如果你想让你的 AHK 脚本不再启动，你可以在这里删除快捷方式。它不会删除原始脚本文件。

这就是在 Windows 中使用全局热键的全部内容。

你打算和 AHK 设定什么热键？下面让我知道！