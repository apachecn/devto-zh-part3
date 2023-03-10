# Apache Camel #2 -调用 Rest API

> 原文：<https://dev.to/djoleb/apache-camel-calling-rest-api-25en>

你好！

这是第二篇关于阿帕奇骆驼的文章，以后会有更多。这篇文章的目的是向人们展示 Camel 的真正威力，也许你们中的一些人会找到一个用例，在那里你可以使用这个框架，而不是一遍又一遍地写样板代码。

今天我们将使用 Camel 调用 API 端点并处理一些异常。

## 属国

[骆驼 http 依赖](https://mvnrepository.com/artifact/org.apache.camel/camel-http)

## 路线

### 调度程序路线

如果你还记得来自[简介](https://dev.to/djoleb/introduction-to-apache-camel-7m9)的话，这是一条调度器路由，它会每 120 秒触发一条消息，发送到“direct:httpRoute”路由。我用的是同步[【直接】](https://camel.apache.org/direct.html)组件，也有异步[【色达】](https://camel.apache.org/seda.html)组件。

```
@Override"
public void configure() {

    from("timer:scheduler?period=120000")
            .log("Scheduled job!")
            .to("direct:httpRoute");

} 
```

Enter fullscreen mode Exit fullscreen mode

### Http 路由

这是一个 HTTP 客户端路由，它将调用 bittrex rest API，获取加密货币并记录响应。

```
@Override
public void configure() {

    from("direct:httpRoute")
            .log("Http Route started")
            .setHeader(Exchange.HTTP_METHOD).constant(HttpMethod.GET)
            .to("https://api.bittrex.com/api/v1.1/public/getcurrencies")
            .log("Response : ${body}");

} 
```

Enter fullscreen mode Exit fullscreen mode

### 错误处理程序

如果你想涵盖一些例外，有两个选择。

1.  默认错误处理程序
2.  onException 子句来处理特定的类异常。

@ Override
public void configure(){

```
 errorHandler(deadLetterChannel("mock:errorHandler"));

    onException(HttpOperationFailedException.class)
            .log("${exception}")
            .to("mock:errorHandler");

    from("direct:httpRoute")
            .log("Http Route started")
            .setHeader(Exchange.HTTP_METHOD).constant(HttpMethod.GET)
            .to("https://api.bittrex.com/api/v1.1/public/getcurrencies")
            .log("Response : ${body}");

} 
```

Enter fullscreen mode Exit fullscreen mode

### 结果

[![](img/45c773e9d5de88c7b43ee273743b13ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--olAvdR2D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r2d94dl7rfecuqyqhn2i.png)

这是本教程的全部内容，如果有什么不清楚的地方，请随时联系我，我会很乐意回答任何问题。

谢谢！