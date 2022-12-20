# 在 Android 中显示/隐藏布局边界

> 原文：<https://dev.to/varunbarad/show-hide-layout-bounds-in-android-59b8>

有时在 Android 中工作时，我们需要在设备上显示布局边界。最近的 Android 版本确实提供了这样做的动作。但在以前的版本中，我们必须深入设置应用程序。我希望能够从我的计算机上执行切换。

ADB 确实提供了这样做的方法，但是这些命令不容易记住，而且过一段时间后会变得令人厌烦。

```
# We need to execute both lines to
# make the layout-bounds show
adb shell setprop debug.layout true adb shell service call activity 1599295570 
```

所以我创建了两个名为`show-lb.sh`和`hide-lb.sh`的文件，它们分别显示和隐藏我们的测试设备上的布局边界。

这些文件如下:

`show-lb.sh`

```
adb shell setprop debug.layout true > /dev/null 2>&1
adb shell service call activity 1599295570 > /dev/null 2>&1 
```

`hide-lb.sh`

```
adb shell setprop debug.layout false > /dev/null 2>&1
adb shell service call activity 1599295570 > /dev/null 2>&1 
```

每一行末尾的那个`> /dev/null 2>&1`部分只是为了让那个特定命令的输出不会显示在我们的终端上，因为我们不需要它。

`Note for Windows users:`您的朋友需要省略`> /dev/null 2>&1`并将这些文件保存为`show-lb.bat`和`hide-lb.bat`

您可以保存这些文件(如果您在基于 unix 的机器上，请确保给予它们`executable`权限)，然后将它们的位置添加到系统的`PATH`变量中。这样你可以在任何地方执行这些。

我真的认为我将来会做一个版本，它会检查当前状态并自动切换。

## 这就是所有的乡亲

这是我将日常工作经验提高 1%的解决方案。如果你有更多这样的想法/例子或任何其他建议，请[联系我](https://varunbarad.com/contact)或发推特给我 [@varun_barad](https://twitter.com/varun_barad) 。