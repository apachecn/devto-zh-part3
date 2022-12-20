# 减少 PHP 中的 HTTP 请求开销

> 原文：<https://dev.to/raoulmeyer/reducing-http-request-overhead-in-php-5343>

PHP 领域的一个常见模式是从整体架构向由(微)服务组成的架构转变。在这种转变中，您可能仍然有一个主入口点应用程序，然后调用一些服务来构建视图或应用一些操作。

## 问题

随着这一转变，我们有三个似乎相互矛盾的主要愿望:

*   我们希望我们的网站速度快。
*   我们希望添加用户甚至不知道他们想要的功能。
*   我们希望以一种很好的分离和模块化的方式来做到这一点，将我们的环境分成暴露 API 的(微)服务。

在 PHP 中，有了我们想要的一切，我们会遇到以下情况:

*   PHP 在很大程度上仍然是一种同步语言，对异步操作和多线程的支持有限。这意味着对您的服务的 HTTP 调用将按顺序执行。花费在 HTTP 调用上的时间增加很快，并且有足够多的不同服务，很容易导致页面或端点变慢。
*   PHP 的核心概念之一是，所有运行时数据，如变量和请求数据，在处理完请求后都会被清除。这也适用于资源，如打开的文件或打开的连接。因此，即使客户端和服务器都同意保持连接，也不能共享 HTTP 连接。
*   建立一个 TCP 连接是非常昂贵的。与 TLS 的新连接，已经成为基本上所有数字通信的默认连接，甚至更加昂贵。虽然有很多因素在起作用(ping、CPU 使用率、加密配置等。)，预计 TCP 连接的开销为几毫秒，TLS 连接的开销大约为 10-100 毫秒。

我们处理越来越多的 HTTP 请求，每次都打开一个新的连接，结果浪费了很多时间。

## 一解

库 [ext-http](https://github.com/m6w6/ext-http) 为 PHP 增加了扩展的连接重用。您可以在创建客户端时指定一个句柄标识符，它将重用该句柄和它打开的任何连接。您可以在不同的脚本执行中使用句柄标识符，它将能够使用相同的句柄。

如何使用它的一个例子:

```
<?php
declare(strict_types=1);

use http\Client;

$client = new Client(null, 'handle-name');

$request = new Client\Request('GET', 'https://www.google.cimg/branding/googlelogo/1x/googlelogo_color_272x92dp.png');

$client->enqueue($request);
$client->send();

/** @var Client\Response $response */
$response = $client->getResponse();
... 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，这个库引入了`Client`、`Response`和`Request`类，类似于它们在 [PSR 标准](https://www.php-fig.org/psr/psr-7/)中的定义。不幸的是，他们实际上并不完全符合 PSR 标准，所以你必须自己做一些映射。

这个例子的一个小基准显示了一个很好的改进:第一个请求需要大约 60 毫秒，但是之后请求时间下降到大约 15 毫秒。

### 管理手柄

为了支持并发请求，您必须确保您没有同时使用同一个`handle-name`。这带来了新的挑战，因为现在您必须以某种方式维护可用句柄的列表。为了进行测试，我尝试将一个 0 到 100 之间的随机整数作为`handle-name`的一部分。这给出了不错的结果，但是当你经历更高的负载时，它当然会停止工作。

虽然我还没有尝试过，但是我认为你可以通过在队列中存储句柄标识符来解决这个问题(例如 Redis 中的 [FIFO 队列)。检索空闲时间最长的句柄，如果没有可用的句柄，可以创建一个新的句柄。](https://redislabs.com/ebook/part-2-core-concepts/chapter-6-application-components-in-redis/6-4-task-queues/6-4-1-first-in-first-out-queues/)

在理想的情况下，图书馆会处理好这件事，但是现在自己做似乎是大多数情况下可以接受的妥协。

## 结论

尽管这个解决方案非常特定于 PHP，但是这个原则适用于所有语言，也适用于支持异步处理的语言。连接重用将减少请求时间，也将降低客户机和服务器的负载。

您是否尽可能重用连接？如果没有，这可能是提高性能和资源利用率的一种简单方法。