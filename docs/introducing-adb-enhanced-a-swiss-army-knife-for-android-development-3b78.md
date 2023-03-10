# 介绍 ADB-enhanced:Android 开发的瑞士军刀

> 原文：<https://dev.to/ashishb/introducing-adb-enhanced-a-swiss-army-knife-for-android-development-3b78>

Android 开发需要大量不相关的开发和测试方法。考虑一些场景

1.  要测试运行时权限——进入设置->应用程序->你要找的应用程序的应用程序信息，并禁用该权限。
2.  测试全新安装-`adb shell pm clear-data com.example`
3.  要在省电模式下测试您的应用程序，请通过展开通知栏来打开省电模式
4.  要停止应用程序的执行，请通过活动管理器`adb shell am kill com.example`将其终止
5.  在瞌睡模式下测试你的应用——首先，通过`adb shell dumpsys battery unplug`让设备认为它被拔掉了，然后，通过`adb shell dumpsys battery set status 3`让它认为它正在放电，然后通过`adb shell dumpsys deviceidle force-idle`启用瞌睡模式。当你完成将设备恢复到正常状态时，不要忘记执行一组不相关的补充命令。
6.  要查看应用程序的透支情况，请转到开发者选项，在那里启用/禁用它。

随着时间的推移，这变成了一个巨大的精神负担，我首先在一个文本文件中编写了一些这样的流，然后将它们转换成自动化的 shell 脚本。但是当我觉得这还不够时，我为自己创造了一个工具，叫做 adb-enhanced。
工作原理:
首先安装工具。我是用 Python 写的，所以，如果下面的命令不起作用，就安装 Python

`pip3 install adb-enhanced # I would discourage Python 2 based install at this point`

现在，让我们再来看看这个工具的使用案例:

1.  要测试运行时权限:

```
# Use grant instead of revoke to grant the permission
adbe permission revoke com.example camera  # See all possible such permissions via "adbe -h"

# Use grant instead of revoke to grant the permission
adbe permission revoke com.example camera  # See all possible such permissions via "adbe -h" 
```

1.  测试全新安装–

```
# Unlike adb shell pm clear-data com.example, this command will 
# produce an error if com.example is not installed 
# which is good for catching typos
adbe clear-data com.example
# Unlike adb shell pm clear-data com.example, this command will 
# produce an error if com.example is not installed 
# which is good for catching typos
adbe clear-data com.example 
```

1.  要在省电模式下测试您的应用程序-

```
# As you would guess, use "off" to turn the battery saver off
adbe battery saver on
# As you would guess, use "off" to turn the battery saver off
adbe battery saver on 
```

1.  要停止应用程序的执行-

```
# For a more aggressive kill, try adbe force-stop com.example
adbe stop com.example 
```

1.  在瞌睡模式下测试你的应用

```
adbe doze on # Use "off" to turn the doze mode off 
```

1.  要查看应用程序的透支额

```
adbe overdraw on 
```

我在 https://github.com/ashishb/adb-enhanced 开源了代码。查看 GitHub 资源库，了解所有这些工具的功能。欢迎反馈和拉动请求。