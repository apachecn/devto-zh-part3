# 自动热键鼠标脚本–不用鼠标玩游戏(例如在平板电脑上)

> 原文：<https://dev.to/jalbam/autohotkey-mouse-scripts--play-games-without-a-mouse-on-a-tablet-for-example-3hnk>

[这里](https://github.com/jalbam/AutoHotkey_mouse_scripts)你有简单的[自动热键](https://autohotkey.com/)脚本，它将通过使用鼠标执行一些动作。

我为自己制作了这些脚本，以便能够在一个没有物理键盘但连接了鼠标的平板电脑上玩一些游戏。这样我就可以退出游戏，显示它们的菜单，显示一个屏幕键盘(遗憾的是，当一些游戏以全屏模式运行时，这个键盘不能很好地工作)等等。只需使用鼠标。

希望它们对其他人也有用，所以我决定分享它们。

它们在 32 位和 64 位版本的 Microsoft Windows 10(平板电脑和台式机)以及 32 位版本的 Microsoft Windows XP(带 Service Pack 1)和 Microsoft Windows 7 上进行了测试。如果在尝试 binariy (32 位版本和 64 位版本，如果有的话)后，它在您的 Windows 版本上不工作，那么您可以尝试从提供的源代码编译它。请记住，并非所有 Windows 版本都有“tabtip.exe”或“osk.exe”文件。

他们可以在 GitHub 上找到:[https://github.com/jalbam/AutoHotkey_mouse_scripts](https://github.com/jalbam/AutoHotkey_mouse_scripts)

## 收录脚本

**press ESC _ 32**–当鼠标滚轮按钮(中间按钮)被按下时，会按下“ESC”(*ESCAPE*)键。针对 32 位编译，但也应该可以在 64 位版本的 Microsoft Windows 上运行。

**按 F5 _ 32**–当鼠标滚轮按钮(中间按钮)被按下时，会按下“F5”键。针对 32 位编译，但也应该可以在 64 位版本的 Microsoft Windows 上运行。

**按 F10 _ 32**–当鼠标滚轮按钮(中间按钮)被按下时，会按下“F10”键。针对 32 位编译，但也应该可以在 64 位版本的 Microsoft Windows 上运行。

**showOSK _ 32**–如果操作系统中有，当按下鼠标滚轮按钮(中间的按钮)时，将显示属于“屏幕键盘”的“OSK”(*)(“浮动”在屏幕周围，作为一个独立的窗口)。为 32 位编译。*

 ***showOSK _ 64**–与“showOSK_32”相同，但适用于 64 位版本的 Microsoft Windows。在使用“showOSK_32”版本时，如果您得到一个错误[，请使用它。](https://autohotkey.com/board/topic/119602-the-system-cannot-find-the-file-specified/)

**show tabstip _ 32**–如果操作系统中有，当按下鼠标滚轮按钮(中间按钮)时，它将显示“tabstip”(【tabtip.exe】T3)，这也属于屏幕键盘(不同于“osk.exe”键盘)。为 32 位编译。

**showtab tip _ 64**–与“showTabTip_32”相同，但适用于 64 位版本的 Microsoft Windows。在使用“showTabTip_32”版本时，如果您得到一个错误[，请使用它。](https://autohotkey.com/board/topic/119602-the-system-cannot-find-the-file-specified/)

## 如何运行

要使用已经编译好的二进制文件，只需像其他普通程序一样运行它们。当它们运行时，任务栏中会显示一个图标。

请注意，如果需要，可以同时运行多个脚本进行组合。

## 从源代码编译

除了 Microsoft Windows 的二进制文件之外，源代码也包括在内，因此可以很容易地对它们进行修改，以编辑所执行的操作、增强功能等。

为了从源代码编译，将需要[自动热键](https://autohotkey.com/)软件。

## 执照

这个项目可以用于任何非商业目的，但始终保持作者的名字和版权条款的自由复制，分发和修改。除此之外，这个项目你想怎么用就怎么用，千万不要卖！*