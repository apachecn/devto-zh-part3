# 如何在 Mac 电脑上设置 20 分钟的电脑休息时间

> 原文：<https://dev.to/yawaramin/how-to-set-up-a-20-minute-computer-break-on-a-mac-bfo>

你是否一直打算开始遵循眼科医生告诉你的 [20-20-20 法则](https://opto.ca/health-library/the-20-20-20-rule)？这里有一个设置 Mac 的教程，提醒你每二十分钟休息一次。它包括以下内容:

*   显示通知的简单 shell 脚本
*   一个 Mac OS [launchd](http://www.launchd.info/) 代理，用于设置重复提醒
*   初始化代理的命令。

一、shell 脚本:

```
# ~/scripts/twenty.sh
osascript -e 'display notification "Take a break!" with title "20-20-20 Rule"' 
```

这使用 Mac 的内置通知系统来显示消息，提醒您休息一下。

接下来，启动代理(这必须保存在`~/Library/LaunchAgents/twenty.plist` ):

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>twenty</string>

    <key>ProgramArguments</key>
    <array>
        <string>sh</string>
        <string>/Users/YOURACCOUNT/scripts/twenty.sh</string>
    </array>

    <key>StartInterval</key>
    <integer>1200</integer>
</dict>
</plist> 
```

(记得把`YOURACCOUNT`改成自己的实际用户账号！)

和命令:

```
$ launchctl load ~/Library/LaunchAgents/twenty.plist 
$ launchctl start twenty 
```

瞧，现在你的 Mac 上有了一个自动提醒，让你休息 20 分钟，帮助减轻眼睛疲劳。

如果您想要停止代理:

```
$ launchctl stop twenty 
```

如果您想要更改代理设置，您还需要*卸载*代理，然后重新加载并重新启动它以使更改生效:

```
$ launchctl unload ~/Library/LaunchAgents/twenty.plist 
```

这应该可以移植到其他操作系统:

*   Linux:用 [notify-send](http://manpages.ubuntu.com/manpages/focal/en/man1/notify-send.1.html) 显示桌面通知，用 [systemd timer](https://www.freedesktop.org/software/systemd/man/systemd.timer.html) 管理通知时间表
*   Windows:用 [Windows 脚本主机](https://stackoverflow.com/questions/3106806/how-to-show-a-popup-without-a-browser)显示桌面通知，用[任务调度器](https://docs.microsoft.com/en-us/windows/desktop/taskschd/task-scheduler-start-page)管理日程。