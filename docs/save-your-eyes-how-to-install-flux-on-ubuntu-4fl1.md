# 保护你的眼睛！如何在 Ubuntu 上安装 Flux

> 原文：<https://dev.to/benjaminspak/save-your-eyes-how-to-install-flux-on-ubuntu-4fl1>

[最初发布在我的博客上](https://blog.benjaminspak.com/how-to-flux-on-ubuntu-installation-and-automated-run-time-cjvvy5aw2004we4s1duyehdtn)

* * *

相信我，你想要[通量](https://justgetflux.com/)。你的眼睛&睡眠质量会感谢你。

如果您需要手动启动小程序，只需在终端中键入“fluxgui”。

资源:[通量回购](https://github.com/xflux-gui/fluxgui)

```
sudo add-apt-repository ppa:nathan-renniewaldock/flux
sudo apt-get update
sudo apt-get install fluxgui 
```

Enter fullscreen mode Exit fullscreen mode

资源:[在 Ubuntu 中创建一个 Cron 任务](https://www.liquidweb.com/kb/create-a-cron-task-in-ubuntu-16-04/)

检查是否安装了 cron:

```
dpkg -l cron 
```

Enter fullscreen mode Exit fullscreen mode

安装 cron(如果需要)

```
sudo apt-get install cron 
```

Enter fullscreen mode Exit fullscreen mode

验证 cron 正在运行。

```
systemctl status cron 
```

Enter fullscreen mode Exit fullscreen mode

创建新的 cron 作业。

```
crontab -e 
```

Enter fullscreen mode Exit fullscreen mode

选择您选择的终端编辑器。

资源: [Crontab 生成器](https://crontab-generator.org/)

使用 Crontab 生成器选择希望 fluxgui 何时运行。
参考消息:Crontab 生成器上的“执行命令”字段为“fluxgui”

验证 cron 作业是否已创建。

```
crontab -l 
```

Enter fullscreen mode Exit fullscreen mode

如果您需要手动启动小程序，只需在终端中键入“fluxgui”。

赢了！你可以走了。