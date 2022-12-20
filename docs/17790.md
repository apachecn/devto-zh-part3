# 尝试学习 Go -再次通过 API 发送电子邮件

> 原文：<https://dev.to/shindakun/attempting-to-learn-go---sending-email-via-api-again-2e4e>

# 邮件列表

[ATLG](https://dev.to/shindakun/attempting-to-learn-go---rest-api-and-a-bit-on-templates-4kca)前情提要，我们看了如何使用 Go 模板快速使用标记文本。这一次，我们将充分利用这些新的格式化技巧！为了做到这一点，我们将把几个星期前我们放在一起的 [`mailgunner`包](https://dev.to/shindakun/attempting-to-learn-go---now-sending-rest-requests-akp)拉进来。我告诉过你我已经有计划了。

我们将直接进入，并在我们已经拥有的基础上开始建设，所以，如果你还没有阅读其他的 [两篇文章](https://dev.to/shindakun/attempting-to-learn-go---rest-api-and-a-bit-on-templates-4kca)，你可能想要花一点时间[阅读。](https://dev.to/shindakun/attempting-to-learn-go---now-sending-rest-requests-akp)

* * *

再一次从头开始！如果您一直在关注这个系列，那么对于我们的例子，您会发现我们是从当前的导入集合开始的。你可能会注意到一个新的标准库，这一次大约是在`errors`的时候，我们一会儿会谈到我们是如何使用它的。你可能还注意到，这一次我们去掉了大部分不需要的结构部分，只留下了带有`name`、`username`和`email`条目的`[]Users`。正如我上次说的，那篇文章只是为了演示，所以我把它省略了。

您可能还会注意到，我们从`mailgunner`包导入的内容略有不同，该包目前在 GitHub 上并不存在。有了开头的`c`，我们就可以像使用字母“c”一样使用 import，这样我们就可以输入`c.SomeFunc()`而不是使用`client`。*请注意，mailgunner 的代码将会稍作调整，并在下一篇文章后放在 GitHub 上。*

```
package  main  import  (  "bytes"  "encoding/json"  "errors"  "fmt"  "io/ioutil"  "net/http"  "text/template"  "github.com/shindakun/envy"  c  "github.com/shindakun/mailgunner/client"  )  const  apiurl  =  "https://api.mailgun.net/v3/youremaildomain.com"  type  Users  []struct  {  Name  string  `json:"name"`  Username  string  `json:"username"`  Email  string  `json:"email"`  } 
```

现在我们开始编写第一个真正的新代码。`checkStatusCode()`是一个小的实用程序(或助手)函数，我们用它来检查`http`响应代码是否是`200`。我们使用的端点应该只用 [`200 OK`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/200) 来响应我们的代码，否则我们将返回一个错误。你可能会问为什么我们要返回一个错误，而不是惊慌失措？这是因为我们希望代码在调用它的代码块中死机并退出，这样你就不用回溯了。稍后，我们还将研究如何优雅地处理错误，而不是惊慌失措。无论如何，总是检查响应代码是一个好主意——我们在前面的例子中跳过了它，但是，从现在开始我将努力记住这样做。也许我们会开始为可以重复使用的东西准备一个“实用工具”包。

```
func  checkStatusCode(s  int)  error  {  if  s  !=  200  {  err  :=  fmt.Sprintf("unexpected status code: %d",  s)  return  errors.New(err)  }  return  nil  } 
```

`sendEmail()`在某些方面类似于我们在`mailgunner`帖子中编写的发送测试消息的代码。然而，这一次，我们将传递我们的`bytes.Buffer`，它将保存我们完成的电子邮件模板和电子邮件地址。仔细看，你可能会注意到我交换了`mgc.FormatEmailRequest()`中的“发件人”和“收件人”字段，这样我就可以发送电子邮件了，但要发送到工作邮箱——请不要尝试发送示例邮件。他们可能都是假的，但仍然垃圾邮件是不礼貌的。

```
func  sendEmail(buf  bytes.Buffer,  email  string)  {  mgKey,  err  :=  envy.Get("MGKEY")  if  err  !=  nil  {  panic(err)  }  mgc  :=  c.NewMgClient(apiurl,  mgKey)  req,  err  :=  mgc.FormatEmailRequest(email,  "youremailaddress@",  "Test email",  buf.String())  if  err  !=  nil  {  panic(err)  }  res,  err  :=  mgc.Client.Do(req)  if  err  !=  nil  {  panic(err)  }  defer  res.Body.Close() 
```

在这里，我们使用我们的助手来检查返回的状态代码，如果我们看起来不太好，就会惊慌失措。最后，和到目前为止的每个例子一样，我们将结果打印到标准输出。

```
 if  err  =  checkStatusCode(res.StatusCode);  err  !=  nil  {  panic(err)  }  body,  err  :=  ioutil.ReadAll(res.Body)  if  err  !=  nil  {  panic(err)  }  fmt.Println(string(body))  } 
```

好了，现在让我们进入我们的`main()`！如果你读过以前的帖子，你会觉得这些看起来很熟悉。这种基础上的迭代是我所希望的，它能让我记住这一点，也许也能让你记住！

```
func  main()  {  APIURL  :=  "https://jsonplaceholder.typicode.com/users"  req,  err  :=  http.NewRequest(http.MethodGet,  APIURL,  nil)  if  err  !=  nil  {  panic(err)  }  client  :=  http.DefaultClient  resp,  err  :=  client.Do(req)  if  err  !=  nil  {  panic(err)  }  defer  resp.Body.Close()  if  err  =  checkStatusCode(resp.StatusCode);  err  !=  nil  {  panic(err)  }  body,  err  :=  ioutil.ReadAll(resp.Body)  if  err  !=  nil  {  panic(err)  }  var  u  Users  json.Unmarshal(body,  &u) 
```

我们的消息是相当平淡无奇的，用一个全功能的邮件列表应用程序，你可以很容易地加载自定义消息，等等。但是，一如既往，我们只是在尝试学习。如果我们对它进行足够的迭代，有一天它会很容易成为一个完整的应用程序。我们将`range`通过我们的用户，并将填充模板的结果传递给`sendEmail()`，后者将进行实际的发送。

```
 msgText  :=  "Hi {{.Name}}! Or should I call you, {{.Username}}? There is a new post!\n\n"  t  :=  template.Must(template.New("msg").Parse(msgText))  for  _,  v  :=  range  u  {  var  buf  bytes.Buffer  err  :=  t.Execute(&buf,  v)  if  err  !=  nil  {  panic(err)  }  sendEmail(buf,  v.Email)  }  } 
```

就是这样！下一次，我们将稍微调整一下我们的`mailgunner`包，并把它发布到 GitHub 上，这样我们就可以真正地`go get`它了！

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