# 排除 crontab 不工作的原因

> 原文：<https://dev.to/jonathans/troubleshoot-why-crontab-is-not-working-2gj4>

Crontab 是一个特别挑剔的 unix 工具。要弄清楚您计划的作业为什么没有正常运行并不总是最容易的。

有很多事情可能会出错。当然，要做的第一件事就是仔细检查 crontab 语法是否在做您期望它做的事情。Crontab guru 正是在这方面提供了帮助。

作为对星星和数字意义的提醒。

```
# m   h   dom mon dow   username command
# *   *   *   *   *     dokku    command to be executed
# -   -   -   -   -
# |   |   |   |   |
# |   |   |   |   +----- day of week (0 - 6) (Sunday=0)
# |   |   |   +------- month (1 - 12)
# |   |   +--------- day of month (1 - 31)
# |   +----------- hour (0 - 23)
# +----------- min (0 - 59) 
```

crontab 位于`/etc/crontab`，然而**不要**去编辑那个文件。相反，您可以在`/etc/cron.d`中创建一个与 cronjob 相关的特定文件。例如，如果您在一台主机上运行多个应用程序，创建一个特定的文件会很有帮助。所以你已经编辑了文件，它仍然没有运行。

如果在`/etc/cron.d`中创建新文件。确保文件**没有**包含任何点，否则它不会运行。它拥有正确的权限也很重要。文件的正确权限应该是`-rw-r--r-- root root`。这些权限是以下两个命令的结果。

```
chown root:root yourcronfile
chmod 644 yourcronfile 
```

您可能需要重新启动 cron 服务来获取您所做的更改。你可以用`sudo service cron restart`来做。

您可以检查 cron 日志，以确保 crontab 正常工作。默认情况下，日志位于`/var/log/syslog`中。运行下面的 grep 命令将获得所有的 cron 日志。

```
grep cron /var/log/syslog 
```

即使作业被记录在那里，任何错误也不会显示在那里。所以把它们分开记录会很有帮助。如果您在 crontab `>> /var/log/myjob.log 2>&1`的末尾添加以下代码片段，它就会这样做。你以后一定会感谢我的；).

你的 cronjob 会在某个时候失败。像[死人告密者](https://deadmanssnitch.com/cases/89b251ba-1c4a-40a4-88e3-25cc65b908c2/snitches)这样的服务肯定会帮助你知道*什么时候*失败了。前提是您将向他们的服务发送一个请求，如果在过去的 X 分钟内没有发送请求，它将通知您您的 cronjob 可能失败了，您需要检查它。

## 独孤插曲

让您的 crontab 与 git 一起签入当然很有帮助。如果你正在使用 dokku，有一个有用的插件`supply-config`几乎可以做到这一点。有一个[公开公关](https://github.com/dokku-community/dokku-supply-config/pull/7)来弥补这个差距。Dokku 文档也有一些有用的提示，告诉你在配置 crontab 时应该注意什么。

你有没有发现 crontab 可能失败的其他方式？