# 再次尝试学习围棋——让我们模块化吧！

> 原文：<https://dev.to/shindakun/attempting-to-learn-go---lets-get-modular---again-10cd>

# 让我们再来看看模块化

在之前，我们发布了第一个模块版本的[邮件枪手](https://github.com/shindakun/mailgunner)套装。这一次，让我们看看发布新版本需要做些什么——我们将对代码做一些小的调整，更新自述文件，然后剪切一个新版本。

* * *

## 沟 http。某种程度上是默认客户端

使用`http.DefaultClient`的一个问题是它没有设置一些合理的默认值，就像它所说的，它基本上是一个准系统客户端。如果有人需要代理请求怎么办？还是设置超时？用我们之前的模块是做不到的。既然是这种情况，我们想让我们的模块不依赖于它。因此，我们将对它进行一点扩展，使它可以传入一个自定义客户端。我们真的自己改变了`New()`函数，所以，我在这里只展示它，参考[回购](https://github.com/shindakun/mailgunner)或[以前的文章](https://dev.to/shindakun/attempting-to-learn-go---lets-get-modular-390i)来看完整的代码。

我们最初的函数非常简单，只是返回一个包含两个字符串和`http.DefaultClient`的 struct。

```
type  MgClient  struct  {  MgAPIURL  string  MgAPIKey  string  Client  *http.Client  }  func  New(apiurl,  apikey  string)  MgClient  {  return  MgClient{  apiurl,  apikey,  http.DefaultClient,  }  } 
```

正如我所说的，我们不会改变结构本身——我们只是简单地更新`New()`来接受另一个参数。另外，请注意，我们现在返回了一个指向`MgClient`的指针，它可能应该在以前的版本中。但是，嘿，我们还在学习。我们的新参数被聪明地命名为`client`，它将接受`*http.Client`——我们稍后将讨论如何实际使用它。

```
func  New(apiurl,  apikey  string,  client  *http.Client)  *MgClient  {  if  client  ==  nil  {  client  =  http.DefaultClient  }  return  &MgClient{  apiurl,  apikey,  client,  }  } 
```

如果使用该模块的人拒绝创建自定义客户端并通过了`nil`，我们将返回使用`http.DefaultClient`。

```
 mgc  :=  mailgunner.New(apiurl,  mgKey,  nil) 
```

### 自定义客户端

好的，但是我们如何利用定制客户端呢？其实挺简单的！在调用`New()`之前，我们将创建一个名为`cc`的新变量，并给它`&http.Client{}`一个 10 秒的超时。

```
 cc  :=  &http.Client{  Timeout:  time.Second  *  10,  } 
```

有了这个漂亮的定制客户端，我们将把它作为新的第三个参数传递给`New()`。

```
 mgc  :=  mailgunner.New(apiurl,  mgKey,  cc) 
```

其他一切都像以前一样工作。🎉

### 走吧 v2

我们还没有准备好发布我们的新模块，首先，我们必须更新自述文件，为了简洁起见，我不会在这里转贴。之后，我们需要更新我们的`go.mod`文件来表明我们处于新的版本。我已经决定这是一个主要版本，因为我们已经改变了`New()`函数的签名。新的`go.mod`只是在后面加上了“v2”。

```
module github.com/shindakun/mailgunner/v2 
```

我们将运行 Git 命令，将所有内容推送到 Github。我们将从创建一个 v2 分支开始(您可能会在开始进行代码更改之前创建这个分支，不过我不是碰巧创建的)，添加我们的更改，然后将这些更改提交到那个分支。

```
git checkout -b v2
git add .
git commit -m "Updated New() to take http.Client so we don't have to rely on http.DefaultClient" 
```

到目前为止一切顺利。现在，我们将把我们的分支推到遥控器上，然后标记并推送标记。

```
git push --set-upstream origin v2
git tag v2.0.0
git push --tags origin v2 
```

现在，任何人都可以简单地导入带有`/v2`的模块，然后运行`go build`就可以开始工作了！

```
➜  GO111MODULE=on go build -v
github.com/shindakun/mailgunner/v2
github.com/shindakun/mailgunner/v2/example 
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

* * *

## 下次

我仍在考虑整理一系列的帖子，告诉大家我想如何在我自己的领域上建立一个“开发博客”。我只需要决定我想如何解决它。

在那之前...

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