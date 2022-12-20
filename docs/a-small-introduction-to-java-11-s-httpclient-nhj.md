# Java 11 的 HttpClient 简介

> 原文：<https://dev.to/iuriimednikov/a-small-introduction-to-java-11-s-httpclient-nhj>

像 Spark 这样的微型 Java 框架通常缺少内置的 HTTP 客户端，所以如果你的代码需要访问和使用外部的[API](https://www.mednikov.tech/two-factor-authentication-for-spring-webflux-apis/)——带上你自己的客户端，比如 Apache、Okhttp 或 Unirest。这同样适用于[罕见的]非 web Java 应用程序。当然，还有*HttpURLConnection*——从 Java 1.0 开始就有了——陈旧、低级且功能不丰富，但是...

Java 9 引入了一种*孵化器模式*，Java 11 引入了最后一种新的内置 HTTP 客户端。值得用吗？

在这篇文章中，我们将看到如何将 HTTP 客户端添加到您的应用程序中，以及如何同步/异步处理 HTTP 请求。

## Java 11 HTTP 客户端概述

新的 Java HTTP 客户端作为 *java.net.http* 包的一部分提供，它有三个核心组件:

*   **Java . net . http client**——客户端本身，用于发送请求和检索响应。通过*构建器*创建
*   **Java . net . HTTP request**——一个 HTTP 请求，通过*构建器*创建
*   **java.net.HttpResponse** -一个响应。不是直接创建的，而是发送一个 *HttpRequest* 的结果。

值得一提的是，现在 Java 支持开箱即用的同步和异步模式，支持 HTTP/2 +传统 HTTP/1，还支持 WebSocket 客户端。顺便说一下，这是用 Java 测试 web 服务器的一个好的简单的方法。

## HTTP 请求

在我们发出请求之前，我们应该获得 *HttpClient* 实例:

```
/* create with default settings */
var defaultClient = HttpClient.newHttpClient();

/* create a custom client */
var customClient = HttpClient.newBuilder()
                    .followRedirects(Redirect.SAME_PROTOCOL)
                    .authenticator(Authenticator.getDefault())
                    .connectTimeout(Duration.ofSeconds(30))
                    .priority(1)
                    .version(HttpClient.Version.HTTP_2)
                    .build(); 
```

Enter fullscreen mode Exit fullscreen mode

创建 *HttpClient* 有两种方式——使用默认设置或者使用定制参数。完整的参数列表存在于 [java 文档](https://docs.oracle.com/en/java/javase/11/docs/api/java.net.http/java/net/http/HttpClient.html)中，但是这里有一些最常见的参数:

*   version =默认值是 HTTP/2，但是如果需要，可以显式设置 HTTP/1.1
*   connectTimeout =确定客户端等待多长时间才能建立连接
*   followRedirects =默认值是 **NEVER** 。

下一步是创建*请求*。让我们看看如何以同步和异步方式使用它们:

### 发送获取同步方式

同步发送请求是一个简单直接的过程。我们创建 *HttpRequest* ，然后通过 *HttpClient* 发送它，并获得 *HttpResponse* 作为结果。看一看代码片段，它非常简单:

```
var client = HttpClient.newHttpClient();

var request = HttpRequest.newBuilder().GET().uri(URI.create("https://jsonplaceholder.typicode.com/posts/1")).build(); 
```

Enter fullscreen mode Exit fullscreen mode

我们使用[JSONPlaceholder](http://jsonplaceholder.typicode.com/)-fake online REST API 进行测试。注意，客户机和请求都是不可变的，我们可以多次重用它们。首先，让我们看看如何以同步方式获取数据:

```
//sync
var response = client.send(request, HttpResponse.BodyHandlers.ofString());

String body = response.body();

System.out.println(body); 
```

Enter fullscreen mode Exit fullscreen mode

### 以异步方式发送获取

以异步方式执行请求的可能性——是新 API 最好的创新之一。实际上，这样的操作**应该以非阻塞的方式执行**。先来看代码:

```
//async
var future = client.sendAsync(request, HttpResponse.BodyHandlers.ofString());

String body2 = future.thenApply(HttpResponse::body).get();

System.out.println(body2); 
```

Enter fullscreen mode Exit fullscreen mode

API 使用[CompletableFuture](https://www.baeldung.com/java-completablefuture)——可以显式完成——来异步发送请求。执行后，我们可以检索一个数据或使用 lambda 表达式做一些响应，例如:

```
future.thenAccept(this::processResponse);

//...

void processResponse(HttpResponse<String> response){
    //do something
} 
```

Enter fullscreen mode Exit fullscreen mode

### 以同步方式发送帖子

现在，让我们试着发送 **POST** 请求。不同的是我们需要指定*体*并设置发布方法:

```
//...client

//body
String body = "{}"

//create a request
var request = HttpRequest.newBuilder()
                .POST(HttpRequest.BodyPublishers.ofString(body))
                .uri(URI.create("https://jsonplaceholder.typicode.com/posts")).build(); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们简单的发送 POST 请求并获取它的状态代码:

```
var response = client.send(request, HttpResponse.BodyHandlers.ofString());

int code1 = response.statusCode();

System.out.printf("SYNC Status code: %d",code1); 
```

Enter fullscreen mode Exit fullscreen mode

### 以异步方式发送帖子

更好的想法是以异步方式发送 HTTP 请求:

```
var future =  client.sendAsync(request, HttpResponse.BodyHandlers.ofString());
int code2 = future.get().statusCode();
System.out.printf("ASYNC Status code: %d",code2); 
```

Enter fullscreen mode Exit fullscreen mode

正如您所注意到的，其原理与 GET(或任何其他)方法相同。我们创建 *CompletableFuture* ，然后从获得的 *HttpResponse* 结果中获取一个数据。

## 结论

在这篇短文中，我们观察了什么是 Java 11 HttpClient 以及如何使用它:以同步/异步方式创建、发送请求。这是一个非常强大的工具，当你需要使用 API 时，你可以在你的应用程序中使用它。

顺便说一下，你也可以在[我的博客](https://mednikov.net):)中阅读我关于 JVM 相关主题的其他帖子