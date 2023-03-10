# 将 Android Studio 虚拟设备移动到 Mac 上的另一个硬盘上

> 原文：<https://dev.to/oscherler/move-android-studio-virtual-devices-on-a-mac-5ej5>

假设您想通过移动 Android Studio 虚拟设备(在 Android 模拟器中运行以测试应用程序的手机和平板电脑)来回收 Mac 主硬盘上的空间。有可能，但是如果你像我一样幸运，你可能会遇到一些障碍。

很容易[发现](https://developer.android.com/studio/command-line/variables)您应该修改`ANDROID_SDK_ROOT`、`ANDROID_EMULATOR_HOME`或`ANDROID_AVD_HOME`环境变量(或者甚至`ANDROID_HOME`，如果您不幸发现一些确实过时的文档)。但是，如果你试图在 Android Studio 首选项中的<u>外观&行为>路径变量</u>下设置它们，因为你认为这是一件合理的事情，那么你可能会浪费很多时间，因为它不起作用。当然，数以千计的关于如何在 Windows 中做到这一点的文档没有任何帮助，少数关于 Mac 的文档已经非常非常过时了。

这里我们想要实现的是定义`ANDROID_AVD_HOME`环境变量，使它对从 Finder 启动的应用程序可用。从 Mac OS 10.10 开始(至少工作到 10.14，在撰写本文时)，可以用`launchctl setenv` :
完成

```
launchctl setenv ANDROID_AVD_HOME /Volumes/LargeHDD/Android/avd 
```

Enter fullscreen mode Exit fullscreen mode

要在不启动 Android Studio 的情况下测试它，您可以使用苹果的脚本编辑器并运行这个脚本(但是，请记住，您必须在调用`launchctl setenv`之后重新启动脚本编辑器，以便它能够获取更改):

```
do shell script  "echo $ANDROID_AVD_HOME" 
```

Enter fullscreen mode Exit fullscreen mode

现在是将您的 Android 虚拟设备(AVD)文件从`~/.android/avd`移动到新位置(例如`/Volumes/LargeHDD/Android/avd`)的好时机。

**不过**，你还需要更新那个文件夹里的`.ini`文件，因为它们引用了旧位置的绝对路径，如果你不更新，Android Studio 会抱怨 INI 文件损坏。例如，如果您在新的`/Volumes/LargeHDD/Android/avd`文件夹中有一个文件`Nexus_5X_API_26.ini`，从
编辑它(`path`属性)

```
avd.ini.encoding=UTF-8
path=/Users/olivier/.android/avd/Nexus_5X_API_26.avd
path.rel=avd/Nexus_5X_API_26.avd
target=android-26 
```

Enter fullscreen mode Exit fullscreen mode

至

```
avd.ini.encoding=UTF-8
path=/Volumes/LargeHDD/Android/avd/Nexus_5X_API_26.avd
path.rel=avd/Nexus_5X_API_26.avd
target=android-26 
```

Enter fullscreen mode Exit fullscreen mode

您现在可以启动 Android Studio 并打开 AVD 管理器来查看您的设备是否还在，但是我们仍然需要确保您每次登录时都设置了这个环境变量。为此，我们将创建一个脚本，并在登录时使用 Launchd 启动它。

在`/Users/[YOUR_USER_NAME]/.android_studio_env_vars`创建以下脚本:

```
export ANDROID_AVD_HOME='/Volumes/LargeHDD/Android/avd'

launchctl setenv ANDROID_AVD_HOME "$ANDROID_AVD_HOME" 
```

Enter fullscreen mode Exit fullscreen mode

然后在`~/Library/LaunchAgents/user.AndroidStudioEnvVars.plist`创建下面的 PList 文件(您可以随意命名):

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>user.AndroidStudioEnvVars</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>/Users/[YOUR_USER_NAME]/.android_studio_env_vars</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
</dict>
</plist> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以只从 PList 中执行`launchctl setenv ANDROID_AVD_HOME /Volumes/LargeHDD/Android/avd`,但是脚本允许我们定义其他变量，比如需要的话定义`ANDROID_SDK_ROOT`。

最后，用:
加载 PList

```
launchctl load ~/Library/LaunchAgents/user.AndroidStudioEnvVars.plist 
```

Enter fullscreen mode Exit fullscreen mode

下次登录或重启时，应该会自动设置环境变量，您可以使用
进行检查

```
launchctl getenv ANDROID_SDK_ROOT
# -> /Volumes/LargeHDD/Android/sdk 
```

Enter fullscreen mode Exit fullscreen mode