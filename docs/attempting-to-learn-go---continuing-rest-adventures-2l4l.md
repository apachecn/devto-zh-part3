# 尝试学习围棋-继续休息冒险

> 原文：<https://dev.to/shindakun/attempting-to-learn-go---continuing-rest-adventures-2l4l>

# 继续冒险

[上次](https://dev.to/shindakun/attempting-to-learn-go---consuming-a-rest-api-5c7g)我们组装了一个小程序来查询一个 REST API 并打印出响应。不是非常有用，但仍然是一个很好的学习经历。这一次，我们将采取这一点，并实际上尝试做一些事情，希望我会在我们到达帖子的结尾之前弄清楚是什么。

* * *

## JSON 响应

为了使用来自`httpbin.org`的 JSON 响应，我们需要将它从 JSON 转换成 Go 可以使用的结构。使用我们上一篇文章的示例回复，我们可以走捷径在 [JSON-to-Go 站点](https://mholt.github.io/json-to-go/)创建我们的`struct`

这是我们最初的目标...

```
{  "args":  {},  "headers":  {  "Accept-Encoding":  "gzip",  "Connection":  "close",  "Host":  "httpbin.org",  "User-Agent":  "Go-http-client/1.1"  },  "origin":  "68.211.xx.xx",  "url":  "https://httpbin.org/get"  } 
```

我们自动生成的结果...

```
type  AutoGenerated  struct  {  Args  struct  {  }  `json:"args"`  Headers  struct  {  AcceptEncoding  string  `json:"Accept-Encoding"`  Connection  string  `json:"Connection"`  Host  string  `json:"Host"`  UserAgent  string  `json:"User-Agent"`  }  `json:"headers"`  Origin  string  `json:"origin"`  URL  string  `json:"url"`  } 
```

这是一个相当好的翻译，但是，我们并不希望网站对我们吐出什么。我们将稍微改变一下`Args`和`Header`部分，使它们成为一张地图。

```
type  HTTPBinResponse  struct  {  Args  map[string]string  `json:"args"`  Headers  map[string]string  `json:"headers"`  Origin  string  `json:"origin"`  URL  string  `json:"url"`  } 
```

映射基本上是一个键/值对，非常适合保存查询参数和头。这使得我们的结构使用起来更简单，如果需要的话，我们可以通过`Args`或者`Headers`循环。

我们只对上一篇文章做了一些小的改动，所以我不会从头再来一遍。首先，我们更新了要调用的 URL，使其包含两个查询参数。这样我们就可以使用新的`Args`地图了。

```
 APIURL  :=  "https://httpbin.org/get?arg1=one&arg2=two" 
```

在收到我们的响应后，我们创建一个类型为`HTTPBinResponse`的新变量`r`。这包含了我们为响应创建的结构。接下来，我们使用`json.Unmarshal`将 JSON 对象“解封”到我们的结构中。

```
 var  r  HTTPBinResponse  json.Unmarshal(body,  &r) 
```

如果左边是-右边是打印数据。我们将以几种不同的方式打印出来，首先是返回的完整结构和用户代理，最后是第二个参数。实际上，您可能不知道具体返回了哪些头(除了标准的 HTTP 头)或参数，但是因为这只是一个例子，所以我们就这样做了。

```
 fmt.Printf("%#v\n\n",  r)  fmt.Printf("%v\n\n",  r.Headers["User-Agent"])  fmt.Printf("%v\n",  r.Args["arg2"]) 
```

你可以在 GitHub 上的 repo 中找到这个和大多数其他试图学习围棋的帖子的代码。

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [【新达昆】](https://github.com/shindakun) / [ atlg](https://github.com/shindakun/atlg)

### 我在 dev.to 上发布的“尝试学习围棋”帖子的来源报告

<article class="markdown-body entry-content p-5" itemprop="text">

# 尝试学习围棋

在这里你可以找到我为尝试学习围棋而写的代码，这些代码是我在 [Dev.to](https://dev.to/shindakun) 上写的。

## 帖子索引

| 邮政 | 密码 |
| --- | --- |
| [制作下载器第 01 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-01-44gl) | - |
| [制作下载器第 02 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-02-2k7i) | - |
| [制作下载器第 03 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-03-2214) | - |
| [制作下载器第 4 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-04-3ln9) | - |
| [建造下载器第 05 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-05-44o) | - |
| [使用 REST API](https://dev.to/shindakun/attempting-to-learn-go---consuming-a-rest-api-5c7g) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-01/main.go) |
| [继续休息冒险](https://dev.to/shindakun/attempting-to-learn-go---continuing-rest-adventures-2l4l) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-02/main.go) |
| [现在发送休息请求](https://dev.to/shindakun/attempting-to-learn-go---now-sending-rest-requests-akp) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-03/main.go) |
| [REST API 和模板上的位](https://dev.to/shindakun/attempting-to-learn-go---rest-api-and-a-bit-on-templates-4kca) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-04/main.go) |
| [再次通过 API 发送电子邮件](https://dev.to/shindakun/attempting-to-learn-go---sending-email-via-api-again-2e4e) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-05/main.go) |
| [让我们模块化吧！](https://dev.to/shindakun/attempting-to-learn-go---lets-get-modular-390i) | [src](https://github.com/shindakun/mailgunner) |
| [让我们再一次模块化！](https://dev.to/shindakun/attempting-to-learn-go---lets-get-modular---again-10cd) | [src](https://github.com/shindakun/mailgunner) |
| [构建开发日志第 1 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-01-1c3m) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-01/main.go) |
| [构建开发日志第 2 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-02-179c) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-02/main.go) |
| [构建开发日志第 3 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-03-7lk) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-03/main.go) |
| [构建开发日志第 4 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-04-2bok) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-04/main.go) |
| [构建开发日志第 5 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-05-4mo1) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-05/main.go) |
| [按扩展名 01 列出文件](https://dev.to/shindakun/attempting-to-learn-go---listing-files-by-extension-1n10) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-sort-01/main.go) |
| [按扩展名 02 列出文件](https://dev.to/shindakun/attempting-to-learn-go---sorting-and-moving-files-by-extension-227j) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-sort-01/main.go) |
| [开发至 API 01](https://dev.to/shindakun/interacting-with-the-devto-article-api-4g34) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devtoapi-01/main.go) |
| [开发至 API 02](https://dev.to/shindakun/interacting-with-the-devto-article-api---again-sort-of-2o8g) | 参见上面的代码 |

</article>

[View on GitHub](https://github.com/shindakun/atlg)

* * *

| 喜欢这篇文章吗？ |
| --- |
| 给我买杯咖啡怎么样？ |

* * *