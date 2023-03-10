# PHP 中的可恢复文件上传:以优雅的方式处理大文件上传

> 原文：<https://dev.to/konsole/resumable-file-upload-in-php-handle-large-file-uploads-in-an-elegant-way-4a84>

> 曾经在 PHP 中为大文件上传而挣扎过吗？想知道你是否可以继续上传你离开的地方，而不需要在任何中断的情况下重新上传全部数据？如果这听起来对你来说很熟悉，那么继续读下去。

文件上传是我们在几乎所有现代 web 项目中的一项常见任务。有了所有不同的工具，用任何语言实现文件上传功能都不是很难。但是，当涉及到大文件上传时，事情变得有点复杂。

比方说，你正试图上传一个相当大的文件。你已经等了一个多小时了，上传了 90%。然后突然，你的连接中断或浏览器崩溃。上传被中止，您需要从头开始。令人沮丧，不是吗？更糟糕的是，如果你的连接速度很慢，就像世界上很多地方一样，无论你尝试多长时间，每次都只能上传第一部分。

[![Basic TUS architecture](img/8cde9d353ff1576f7352c4858287a0ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y1Az-0sG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Avf60tLKejXf1XZgBJcxhMw.png)

在这篇文章中，我们将看到一个尝试，通过使用 tus 协议上传可恢复块中的文件来解决这个问题。

[https://www.youtube.com/embed/sHep0dLbMd4](https://www.youtube.com/embed/sHep0dLbMd4)

## 什么是 tus？

Tus 是一个基于 HTTP 的[开放协议，用于可恢复文件上传](https://tus.io/)。可恢复意味着我们可以继续我们离开的地方，而不需要在任何中断的情况下重新上传全部数据。如果用户想要暂停，中断可能是自愿发生的，或者在网络问题或服务器中断的情况下偶然发生的。

> Tus 协议[于 2017 年 5 月被 Vimeo](https://medium.com/vimeo-engineering-blog/vimeo-is-adopting-tus-d5e999acd517) 采用。

## 为什么是 tus？

引用自 [Vimeo 的博客](https://medium.com/vimeo-engineering-blog/vimeo-is-adopting-tus-d5e999acd517):

> 我们决定在我们的上传栈中使用 tus，因为 tus 协议以简洁和开放的方式标准化了上传文件的过程。这种标准化将允许 API 开发人员更多地关注他们的应用程序特定的代码，而不是上传过程本身。

以这种方式上传文件的另一个主要好处是，你可以从笔记本电脑开始上传，甚至可以继续从手机或任何其他设备上传相同的文件。这是增强用户体验的好方法。

<figure>

[![Basic Tus Architecture](img/054e91c513a02fafd421a20447c4a7d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6akJzIuM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ybitnimp708fds5csq1y.png)

<figcaption>Basic Tus Architecture</figcaption>

</figure>

## 入门

让我们从添加依赖项开始。

```
$ composer require ankitpokhrel/tus-php 
```

Enter fullscreen mode Exit fullscreen mode

[tus-php](https://github.com/ankitpokhrel/tus-php) 是一个框架不可知的纯 PHP [服务器和客户端实现](https://tus.io/implementations.html)用于 tus 可恢复上传协议 v1.0.0

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[ankitpokhrel](https://github.com/ankitpokhrel)/[tus-PHP](https://github.com/ankitpokhrel/tus-php)

### 🚀用于 tus 可恢复上传协议 v1.0.0 的纯 PHP 服务器和客户端

<article class="markdown-body entry-content container-lg" itemprop="text">

# TusPHP

[![PHP Version](img/6de1fa1cd4681cf5dc567c38ee34d76e.png)](https://packagist.org/packages/ankitpokhrel/tus-php)[![Build Status](img/e4e1976f5246b9701ff341ddaaaf7c3b.png)](https://github.com/ankitpokhrel/tus-php/actions/workflows/ci.yml?query=branch%3Amain+is%3Acompleted)[![Code Coverage](img/f7c27de51d0e544aae9fe2d354b34b7c.png)](https://scrutinizer-ci.com/g/ankitpokhrel/tus-php)[![Scrutinizer Code Quality](img/8ed1d66263ef312a31cb65e1be5507e0.png)](https://scrutinizer-ci.com/g/ankitpokhrel/tus-php)[![Downloads](img/e4d72197de8c13d3bd198459f92ef238.png)](https://packagist.org/packages/ankitpokhrel/tus-php)[![Software License](img/bef0dd78b2319f6aa43444906a81e982.png)](https://github.com/ankitpokhrel/tus-php/blob/main/LICENSE)

*使用 [tus 可恢复上传协议 v 1 . 0 . 0](https://tus.io)T3】在 PHP 中上传可恢复文件*

[![TusPHP Demo](img/31eb4f99c2663d50d055ffc47f218ed3.png)](https://github.com/ankitpokhrel/tus-php/blob/main/example/demo.gif) 

[中条](https://medium.com/@ankitpokhrel/resumable-file-upload-in-php-handle-large-file-uploads-in-an-elegant-way-e6c6dfdeaedb) <g-emoji class="g-emoji" alias="zap" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a1.png">⚡</g-emoji> [拉勒维尔&流明整合](https://github.com/ankitpokhrel/tus-php/wiki/Laravel-&-Lumen-Integration) <g-emoji class="g-emoji" alias="zap" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a1.png">⚡</g-emoji> [Symfony 整合](https://github.com/ankitpokhrel/tus-php/wiki/Symfony-Integration) <g-emoji class="g-emoji" alias="zap" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a1.png">⚡</g-emoji> [CakePHP 整合](https://github.com/ankitpokhrel/tus-php/wiki/CakePHP-Integration) <g-emoji class="g-emoji" alias="zap" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a1.png">⚡</g-emoji> [WordPress 整合](https://github.com/ankitpokhrel/tus-php/wiki/WordPress-Integration)

[![](img/52ad1a1a2a4620e3ea80480739cc1877.png)](https://opencollective.com/tus-php#backers)

tus 是一个基于 HTTP 的协议，用于可恢复的文件上传。可恢复意味着您可以从中断的地方继续，而无需在任何中断的情况下重新上传全部数据。如果用户想要暂停，中断可能是自愿发生的，或者在网络问题或服务器中断的情况下偶然发生的。

### 目录

*   [安装](https://github.com/ankitpokhrel/tus-php#installation)
*   [用途](https://github.com/ankitpokhrel/tus-php#usage)
    *   [服务器](https://github.com/ankitpokhrel/tus-php#server)
        *   [Nginx](https://github.com/ankitpokhrel/tus-php#nginx)
        *   [阿帕奇](https://github.com/ankitpokhrel/tus-php#apache)
    *   [客户端](https://github.com/ankitpokhrel/tus-php#client)
    *   [第三方客户端库](https://github.com/ankitpokhrel/tus-php#third-party-client-libraries)
    *   [云提供商](https://github.com/ankitpokhrel/tus-php#cloud-providers)
*   [扩展支持](https://github.com/ankitpokhrel/tus-php#extension-support)
    *   [到期](https://github.com/ankitpokhrel/tus-php#expiration)
    *   [串联](https://github.com/ankitpokhrel/tus-php#concatenation)
*   [事件](https://github.com/ankitpokhrel/tus-php#events)
    *   [响应事件](https://github.com/ankitpokhrel/tus-php#responding-to-an-event)
*   [中间件](https://github.com/ankitpokhrel/tus-php#middleware)
    *   [创建中间件](https://github.com/ankitpokhrel/tus-php#creating-a-middleware)
    *   [添加中间件](https://github.com/ankitpokhrel/tus-php#adding-a-middleware)
    *   [跳过一个中间件](https://github.com/ankitpokhrel/tus-php#skipping-a-middleware)
*   [设置开发环境和/或在本地运行示例](https://github.com/ankitpokhrel/tus-php#setting-up-a-dev-environment-andor-running-examples-locally)
    *   [码头工人](https://github.com/ankitpokhrel/tus-php#docker)
*   [投稿](https://github.com/ankitpokhrel/tus-php#contributing)
*   [关于这个项目的问题？](https://github.com/ankitpokhrel/tus-php#questions-about-this-project)
*   [支持者](https://github.com/ankitpokhrel/tus-php#supporters)

### 装置

通过 composer 提取包。

```
$ composer require ankitpokhrel/tus-php
// Use v1 for php7.1, Symfony 3 or 4.
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/ankitpokhrel/tus-php)

> **更新** : Vimeo 现在正在为 Vimeo API 使用他们[官方 PHP 库](https://github.com/vimeo/vimeo.php/pull/186) [v3](https://github.com/vimeo/vimeo.php/releases/tag/3.0.0) 中的 [TusPHP](https://github.com/ankitpokhrel/tus-php) 。

## 创建服务器来处理我们的请求

这是一个简单服务器的样子。

```
// server.php
$server   = new \TusPhp\Tus\Server('redis');

$response = $server->serve();

$response->send();

exit(0); // Exit from current PHP process. 
```

Enter fullscreen mode Exit fullscreen mode

您需要配置您的服务器来响应特定的端点。例如，在 Nginx 中，您可以这样做:

```
# nginx.conf

location /files {
    try_files $uri $uri/ /path/to/server.php?$query_string;
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们假设我们服务器的 URL 是 [http://server.tus.local](http://server.tus.local) 。因此，基于上面的 nginx 配置，我们可以使用 http://server.tus.local/files 的 T2 来访问我们的 tus 端点。

现在，我们可以使用以下 RESTful 端点。

```
# Gather information about server's current configuration
OPTIONS /files

# Check if the given upload is valid
HEAD /files/{upload-key}

# Create a new upload
POST /files

# Resume upload created with POST
PATCH /files/{upload-key}

# Delete the previous upload
DELETE /files/{upload-key} 
```

Enter fullscreen mode Exit fullscreen mode

查看[协议详情](https://tus.io/protocols/resumable-upload.html)了解更多关于端点的信息。

> 如果您使用的是 Laravel 这样的框架，您可以在框架路由文件中定义到所有基于 tu 的端点的路由，而不是修改您的服务器配置。我们将在另一个教程中详细介绍这一点。

## 使用 tus-php 客户端处理上传

一旦服务器就位，就可以使用客户机来上传文件。让我们从创建一个简单的 HTML 表单来获取用户输入开始。

```
<form action="upload.php" method="post" enctype="multipart/form-data">
    <input type="file" name="tus_file" id="tus-file" />
    <input type="submit" value="Upload" />
</form> 
```

Enter fullscreen mode Exit fullscreen mode

提交表单后，我们需要遵循几个步骤来处理上传。

#### 1。创建一个 tus-php 客户端对象

```
// Tus client
$client = new \TusPhp\Tus\Client('http://server.tus.local'); 
```

Enter fullscreen mode Exit fullscreen mode

上面代码中的第一个参数是您的 tus 服务器端点。

#### 2。用文件元数据初始化客户端

为了保持上传的唯一性，我们需要使用一些标识符来识别即将到来的请求中的上传。为此，我们必须生成一个唯一的上传密钥，用于以后继续上传。您可以明确提供上传密钥，也可以让系统自己生成密钥。

```
// Set upload key and file meta
$client->setKey($uploadKey)
    ->file($_FILES['tus_file']['tmp_name'], 'your file name'); 
```

Enter fullscreen mode Exit fullscreen mode

如果没有明确提供上传密钥，上述步骤将类似于:

```
$client->file($_FILES['tus_file']['tmp_name'], 'your file name');

$uploadKey = $client->getKey(); // Unique upload key 
```

Enter fullscreen mode Exit fullscreen mode

#### 3。上传一大块

```
// $chunkSize is size in bytes, i.e. 5000000 for 5 MB
$bytesUploaded = $client->upload($chunkSize); 
```

Enter fullscreen mode Exit fullscreen mode

下一次，当你想上传另一个块，你可以使用相同的上传键继续。

```
// To resume upload in next request
$bytesUploaded = $client->setKey($uploadKey)->upload($chunkSize); 
```

Enter fullscreen mode Exit fullscreen mode

上传完成后，服务器会根据校验和验证上传，以确保上传的文件没有损坏。默认情况下，服务器将使用`sha256`算法来验证上传。

> 上面演示视频的完整实现可以在[这里](https://github.com/ankitpokhrel/tus-php/tree/master/example/basic)找到。

## 使用 tus-js-client 处理上传

Uppy 是一个时髦的模块化文件上传插件，由支持 tus 协议的人开发。您可以使用 uppy 将官方的 [tus-js-client](https://github.com/tus/tus-js-client) 与 tus-php 服务器无缝集成。这意味着我们使用服务器的 php 实现和客户机的 js 实现。

```
uppy.use(Tus, {
  endpoint: 'https://server.tus.local/files/', // your tus endpoint
  resume: true,
  autoRetry: true,
  retryDelays: [0, 1000, 3000, 5000]
}) 
```

Enter fullscreen mode Exit fullscreen mode

> 在 [uppy 文档](https://uppy.io/docs/tus/)和示例实现[这里](https://github.com/ankitpokhrel/tus-php/tree/master/example/uppy)查看更多细节。

## 部分上传

tus-php 服务器支持[连接扩展](https://tus.io/protocols/resumable-upload.html#concatenation)，并且能够将多个上传连接成单个上传，使客户端能够执行并行上传和上传不连续的块。