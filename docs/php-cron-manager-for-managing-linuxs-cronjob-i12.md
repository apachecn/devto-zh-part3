# php cron 管理器，用于管理 linux 的 cronjob

> 原文：<https://dev.to/gyudoza/php-cron-manager-for-managing-linuxs-cronjob-i12>

我管理它只是为了个人使用，但是我认为有人需要它。当我使用 linux crontab 时，与 iptables 不同，我感到非常不舒服，所以我把它当作 php 代码，而不是 shell 脚本。抱歉，我没有关于 shell 脚本的技能。

无论我如何努力让它变得简单。所以没有处理一些危险的命令(例如:RM-RF[某些东西]，停止，重启，关机，等等)...).所以我希望你在使用这个类的时候要小心。我对使用本课程所产生的问题不负任何责任。可以理解，您需要了解 cron 命令。

Cronjobs 由这个类生成，由 CRONTAG 管理。CRONTAG 是贴了 cronjob 尾巴的评论。它通过使用正则表达式创建和移除大约个 cronjobs。

代码和手册在回购下面。我不知道如何在这里写代码片段。请好好使用它。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [枣乳 3 ](https://github.com/jujumilk3) / [ PHPCronManager](https://github.com/jujumilk3/PHPCronManager)

### PHPCronManager

<article class="markdown-body entry-content container-lg" itemprop="text">

# PHPCronManager

### 前言

我试着让这变得最简单。所以没有处理一些危险的命令(例如:RM-RF[某些东西]，停止，重启，关机，等等)...)所以我希望你在使用这个类的时候要小心。我对使用这个类

出现的问题不负任何责任

### 描述

接近 linux 的 cron 系统。Cronjobs 是由这个类创建的，由 CRONTAG 管理。

### 测试环境

OS : CentOS 6.8(最终版)
php : PHP 7.0.16
和可以理解的 **crond** 已安装。

### 更新注释

*   v1.01(2018-12-20)
    *   函数 add_cronjob 的命令变得更加安全。
*   1.00 版(2018-11-10)
    *   上传

例如(假设 u 要完全加载这个文件)

```
$cron_manager = new CronManager()
$cron_add_result = $cron_manager->add_cronjob('* * * * * date >> /var/testdir/datelog.txt 2>&1', 'datelog');
//if this codes worked normally, u will get array return with success status
//Already listed
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/jujumilk3/PHPCronManager)