# Go 中的一个简单的 Slack Bot 托管

> 原文：<https://dev.to/shindakun/a-simple-slack-bot-in-go---the-hosting-558l>

# 赞美太阳

## 真正的*黑暗灵魂*懈怠机器人在这里继续

上次我们组装了一个非常简单的 Slack 机器人，每当它看到测试“*黑暗灵魂*”时，它都会以“赞美太阳”作为回应。这一次我们将讨论如何举办这次活动。请注意，我可能会忽略一些步骤——这是因为不同的操作系统有不同的步骤。如果你遇到任何问题，快速谷歌应该会让你回到正轨。

我们有一堆不同的主机选择，其中一些包括[数字海洋](https://m.do.co/c/fa6899c60e6c)(附属链接) [AWS](https://aws.amazon.com/) 、 [Azure](https://azure.microsoft.com/) 、[谷歌计算引擎](https://cloud.google.com/compute/)或者任何你可以选择的东西。在本文中，我们将使用 GCE——不像其他选择那样简单，但是使用一个小实例应该可以免费托管我们的 bot。对于主机来说，没有什么错误的选择，使用任何对你有用的东西——我使用 GCE 只是因为我最近经常使用它。

### 谷歌云托管

首先，如果你还没有帐户，注册谷歌云。新帐户将获得 300 美元的信用，有效期长达一年，但你必须有一张信用卡来激活帐户，所以请记住这一点。我们将在 UI 中做一些设置来开始。

让我们在[https://console.cloud.google.com/projectcreate](https://console.cloud.google.com/projectcreate)创建一个新项目。我给我的取名为`golang-slackbot`。

[![Google Cloud Console](img/243671264c7f180e4325639ca71aa7fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tmflzy1h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1v1o153ubnu01blc7gz9.png)

现在让我们创建一个新的虚拟机实例。一旦你创建并选择了你的新项目，你可以访问[https://console.cloud.google.com/compute/instances](https://console.cloud.google.com/compute/instances)创建一个新的虚拟机。GCE 可能需要几分钟才能在你的账户上建立起来——如果你不得不等的话，去给自己倒一品脱或喝点茶。

[![GCE](img/2907cc59670fc2aa2dda5d3f0208d8ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oIJBy5W9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nao5enqnn7hn7nyzvyet.png)

正如你在我上面的截图中看到的，我选择了使用一个运行在区域`us-west1`和区域`us-west1-a`的`Debian GNU/Linux 9 (stretch)`的`micro`实例。您可能希望选择一个离您更近的地区和区域，记下区域和项目名称，稍后会用到它们。我建议坚持使用微实例，因为在你的 300 美元信用用完之后，你可以每月免费运行一个实例。我只选择了 Debian，因为它是默认选择。其他一切保持不变，点击底部的“创建”。

### 谷歌云 SDK

同样，创建虚拟机需要一两分钟的时间。现在是安装 Google Cloud SDK 的好时机。安装文档非常好，所以我将把安装留给读者作为练习。

### 建筑

我们越来越接近了！现在，我们的虚拟机应该已经启动并运行了。现在，无论 Slack bot 源代码位于本地计算机的什么位置，您都需要打开一个命令提示符。在那里，我们将构建一个 Linux 可执行文件。我通常会使用`-ldflags="-s -w"`来获得稍微小一点的可执行文件。反正我不会调试这个，所以[剥离](https://en.wikipedia.org/wiki/Strip_(Unix))没什么大不了的，如果你愿意，就把`ldflags`去掉。在这里你可以看到我们希望生成的文件被命名为`dsbot`。

```
$ GOOS=linux go build -ldflags="-s -w" -o dsbot main.go 
```

Enter fullscreen mode Exit fullscreen mode

### 正在部署

我称这个阶段为部署阶段，但它不是真的-至少在 DevOps 的意义上不是。所有这些在某种程度上至少是自动化的。我发现最好是先把你的头缠在更手动的步骤上，以便很好地理解正在发生的事情。无论如何，现在我们将回到谷歌云 SDK。它可以做很多事情，但我们应该只需要关注几个命令。首先，我们要确保我们的计算区域已设置好——我使用的是`us-west1-a`,所以我在配置中进行了设置，使用您在创建虚拟机时选择的区域。

```
$ gcloud config set compute/zone us-west1-a 
```

Enter fullscreen mode Exit fullscreen mode

我们还将设置项目，再次使用您在上面选择的项目名称。

```
$ gcloud config set project golang-slackbot 
```

Enter fullscreen mode Exit fullscreen mode

要将或 Linux 可执行文件转移到虚拟机，我们需要 SCP(安全拷贝)该文件。命令分解成这样`scp <local filename> <remote instance>:<remote filename>`。我们应该还在编译 bot 的目录中，所以我们运行下面的代码。

```
$ gcloud compute scp ./dsbot slackbot:dsbot 
```

Enter fullscreen mode Exit fullscreen mode

咻！终点在望！现在让 SSH 进入我们的虚拟机。

```
$ gcloud compute ssh slackbot 
```

Enter fullscreen mode Exit fullscreen mode

你可以像我们在上一篇文章中那样用`SLACKTOKEN=slacktoken dsbot`运行可执行文件，但是，这对我们来说是不可行的。我们将把它设置为服务！因此，如果谷歌对我们所在的集群进行维护，一旦机器重启，机器人就会自动重启。一旦连接到远程服务器，验证 dsbot 是否成功。

```
steve@slackbot:~$ ls -al
total 5240
drwxr-xr-x 3 steve steve    4096 Nov 26 17:56 .
drwxr-xr-x 4 root  root     4096 Nov 26 05:23 ..
-rw------- 1 steve steve     110 Nov 26 18:21 .bash_history
-rw-r--r-- 1 steve steve     220 May 15  2017 .bash_logout
-rw-r--r-- 1 steve steve    3526 May 15  2017 .bashrc
-rwxr-xr-x 1 steve steve 5334816 Nov 26 17:55 dsbot
-rw-r--r-- 1 steve steve     675 May 15  2017 .profile
drwx------ 2 steve steve    4096 Nov 26 17:55 .ssh 
```

Enter fullscreen mode Exit fullscreen mode

完美！

### Systemd

我们的虚拟机，只要你按照我的方式设置，应该会运行`systemd`。我们将使用它来创建一个系统服务来管理我们的机器人。如果你不熟悉的话，在[数字海洋](https://www.digitalocean.com/community/tutorials/systemd-essentials-working-with-services-units-and-the-journal)上有一个很好的关于 systemd 的初级读本。我们将使用可靠的旧`vim`来建立我们的服务文件。

```
$ sudo vim /etc/systemd/system/dsbot.service 
```

Enter fullscreen mode Exit fullscreen mode

进入`vim`后，按下`i`进入插入模式，然后键入或粘贴以下内容。您需要将主目录位置更新到您将 dsbot 可执行文件复制到的位置。它不需要留在主目录中，你也可以把它移到`/opt/dsbot`或类似的地方。您还需要从位于[https://YOURINSTANCENAME.slack.com/apps/A0F7YS25R-bots](https://YOURINSTANCENAME.slack.com/apps/A0F7YS25R-bots)的 Slack 实例中获取 Slack 令牌，并相应地更新环境变量。

```
[Unit]
Description=dsbot for slack
After=network.target

[Service]
Type=simple
User=steve
WorkingDirectory=/home/steve
ExecStart=/home/steve/dsbot
Restart=on-abort

Environment=SLACKTOKEN=xoxb-slacktoken

[Install]
WantedBy=multi-user.target 
```

Enter fullscreen mode Exit fullscreen mode

现在谷歌一下如何保存和退出 vim ( `ESC:wq`)。一旦返回提示，我们就可以启用该服务。

```
$ sudo systemctl enable dsbot.service 
```

Enter fullscreen mode Exit fullscreen mode

最后，启动它！

```
$ sudo systemctl start dsbot 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！你现在应该有你自己的机器人运行在云中的虚拟机上——并且免费托管。下次我们将废弃 VM，看看我们可以用 Docker 和 Kubernetes 做些什么。

[![Praise the Sun](img/76be755626f737ef9176f0fc3ae701fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QXC4l969--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jcuy55klegvgufizake2.png)

* * *

| 喜欢这篇文章吗？ |
| --- |
| 给我买杯咖啡怎么样？ |

* * *