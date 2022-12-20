# 解决 AMPPS 上的工艺设置问题的方法

> 原文：<https://dev.to/davinaleong/resolutions-to-setup-issues-with-craft-on-ampps-2of1>

这是我关于**工艺介绍**的帖子的后续。

[![davinaleong](img/6bbb66b1fe2e3e8581ac733da5da702f.png)](/davinaleong) [## 工艺介绍

### davina Leong 1918 年 1 月 18 日 4 分钟阅读

#php #craft #learning](/davinaleong/introduction-to-craftcms-1n9a)

注意这个*不是*的结论性列表。这很简单，我列出了我所面临的问题，以及我如何解决它们。

# 答:让 Craft 在 Mac 上运行 AMPPS

*   在您的主目录中创建一个`/developer`文件夹。
*   在`/developer`中为你的工艺项目创建一个文件夹；例如(`/my-craft-project`)
*   手动或者通过[作曲](https://docs.craftcms.com/v3/installation.html)将 Craft 安装到那个文件夹中。
*   打开你的**终端**和`cd`到你的工艺项目的`/web`文件夹，输入:

```
pwd | pbcopy 
```

Enter fullscreen mode Exit fullscreen mode

该命令将当前目录的路径复制到剪贴板中。注意:如果您没有收到任何成功消息，请不要惊慌。

*   导航到您的 **AMPPS 的别名管理器**并点击**添加新的**T4】
*   将您在步骤(4)中复制的路径粘贴到**路径**字段中。填写后的表单应该是这样的:![AMPPS add new alias](img/7acf3ef8b96967a32c32a9c849eeba44.png)点击**创建别名**
*   要进行测试，请确保 AMPPS 正在运行，然后导航至`http://localhost/my-craft-project`。你可以看到欢迎页面，或者你的主页，如果你设置了的话。

* * *

# B:插件存储加载失败

*   下载`[cacert.pem](https://curl.haxx.se/ca/cacert.pem)`并将其移动到(A)中的`/developer`文件夹。-.在 AMPPS 控制台中，点击 PHP 的**齿轮**图标。然后点击旁边的**扳手**图标，启动`php.ini`。
*   搜索`curl.info`。您将看到类似这样的内容:`:curl.info =`用下面的代码替换那一行:

```
curl.cainfo = "/Users/<username>/developer/cacert.pem" 
```

Enter fullscreen mode Exit fullscreen mode

记得把`<username>`改成你的**主目录**的名字。

*   重启你的 Apache 服务器并重新加载你的网页

* * *