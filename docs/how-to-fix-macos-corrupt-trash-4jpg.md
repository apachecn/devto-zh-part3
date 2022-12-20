# 如何修复 macOS 损坏的垃圾

> 原文：<https://dev.to/codebrotha/how-to-fix-macos-corrupt-trash-4jpg>

在一个典型的工作日，我倾向于在我的桌面上积累临时文件。这些可能是占位符图像或我为以后编写的代码片段，或者是从 Stack Overflow“借用”的...

[![](img/09df05446019ff1c9487d2cd292d20a0.png)](https://i.giphy.com/media/XreQmk7ETCak0/giphy.gif)

每隔几个小时，我就会瞥见桌面上的混乱，我需要看到一个干净的桌面，这迫使我去清理它。看起来很简单，扔掉我不需要的文件，然后继续编码。

除非事情没那么简单，我系统的垃圾已经损坏，显示了上面图片中的信息。

无论何时出现此问题，以下是解决方法:

在终端中输入以下命令，一次一行。(第一行将提示您输入密码。)

```
sudo rm -rf ~/.Trash
sudo rm -rf /Volumes/*/.Trashes
mkdir ~/.Trash
sudo chown $UID ~/.Trash
chmod u+rwx ~/.Trash 
```

Enter fullscreen mode Exit fullscreen mode

当您输入完这些命令后，请退出“终端”。您的垃圾现在应该被修复了。将文件移到废纸篓，以确保它现在可以正常工作。然后清空垃圾以确保它也能工作。

**Pro 提示:**创建别名，让你的生活更轻松！(苹果工程师似乎无法解决这个问题，如果你的系统受到影响，这种情况会反复发生)。

如果你不知道如何创建一个 Bash Shell 别名， **[这篇文章](https://codeburst.io/how-to-create-shortcut-commands-in-the-terminal-for-your-mac-9e016e25e4d7)** 解释了它。

下面是我在 MacBook Pro 上使用的别名:

```
alias fixTrash='sudo rm -rf ~/.Trash && sudo rm -rf /Volumes/*/.Trashes && mkdir ~/.Trash && sudo chown $UID ~/.Trash && chmod u+rwx ~/.Trash && echo "Trash has been reset."' 
```

Enter fullscreen mode Exit fullscreen mode

**根据您 Mac 的权限，您可能需要暂时禁用系统完整性保护(SIP ),以下是必要时的操作步骤:**

关闭您的 Mac。

按下电源按钮，然后快速按住 command + R 按钮一起启动到恢复模式。

一旦您的 Mac 启动到恢复模式，从菜单栏中选择实用工具，选择终端并输入以下命令:

```
csrutil status 
```

Enter fullscreen mode Exit fullscreen mode

该命令将返回“系统完整性保护状态:已启用”的响应或“系统完整性保护状态:禁用”

如果启用，在终端中键入以下命令:

```
csrutil disable 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到一条消息，说明“已成功禁用系统完整性保护”

关闭“终端”并重新启动 Mac。

要重新启用 SIP，请像之前一样回到恢复模式，并在终端中键入以下命令:

```
csrutil enable 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到一条消息，说明“已成功启用系统完整性保护”

重新启动您的 Mac。

**如果问题仍然存在，您可能需要重建您的用户帐户:**

1.  登录另一个管理员用户。(如果不存在其他管理员用户，请创建一个。)
2.  从“系统偏好设置”>“用户和群组”中删除您的主用户。
3.  请务必选择“保留个人文件夹的副本”选项。
4.  打开 Finder>“前往”>“电脑”>“Macintosh”>“用户”>“已删除的用户”,从用户名中删除“已删除的”,然后移回“用户”文件夹。
5.  在“用户和群组”中重新创建用户，确保使用相同的用户名(个人文件夹名称)和密码。(如果是管理员，请选择管理员)。
6.  创建用户&当出现提示时，选择“使用现有文件夹”选项。
7.  重新启动并登录到您的原始用户。