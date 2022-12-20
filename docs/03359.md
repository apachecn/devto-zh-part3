# 如何触发任何行动时，一个文件或文件夹的变化对 Macos 的廉价

> 原文：<https://dev.to/mayeu/how-to-trigger-any-action-when-a-file-or-folder-changes-on-macos-on-the-cheap-3dcp>

当一个文件改变时，你曾经想要触发一个动作吗？比如自动移动一个刚下载的文件？动作可以是一个脚本，应用程序，任何东西。在这里，我将描述如何在 macOS 上使用默认系统提供的工具来实现这一点。

补充说明:我知道有一些图形用户界面应用程序可以用更简单的方式做到这一点；在这里，我只想强调如何用 macOS 提供给我们的东西来手动操作。

但是在我们开始之前，我想强调一个事实，这种方法可能对任何关键的事情都不理想。引用文件:

> ⚠️重要提示:非常不鼓励使用这个键，因为文件系统事件监视非常容易发生争用，并且完全有可能错过修改。当捕获到修改时，无法保证文件在作业启动时处于一致状态。

这基本上意味着:

*   如果一次有太多的变化发生，一些可能会被错过，你的行动不会触发
*   您无法确定您的操作是否会找到处于触发脚本的确切状态的文件

但是我相信，对于日常工作流来说，计算要求不高，这些警告并不是真正的问题。

# 如何观察变化？

