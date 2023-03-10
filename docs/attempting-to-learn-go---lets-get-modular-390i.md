# 尝试学习围棋-让我们模块化！

> 原文：<https://dev.to/shindakun/attempting-to-learn-go---lets-get-modular-390i>

# 让我们来模块化

还记得这个吗？

```
mkdir $GOTPATH/github.com/shindakun/mailgunner
mkdir $GOTPATH/github.com/shindakun/mailgunner/client 
```

如果你没有[看过之前关于通过 MailGun 发送电子邮件的帖子](https://dev.to/shindakun/attempting-to-learn-go---now-sending-rest-requests-akp)，你可能不会。这实在是太草率了，我这样做只是为了通过一个适当的例子使在本地使用这个包更容易一点。我不太喜欢它，因此它仍然没有被发布在 GitHub 上。所以，我想我们应该做一个快速的贴子，让`mailgunner`成为一个合适的 Go 模块。

* * *

## 准备工作

我们要做的第一件事是在`$GOPATH`之外创建一个目录。你可以在环境变量`GO111MODULE=on`内部使用模块，但是，因为这是关于去除`$GOPATH`的，我们将在它的外部工作。

```
mkdir ~/Code/mailgunner 
```

现在，创建一个`main.go`并在 VS 代码中打开它。

```
touch main.go
code . 
```

代码已经写好了，所以我把它粘贴进去。注意，我已经将包名从`client`更新为`mailgunner`。我还向导出的函数添加了简单的注释。这给了我们一些在 VS 代码中使用智能感知的细节。不过，我可能应该让它们更有用一点。

[![Intellisense in Action](img/8fa4d5c04d1733b5bb99f5aad7ef1f34.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VCInIM7i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/prw18wfddl76nb7mzcu7.png)

我不打算再重复代码，只是在这里提供它，这样你就不必跳到回购或上一篇文章。*注意:我在事后对代码做了一点小小的调整，并将`NewMgClient`函数重命名为更正确的`New()`。

```
package  mailgunner  import  (  "net/http"  "net/url"  "strings"  )  // MgClient struct holds our URL and API key  type  MgClient  struct  {  MgAPIURL  string  MgAPIKey  string  Client  *http.Client  }  // New returns our MgClient with the proper settings  func  New(apiurl,  apikey  string)  MgClient  {  return  MgClient{  apiurl,  apikey,  http.DefaultClient,  }  }  // FormatEmailRequest puts everything together for sending  func  (mgc  *MgClient)  FormatEmailRequest(from,  to,  subject,  body  string)  (r  *http.Request,  err  error)  {  data  :=  url.Values{}  data.Add("from",  from)  data.Add("to",  to)  data.Add("subject",  subject)  data.Add("text",  body)  r,  err  =  http.NewRequest(http.MethodPost,  mgc.MgAPIURL+"/messages",  strings.NewReader(data.Encode()))  if  err  !=  nil  {  return  nil,  err  }  r.SetBasicAuth("api",  mgc.MgAPIKey)  r.Header.Add("Content-Type",  "application/x-www-form-urlencoded")  return  r,  nil  } 
```

为了使它成为一个合适的模块，我们需要初始化它。

```
$ go mod init github.com/shindakun/mailgunner
go: creating new go.mod: module github.com/shindakun/mailgunner 
```

现在，让我们设置我们的 Git 回购。如果你不熟悉 Git，我建议你花点时间去看看。 [Pro Git](https://git-scm.com/book/en/v2) 在线免费提供。

```
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/shindakun/mailgunner.git
git push -u origin master 
```

现在，为了充分利用 Go 模块，我们需要用版本号来标记我们的版本。我们将遵循[语义版本化](https://semver.org/)，将我们的发布标记为版本 1 或`v1.0.0`，并将标记推送到 Github。

```
git tag v1.0.0
git push --tags 
```

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [辛达坤](https://github.com/shindakun) / [邮枪手](https://github.com/shindakun/mailgunner)

### Go 中一个超级简单的只发送邮件的客户端实现。

<article class="markdown-body entry-content p-5" itemprop="text">

# 尾部炮手

Go 中一个超级简单的只发送的 [Mailgun](https://www.mailgun.com/) 客户端的实现。

## 装置

用作模块，导入`github.com/shindakun/mailgunner/v2`，然后运行

```
go mod init module
go build
```

如果你是模块新手，请查看我在 [Dev.to 上的帖子，了解更多细节](https://dev.to/shindakun/attempting-to-learn-go---lets-get-modular-390i)。

```
go get github.com/shindakun/mailgunner
```

## 使用

有关使用示例，请参见 [example/main.go](https://raw.githubusercontent.com/shindakun/mailgunner/master/./example/main.go) 。

## 更改日志

*   更新了`New()`走 http。客户端，所以我们不必依赖`http.DefaultClient`

</article>

[View on GitHub](https://github.com/shindakun/mailgunner)

## 使用模块

好的，很好——但是，我们如何使用它呢？Git repo 包含一段示例代码。继续将回购克隆到您的`$GOPATH`之外的目录中。导航到示例目录。然后我们将初始化它以使用该模块。最后，运行`go build`，它看起来会像下面这样。

```
$ go mod init example
go: creating new go.mod: module example
$ go build
go: finding github.com/shindakun/mailgunner v1.0.0
go: downloading github.com/shindakun/mailgunner v1.0.0 
```

看，在那——不再使用`go get`！

## 下次

我想花点时间看看 Go 中的 web 服务器，所以我们可能会看看如何制作简单的服务器。它们可能还没有准备好投入生产，但是把它们放到网上去看它们的运行应该还是很有趣的。我有一些愚蠢的项目尝试和工作的其他想法，我可能会先去，我们看看会发生什么。

直到那时...

* * *

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
| [制作下载器第 05 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-05-44o) | - |
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