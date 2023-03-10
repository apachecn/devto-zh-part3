# 你知道这 63 个 HTTP 状态码是什么意思吗？

> 原文：<https://dev.to/meet__chopra/do-you-know-what-these-http-status-codes-mean-117b>

当我在一个新产品 [Freshping](https://www.freshworks.com/website-monitoring/) 工作时，我开始了解 HTTP 状态，这是一个免费的网站监控工具，我对 HTTP 状态代码的数量非常感兴趣。我在 HN 上看到一些解释状态码的工具，想到这里分享一下。有趣的是，我发现了大约 63 种状态代码。不知道还有没有比这更多的。如果你找到了，一定要分享。

#### 1xx 代码

1xx 代码通常被称为信息响应，其中服务器接受请求并希望接收进一步的有效载荷/主体。

`100 Continue`
`101 Switching Protocols`
T2】

#### 2××成功

这通常意味着服务器已经收到、理解并接受了客户端的请求。

`200 OK`
`201 Created`
`202 Accepted`
`203 Non-authoritative Information`
`204 No Content`
`205 Reset Content`
`206 Partial Content`
`207 Multi-Status`
`208 Already Reported`
`226 IM Used`

#### 3××重定向

300 代码通常用于 URL 重定向。服务器发送包含元数据和 URI 引用列表的响应，用户或用户代理可以从中选择最优选的一个。

`300 Multiple Choices`
`301 Moved Permanently`
`302 Found`
`303 See Other`
`304 Not Modified`
`305 Use Proxy`
`307 Temporary Redirect`
`308 Permanent Redirect`

#### 4××客户端错误

此状态代码是在似乎存在由客户端引起的错误时使用的

`400 Bad Request`
`401 Unauthorized`
`402 Payment Required`
`403 Forbidden`
`404 Not Found`
`405 Method Not Allowed`
`406 Not Acceptable`
`407 Proxy Authentication Required`
`408 Request Timeout`
`409 Conflict`
`410 Gone`
`411 Length Required`
`412 Precondition Failed`
`413 Payload Too Large`
`414 Request-URI Too Long`
`415 Unsupported Media Type`
`417 Expectation Failed``418 I'm a teapot`

`422 Unprocessable Entity``423 Locked`
`424 Failed Dependency`

我知道这是太多的 4xx :D

#### 5××服务器错误

当服务器不能服务/满足请求时，使用 500 个状态代码。

`500 Internal Server Error`
`501 Not Implemented`
`502 Bad Gateway`
`503 Service Unavailable`
`504 Gateway Timeout`
`505 HTTP Version Not Supported`
`506 Variant Also Negotiates`
`507 Insufficient Storage`
`508 Loop Detected`
`510 Not Extended`
`511 Network Authentication Required`
T11】

在阅读了这么多状态代码之后，您现在对 HTTP 代码有了更多的了解。我试图给出所有代码的要点，因为描述每一个代码都太大了。有兴趣了解更多的人，这里有[链接](https://tools.ietf.org/html/rfc7231#section-6.2.1)

此外，我们在[fresh ing](https://www.freshworks.com/website-monitoring/)给新业务 [50 个免费的公共状态页面](https://www.freshworks.com/website-monitoring/)，一定要看看我们，让我知道你的想法。

希望再次见到你。再见！👋