为此，我们将使用运行在 macOS 上的[服务管理器`launchd`。它有两个主要任务:](https://en.wikipedia.org/wiki/Launchd)

*   引导系统
*   管理守护程序和代理

在行话中，代理是基于每个用户运行的服务，守护进程是系统服务。

`launchd`有多种级别的配置，有些是系统级和特权级的，但您也可以使用它来运行无特权的用户任务。您可以在五个文件夹中找到这些定义:

*   `~/Library/LaunchAgents`:用户提供的每用户代理。
*   `/Library/LaunchAgents`:管理员提供的每用户代理。
*   `/Library/LaunchDaemons`:管理员提供的全系统守护进程。
*   `/System/Library/LaunchAgents`:苹果公司提供的每用户代理。
*   苹果公司提供的全系统守护程序。

在我们的例子中，我们将使用一个小的`launchd`代理，它将被存储在`~/Library/LaunchAgents`中。我们将在后面看到如何加载或卸载代理来激活或停用它。

# 测试设置

让我们采取一些行动，进入您的终端，进入一个干净的文件夹进行测试。从这里开始，我将假设您使用的文件夹是`~/file-watching-test`，如果您使用的是另一个文件夹，请不要忘记在本文的其余部分调整您的路径！

```
$ mkdir ~/file-watching-test
$ cd ~/file-watching-test 
```

在这个文件夹中，我们将创建一个由`launchd` :
监控的空`watched`文件

```
$ touch ~/file-watching-test/watched 
```

我们还将创建一个脚本，当被监视的文件改变时将调用该脚本，在您最喜欢的编辑器中打开`~/file-watching-test/script.sh`文件并添加:

```
#!/bin/sh
echo "$(date): 🐈 I has be summoned" >> ~/file-watching-test/result 
```

让我们来看看这里发生了什么:

*   `echo "$(date): 🐈 I has be summoned"`:
    *   该回显命令将获得通过`$()`执行的日期命令的返回
    *   将`<current date>: 🐈 I has be summoned`打印到标准输出。

您可以直接在您的 shell 中尝试一下，看看发生了什么。然后:

*   这是一个重定向操作符。这个函数获取打印在标准输出上的任何内容(因此没有错误，如果有的话),并**将**添加到它指向的文件中。有了它，我们可以记录我们脚本的所有执行。
*   最后，`~/file-watching-test/result`是我们的结果文件，它将接收我们的`echo`命令的输出。

现在，确保脚本可以用`chmod +x ~/file-watching-test/script.sh`执行。

综上所述，我们有以下层次:

```
$ tree file-watching-test
file-watching-test
├── script.sh      # Our script
├── watched        # The file we are watching 
```

# 为`launchd`创建服务文件

我们希望我们的测试服务能够:

*   观看`~/file-watching-test/watched`文件
*   当变化发生时，我们想执行`~/file-watching-test/script.sh`

在我向您展示服务文件之前，请注意`launchd`使用 XML 进行服务声明，所以请做好准备。

这里是我们的`me.mayeu.watchtest.plist`文件:

```
<?xml version=“1.0” encoding=“UTF-8”?>
<!DOCTYPE plist PUBLIC “-//Apple//DTD PLIST 1.0//EN” “http://www.apple.com/DTDs/PropertyList-1.0.dtd”>
<plist version=“1.0”>
<dict>
        <key>Label</key>
        <string>me.mayeu.watchtest</string>
        <key>ProgramArguments</key>
        <array>
                <string>/Users/m/file-watching-test/script.sh</string>
        </array>
        <key>WatchPaths</key>
        <array>
                <string>/Users/m/file-watching-test/watched</string>
        </array>
</dict>
</plist> 
```

好了，让我们把它分解一下，一定要让不同的路径适应你正在使用的路径！如果您不确定，当您在`file-watching-test`文件夹中时，在您的 shell 中使用`pwd`命令来打印文件夹的绝对路径。

第一部分并不真正有趣，因为它是格式和文档类型定义(DTD)的声明

```
<?xml version=“1.0” encoding=“UTF-8”?>
<!DOCTYPE plist PUBLIC “-//Apple//DTD PLIST 1.0//EN” “http://www.apple.com/DTDs/PropertyList-1.0.dtd”> 
```

然后，我们声明该文件使用的是 1.0 版本的`plist`格式。`plist`是用于在 macOS 中存储配置、服务、序列化对象等的`Property List`文件的命名。我们还声明这个`plist`包含一个字典(`dict` ):

```
<plist version=“1.0”>
<dict>
...
</dict>
</plist> 
```

在这些键中，我们将声明我们的作业。首先，我们必须提供一个唯一的标签来标识工作。这里我用`me.mayeu.watchtest` :

```
<key>Label</key>
<string>me.mayeu.watchtest</string> 
```

按照惯例(不仅如此，我们也不需要深究)，我们使用反向域名，后跟一些代表应用程序的名称。该 ID 还用于标识应用程序和其他相关资源。

比如 Evernote 就用`com.evernote.Evernote`作为应用 ID。

然后，我们使用`ProgramArguments`键:
声明我们将要运行的程序

```
<key>ProgramArguments</key>
<array>
    <string>/Users/m/file-watching-test/script.sh</string>
</array> 
```

这个键接受一个列出所有参数的数组。在我们的例子中，它只是脚本的路径，但是如果我们想执行`git commit -m “My commit message”`，我们应该这样做:

```
<key>ProgramArguments</key>
<array>
    <string>/usr/bin/git</string>
    <string>commit</string>
    <string>-m</string>
    <string>My commit message</string>
</array> 
```

最后，我们声明何时运行程序，在这种情况下，我们使用`WatchPaths`键，该键接受一个要监视的路径数组:

```
 <key>WatchPaths</key>
 <array>
    <string>/Users/m/file-watching-test/watched</string>
 </array> 
```

搞定了。我们有了新的服务。

如果你想知道`launchd`能做什么，你可以在`launchd.plist`手册页中找到所有有效的密匙:`man 5 launchd.plist`。`launchd`也应该用于基于时间的工作，而不是使用`cron`。参见[苹果文档](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/ScheduledJobs.html#//apple_ref/doc/uid/10000172i-CH1-SW2)了解更多相关信息。

# 触发它！

快到了！我们已经有了所有需要的部分，所以让我们将服务文件复制到正确的位置:

```
$ cp me.mayeu.watchtest ~/Library/LaunchAgents/ 
```

我们现在将告诉`launchd`加载它:

```
$ launchctl load ~/Library/LaunchAgents/me.mayeu.watchtest 
```

现在`launchd`已经加载了我们的服务，我们可以通过在其中添加内容来更改我们关注的文件:

```
$ echo ‘trigger the watcher’ > test/watched` 
```

这应该触发了脚本；这样，结果文件中就会有一些内容:

```
$ cat ~/file-watching-test/result
Thu Apr 11 12:39:41 +07 2019: 🐈 I has be summoned 
```

🎉

如果我们再次触发它，我们应该会看到新的一行:

```
$ echo ‘trigger the watcher’ > test/watched`
$ cat ~/file-watching-test/result
Thu Apr 11 12:39:41 +07 2019: 🐈 I has be summoned
Thu Apr 11 12:42:55 +07 2019: 🐈 I has be summoned 
```

我们完成了这个测试！为了清洁我们的身体，我们将:

*   卸载服务
*   删除服务`plist`
*   删除我们的测试文件夹

```
$ launchctl unload ~/Library/LaunchAgents/me.mayeu.watchtest
$ rm -rf ~/Library/LaunchAgents/me.mayeu.watchtest
$ rm -rf ~/file-watching-test 
```

就是这个！您现在已经有了一些使用`launchd`的基础，并且可能同时学到了一两件关于您的 shell 和 shell 脚本的事情🙂

最后一点，你也可以用这种方法观察文件夹，从而对这些文件夹下发生的任何变化采取行动，可能是删除的文件，新文件，新文件夹，等等。

玩得开心！如果你用这个做了一些很酷的东西，不要犹豫，在推特上或者发邮件给我:T2，我是 mayeu，我是 T3。

*本文最初发表于 [mayeu.me](https://mayeu.me/post/how-to-trigger-any-action-when-a-file-or-folder-changes-on-macos-on-the-cheap/) 。*