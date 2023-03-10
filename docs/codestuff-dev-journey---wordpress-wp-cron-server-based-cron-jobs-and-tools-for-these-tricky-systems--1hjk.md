# Wordpress WP-Cron，基于服务器的 Cron 作业，以及这些棘手系统的工具。

> 原文：<https://dev.to/codestuff2/codestuff-dev-journey---wordpress-wp-cron-server-based-cron-jobs-and-tools-for-these-tricky-systems--1hjk>

## 欢迎来到第一部分，我希望这是我作为全职开发人员和前端工程师的旅程的半定期记录。

大家好，我叫亚当，在北科罗拉多州的一家数字营销公司工作。我的主要工作职责是处理客户要求的定制工作。这通常属于构建 wordpress 插件、应用程序、API 集成和偶尔的图形设计任务。

我也以拍照、做木工、和家人在山里闲逛而闻名。

你可以在 Github、
Unsplash、Dribbble 和其他地方找到我的一些作品。

[GitHub - adamwhitlock1](https://github.com/adamwhitlock1)

[Twitter - @codestuff2](https://twitter.com/codestuff2)

[无飞溅摄影](https://unsplash.com/@adam_whitlock)

[可滴设计](https://dribbble.com/codestuff2)

我说得够多了，让我们开始派对吧。
[![a color run event I attended](img/0f31fb44aaa75787a928dcc082d0806f.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--T89j1ELm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iz3qi5uml0fwjgoctpyj.jpg)

本系列的目的只是让我养成记录学习代码过程的习惯。希望我的写作也能帮助一些人学到新的有用的东西！

去比赛了。我今天做的一件有趣的事情是看了一个网站的一些问题，这个网站应该会定期自动获取汽车库存数据。它没有做到这一点。输入 Wordpress 的 wp-cron。

## WP-Cron，以及它是如何导致你拔头发的。

Wordpress 将 WP-Cron 特性描述为:

Cron 是 UNIX 系统上可用的基于时间的任务调度系统。WP-Cron 是 WordPress 处理基于时间的任务的方式。WordPress 的几个核心功能，比如检查更新和发布预定帖子，都利用了 WP-Cron。

更多信息请访问:
[https://developer.wordpress.org/plugins/cron/](https://developer.wordpress.org/plugins/cron/)

许多主题开发者和插件创建者已经创建了一些功能，这些功能与 wordpress cron 系统挂钩，包括内容的自动社交媒体发布、电子邮件调度等等。最近，我开发了一个功能，一旦某个房地产销售公司完成了他们的房地产销售活动，该房地产销售就会在结束后 24 小时内自动从网站上删除。

当你试图连接到这个 wp-cron 系统并使用它构建一些很酷的东西时，这个系统可能很难诊断。

典型地，wp-cron 被设置为:

```
Super Simple One Liner (PHP)

<?php
wp_schedule_event( $timestamp, 'daily', 'hook_for_cron_event', $args_for_hook );
?>

// $timestamp (integer)
// When you want the event to start.
// Basically you could set up an event to have it's first execution
// happen in the future by setting this to a UNIX timestamp value in the future.
// I usually just set this value to time() which produces an immediate timestamp
// that way my event starts immediately and conitinues on the inteval of my choosing

// $recurrence ie 'daily' (string)
// How often the event should reoccur.
// The default values are:
// hourly, twicedaily, or daily
// If you need a different recurrence you can create your own
// custom reocurrence schedule.
// (look up the filer cron_schedules for more info on custom schedules)

// $hook_for_cron_event (string)
// This is the name of the action hook that you
// want to execute when the event occurs.
// This is where your write all the stuff you need to do

// $args_for_hook (array) (optional)
// Put any arguments that you need to pass into the $hook_for_cron_event
// into this array. Being optional, it will default to none if you don't provide any args. 
```

不管是谁，如果你必须和 wordpress crons 一起工作或者调试他们可能引起的潜在问题，我有一些资源给你。

### WP Crontrol

[https://wordpress.org/plugins/wp-crontrol/](https://wordpress.org/plugins/wp-crontrol/)

[![crontrol plugin](img/ba576ae6f2920f0052f31f81fdcb3874.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6KDqPApC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/madshot/37313838608e113f766882139cb80944.png)

WP Crontrol 是一个插件，可以显示所有当前正在运行的 wp crons，还可以让你随时手动运行这些作业，这样你就可以很容易地调试它们。

* * *

### 禁用 wp-cron 功能

有几个原因可以让你完全禁用一个站点上的 wp-cron 功能。第一个原因是，有时，尤其是当您在同一台服务器上运行多个站点时，您的站点可能会由于在这个 wp-cron 脚本期间运行的任务而导致高 CPU/内存负载。

您可能有大量不同的任务注册到这个 wp-cron 功能，实际上，它们可能不需要像计划的那样频繁运行。如果在同一台服务器上运行大量的站点，这种负载会使整个服务器变得复杂和缓慢，甚至会使服务器完全崩溃。

这种“伪”cron 的一种替代方法是实际设置传统的、基于服务器的、真正的 cron 作业来激活 wp-cron 功能。这样，您就可以控制 wp-cron 脚本何时运行，并为它设置一个具体的时间表。您的站点可能只需要每 6 小时执行一次特定的 wp-cron 任务，因此为此设置一个常规的服务器 cron 会有很大帮助。

要了解 wp-cron 与传统 cron 有何不同，以及如何禁用 wp-cron，您可以查看来自 Kinsta 的这篇有用的文章。

[https://kinsta.com/knowledgebase/disable-wp-cron/](https://kinsta.com/knowledgebase/disable-wp-cron/)

* * *

### 服务器 Cron 资源

传统的基于服务器的 crontab 作业确实有严格的语法。

Crontab Guru 使得编写传统的 cron 作业语法变得更加容易

[https://crontab.guru/](https://crontab.guru/)

如果您确实实现了传统的 cron 作业，那么这些作业的调试和监控可能就像 WP-cron 一样棘手。

Cronitor 为您的服务器提供 cron 监控，而且非常简单。

[https://cron . I](https://cronitor.io)

想要一个快速指南来告诉你 cron 工作的来龙去脉。我从 WhoIsHostingThis 引用这个资源？一直都是。

[https://www.whoishostingthis.com/resources/cron/](https://www.whoishostingthis.com/resources/cron/)

* * *

### 我错过了什么？

你有使用 wp-cron 或常规 cron 工作的技巧或提示吗？你使用的常见 crons 的代码报告，或者你想分享的任何东西？

在代码主题中，您还有什么想漫谈的吗:)

请让我知道你的想法，感谢阅读！