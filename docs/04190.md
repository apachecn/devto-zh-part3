# Cron 初学者指南

> 原文：<https://dev.to/m_nevin/a-beginners-guide-to-cron-1ma4>

需要每月在 Linux 服务器上进行备份吗？每隔几天在 Mac 上自动运行一些脚本？或者每小时在你的 Raspberry Pi 上运行一些代码？Cron jobs 的出现让这一切变得更容易。

* * *

最近在工作中，我一直在使用一种非常臃肿的方法来自动化一些常规的数据捕获，直到我想起了 Cron。

这提醒了我它是多么的有用，这让我把它用在了我一直在做的各种小项目上，比如一些树莓 pi 的数据收集。

使用 Cron 提醒了我它有多么有用，有时又是如何被忽视的。所以，这篇文章旨在帮助初学者，那些不熟悉类 UNIX 环境的人或者有些生疏的人，开始使用 Cron。

所以首先；

# Cron 是什么？

Cron 的名字来源于希腊语的时间；Cron 是一个基于时间的作业调度器，适用于类 Unix 操作系统。这意味着它允许我们安排作业在固定的时间、日期或间隔连续运行，这些作业可以是任何东西，只要它是一行；从简单的命令到复杂的脚本。

这些作业通常用于系统维护、自动化或管理任务，用于减轻用户的记忆负担，使其无需干预即可运行。

听起来很有用，我们如何开始？

# 创建 Cron 作业

这一切都发生在命令行上，所以从那里开始，打开 Cron 表，或 crontab。在这里我们可以看到、编辑和创建新的 Cron 作业，可以通过键入以下内容来访问；

```
crontab -e 
```

Enter fullscreen mode Exit fullscreen mode

如果你以前从未使用过 Cron，它会打开一个空文件让你输入命令。

注意:如果您还没有设置默认编辑器，它可能会要求您设置一个默认编辑器来编辑包含表格的文件，这可以是任何命令行编辑器，如 Vim 或 nano。

## Cron 格式

Cron 遵循标准格式，5 个值，然后是您的命令，分钟，小时，天，月和星期几。比如说；

```
5 7 * * * /home/test.sh 
```

Enter fullscreen mode Exit fullscreen mode

这将在每天 7 点 5 分运行一个 bash 脚本 test.sh。

一个好的形象化的例子是这样的，它大量地解释了这 5 个价值中的每一个所包含的内容；

```
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of the month (1 - 31)
│ │ │ ┌───────────── month (1 - 12)
│ │ │ │ ┌───────────── day of the week (0 - 6) (Sunday to Saturday, 7 is also Sunday on some systems)
│ │ │ │ │                                   
│ │ │ │ │
* * * * * command to execute 
```

Enter fullscreen mode Exit fullscreen mode

例如，`0 * * * *`是每一分钟，`* * * * *`是每一秒钟，`0 0 * * *`是每个月的开始。星号的含义与大多数应用程序中的含义相同，从开始到结束，一切都是如此。

但是除了选择一个值，你还可以做更多的事情；

#### 斜线

您可以使用“/”来表示步数，因此`*/10 * * * *`将表示每 10 分钟一次。

#### 连字符

另一个是'-'可用于包含范围，因此`0 0 1-6 * *`将是前 6 个月每个月的开始。

#### 逗号

我现在要讲的最后一个是'，'，它可以用来创建列表，例如，`0 * * * MON,WED,FRI`它会在周一、周三和周五的每一分钟运行一次。

有更多的选项，例如使用一些字符，但这些在大多数平台上都不是标准的。

#### 关键词

上面我们也只是使用了一些关键字，Cron 有一些功能来理解关键短语，比如日和月的简写。因此，对于星期字段，您可以使用周一至周日。对于月份字段，您可以使用 JAN-DEC。

在许多系统中，有一些关键词可以在一些频繁使用的时候使用，这里列出了一些最常见的关键词；

```
@reboot        Run once at startup
@yearly        Run once a year (0 0 1 1 *)
@annually      same as @yearly
@monthly       Run once a month (0 0 1 * *)
@weekly        Run once a week (0 0 * * 0)
@daily         Run once a day (0 0 * * *)
@midnight      (same as @daily)
@hourly        Run once an hour (0 * * * *) 
```

Enter fullscreen mode Exit fullscreen mode

为了使这变得更容易，有一些相当好的在线 crontab 生成器来帮助你以更直观的方式构建你的工作。

## 你有邮件吗？

此时，值得一提的是，您将会收到一些邮件。每次 Cron 作业运行时，可能一分钟一次，它使用 SMTP 向您的终端发送电子邮件通知。下次打开终端时，你会看到一个提示，说你收到了邮件，运行`mail`会让你看到这些，你可以从那里`del *`删除所有的邮件...

或者，如果您将它添加到 crontab 的开头

```
MAILTO="" 
```

Enter fullscreen mode Exit fullscreen mode

这会阻止它发送你的终端邮件。

## 检查你的工作

现在，如果您已经创建了一个作业，您可以看到我们的 Cron 表，它将通过运行以下命令来包含它；

```
crontab -l 
```

Enter fullscreen mode Exit fullscreen mode

这将允许您查看您的作业和您设置的任何其他作业，`-e`将使您能够返回并编辑您的作业(如果您愿意的话)。

## 删除一个作业

要删除 crontab 以停止所有作业，您只需运行以下命令即可；

```
crontab -r 
```

Enter fullscreen mode Exit fullscreen mode

它们都将消失，或者您可以编辑 crontab 并删除您不想要的单个组件。

# 陷阱

虽然 Cron 看起来相当简单，但它也充满了陷阱，与任何与 Cron jobs 一起工作的人交谈通常会导致一些关于错误配置的工作的故事，这些工作很难解决或导致一些灾难。因此，在使用它们时，你会经常发现自己被简单的事情或工具的一些奇怪的方面绊倒。

一些最常见的陷阱来自:

*   错误的 Crontab 符号
*   许可问题
*   环境变量

这个堆栈交换[线程](https://askubuntu.com/questions/23009/why-crontab-scripts-are-not-working)涵盖了许多最常见的线程，对于解决您的问题是一个非常有价值的资源。

* * *

总的来说，如果用在正确的地方，Cron 是一个很好的工具，但它不是满足您调度需求的一站式商店。记住要为工作选择正确的工具，但是如果是 Cron，希望这个指南能让你走上正确的道路！