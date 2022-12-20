# Laravel 队列分步指南

> 原文：<https://dev.to/larashout/laravel-queues-step-by-step-guide-p00>

在这篇文章中，我们将讨论 Laravel 队列，这是 Laravel 框架最好的特性之一。所以让我们开始吧。

## Laravel 队列

Laravel 队列提供了各种不同队列后端的集成，如 Beanstalkd、亚马逊 SQS、Redis、同步(sync)和数据库。您可以在 config/queue.php 文件中找到队列配置。在这个文件中，我们将为每个队列驱动程序定义连接的配置。您可以查看官方文档了解更多详细信息。

你可以在 [Laravel Queues](https://www.larashout.com/laravel-queues-step-by-step-guide) 阅读全文