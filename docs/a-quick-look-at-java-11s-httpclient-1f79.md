# 快速浏览一下 Java 11 的 HttpClient

> 原文：<https://dev.to/aussieguy/a-quick-look-at-java-11s-httpclient-1f79>

*本文原载于[我的博客](http://anthonybruno.dev/2019/01/16/A-quick-look-at-java-11's-HttpClient.html)T3*

Java 11 于 2018 年 9 月发布，是继 Java 8 之后第一个长期支持的版本。它的一个特性是 [HttpClient](https://docs.oracle.com/en/java/javase/11/docs/api/java.net.http/java/net/http/HttpClient.html) ，这是一种新的 HTTP 请求方式。这篇文章将简要介绍 HttpClient，以及它是如何取代 [URLConnection](https://docs.oracle.com/javase/8/docs/api/java/net/URLConnection.html) 的！

## 任务

我在自己的网站[http://anthonybruno.dev/last-update](http://anthonybruno.dev/last-update)上创建了一个页面，上面有一个 Unix 时间戳，显示了网站的最后一次创建时间。

任务很简单，创建一些请求这个页面并返回
时间戳的代码！

## 使用 URLConnection

下面是使用`URLConnection`的代码。

```
// 1\. Create URL object
URL updateUrl = new URL("http://anthonybruno.com.au/last-update");

// 2\. We use openConnection() on the url to get a HttpURLConnection, 
//    that we have to cast(?!). Also, this doesn't actually make a 
//    network request(?!?!?)
HttpURLConnection connection = (HttpURLConnection) updateUrl.openConnection();

// 3\. We can then set things like set request methods, headers.
connection.setRequestMethod("GET");

// 4\. Then we actually connect! Note: connect doesn't return anything, it
//    mutates the connection object!
connection.connect();
int statusCode = connection.getResponseCode();
if (statusCode != 200) {
    throw new RuntimeException("Got non 200 response code! " + statusCode);
}
// 5\. Content is returned in an InputStream (Don't forget to close it!)
InputStream content = connection.getInputStream()

Instant timestamp = processIntoInstant(content)

// 6\. Remember to disconnect! Note: HttpURLConnnection is not autoclosable!
connection.disconnect() 
```

Enter fullscreen mode Exit fullscreen mode

在创建了`URL`对象之后，事情很快就出错了。使用一个名为`openConnection()`的方法是非常违背直觉的，它实际上并没有打开一个连接！不得不将返回的`URLConnection`对象强制转换为`HttpURLConnection`来访问像`setRequestMethod`和`disconnect`这样的方法是非常愚蠢的。最后调用`connect()`(实际上是发出网络请求！)不返回任何东西，相反，您必须从`connection`对象本身获得响应信息。

## 使用 HttpClient

下面是使用`HttpClient`的代码。你会看到很大的不同。

```
// 1\. Create HttpClient object
HttpClient httpClient = HttpClient.newHttpClient();

// 2\. Create URI object
URI uri = URI.create(updateUrl);

// 3\. Build a request
HttpRequest request = HttpRequest.newBuilder(uri).GET().build();

// 4\. Send the request and get a HttpResponse object back!
//    Note: HttpResponse.BodyHandlers.ofString() just parses the response body
//          as a String
HttpResponse<String> response = httpClient.send(request, HttpResponse.BodyHandlers.ofString());
int statusCode = response.statusCode();
if (statusCode != 200) {
    throw new RuntimeException("Got non 200 response code! " + statusCode);
}
Instant timestamp = processIntoInstant(response.body()) 
```

Enter fullscreen mode Exit fullscreen mode

现在，这难道不比我们之前看到的`URlConnection`代码好得多吗？我们首先设置一个`HttpClient`对象，它将发送我们的请求。然后我们实例化一个`HttpRequest`对象，它包含请求方法、头等。我们使用之前创建的`HttpClient`发送`HttpRequest`，返回一个漂亮的`HttpResponse`对象。

`httpClient.send`中的第二个参数是一个`BodyHandler`，它是
负责将响应体解析成您想要的格式。Java 在`BodyHandlers`中提供了一些默认的，涵盖了常见的用例，比如解析到`String`、`File`和`InputStream`。当然，也有可能创建自己的，这本身就值得写一篇文章。

创建客户端、创建请求和接收响应的想法比使用`URlConnection`更加直观！`HttpClient`还支持异步请求、HTTP/2 和 websockets。这是从 8 升级到 11 的诱人理由！

*本文中使用的代码可以在[这里找到](https://github.com/AussieGuy0/trash-heap/blob/master/code/java/http/src/main/java/au/com/anthonybruno/http/Main.java)T3】*