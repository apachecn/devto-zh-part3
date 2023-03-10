# 你应该知道的 Xcode 调试技术

> 原文：<https://dev.to/logicxd/xcode-debugging-techniques-you-should-know-4jde>

无论您是在寻找 bug、弄清楚代码是如何流动的，还是在试验您的新功能，知道如何使用调试器都将为您节省大量时间。我基本上每次运行应用程序时都会使用调试器。它也是学习编程如何工作的一个很好的工具。我只是希望学校在我早期的计算机科学初级班里教我这些。相反，我是在两年后第一次实习时第一次被介绍到这里的。

这些是我最常用的一些调试技术。我强烈推荐通读 [objc.io 的调试技巧](https://www.objc.io/issues/19-debugging/lldb-debugging/)以获得更全面的阅读。

## 基础知识

基础知识的快速概述。你需要知道这个。句号。

*   单步执行-过度执行当前行，并在执行下一行之前停止。
*   单步执行-在当前行进入方法内部。如果它已经在运行，那么执行完当前行，在执行下一行之前停止。
*   步出-完成当前块的执行并返回给调用者。
*   条件断点-仅当断点满足条件时才命中断点。
*   自动继续断点-执行断点中的表达式，然后自动继续。
*   `po` -打印出变量的详细信息。`po myVariable`。
*   `e`、`expr`、`expression`——执行命令。`e myVariable = @"new"`。
*   `e UIView *$view = (UIView *)0x0000` -调试时创建变量。

## 所有目标-C 异常断点

在撞车前抓住它。这个异常断点是一个重要的救命稻草。当有异常抛出时，应用程序将崩溃，并在 Xcode 输出中给出错误堆栈跟踪。启用此选项后，在应用程序崩溃之前，您将在该行上遇到一个断点。然后你可以看看你的变量，看看到底是什么导致了崩溃。

要打开它:

*   打开导航器
*   单击“断点导航器”选项卡
*   单击底部的“+”号创建一个新的“异常断点”

仅此而已！但是您可以做更多的事情来获得最大收益:

*   当断点命中时，设置它以便异常将打印出运行这个命令的错误消息:`po $arg1`。
*   仅在“目标-C”上设置例外。如果您不想看到任何 C++异常，这将忽略它们。
*   右键单击并将断点移动到用户，以在所有项目中保留此断点！

## 从一个方法返回

例子:`thread return`、`thread return NO`、`thread return myVariable`。

伟大的存根方法。从具有所需值的方法中快速返回。假设您想测试这个可以返回多个值的方法。不用改变值，重新编译并运行它，你可以在这里放一个断点并返回你想要的值！

## 跳过/跳转正在执行的行

例子:`thread jump --by 2`，`thread jump --line 102`

当我想跳过某个正在执行的东西时，我使用这个。虽然这并不总是完美的工作，因为 LLDB 不太喜欢跳过一些行。另外，Xcode IDE 不会知道你跳过了一行，直到你跳到下一行。

## 进口套口

示例:`e @import UIKit`

您遇到了一个断点，并且想要通过调用`po view.bounds`来打印视图的边界。您可能得到了一些垃圾输出，而不是视图的边界，比如:

*   错误:未知类型的不支持的表达式
*   错误:“bounds”具有未知的返回类型；将调用强制转换为其声明的返回类型

要修复它，您可能已经通过调用它作为一个方法来调用它，并且还通过做`po (CGRect)[self bounds]`来指定返回类型。好吧，不用每次你想打印东西的时候都输入那些乱七八糟的东西，你只需要做`e @import UIKit`就可以了，你将被允许做`po view.bounds`和 UIKit 附带的所有其他方法。

## 更新 UI 变化

例子:`e (void)[CATransaction flush]`、`caflush`(用[凿子装好](https://github.com/facebook/chisel))

非常适合动态测试 UI 更改，无需在每次更改后重启。

## 一个视图的快照

示例:`visualize myView`(用[凿子安装](https://github.com/facebook/chisel))

想知道风景是什么样的？只需运行此命令，它将在 Preview.app 上打开您的视图的快照图像。

* * *

我并不总是使用所有这些技术，但它们比你可能见过的其他技术使用得更频繁。当我的偏好改变或我学到新的东西时，我会更新这些。让我知道我是否应该使用一些东西，它从这个列表中消失了。