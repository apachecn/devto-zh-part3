# 尝试学习 Go - REST API 和一些模板知识

> 原文：<https://dev.to/shindakun/attempting-to-learn-go---rest-api-and-a-bit-on-templates-4kca>

# 焦躁不安

之前，我们学习了如何通过 MailGun API 发送电子邮件。这一次，我们将更新几周前我们整理的代码，以便从 [jsonplaceholder](https://jsonplaceholder.typicode.com/) 站点吸引一组用户。一旦更新，我们会看看我们是否可以利用它，如果你读了最后一个帖子，你可能会猜到我们要做什么...

* * *

核心方法与我们上次做的非常相似。我们已经知道了将从端点接收的 JSON 对象的格式，所以我们将从创建我们的结构或更正确的结构开始。我想我们会把它分成小块，你可以看到下面，虽然它有我们需要的一切，但我认为它有点笨重。为了清楚起见，我们实际上不需要用传入的 JSON 填充整个结构。我们只需获得 ID、姓名、用户名和电子邮件，这就够了。我只是将所有内容作为一个例子，[GitHub](https://github.com/shindakun/atlg/blob/master/go-api-04/main.go)上的代码示例可能不会。

```
type  Users  []struct  {  ID  int  `json:"id"`  Name  string  `json:"name"`  Username  string  `json:"username"`  Email  string  `json:"email"`  Address  struct  {  Street  string  `json:"street"`  Suite  string  `json:"suite"`  City  string  `json:"city"`  Zipcode  string  `json:"zipcode"`  Geo  struct  {  Lat  string  `json:"lat"`  Lng  string  `json:"lng"`  }  `json:"geo"`  }  `json:"address"`  Phone  string  `json:"phone"`  Website  string  `json:"website"`  Company  struct  {  Name  string  `json:"name"`  CatchPhrase  string  `json:"catchPhrase"`  Bs  string  `json:"bs"`  }  `json:"company"`  } 
```

自上而下，我们将首先拔出`Address`

```
type  Address  struct  {  Street  string  `json:"street"`  Suite  string  `json:"suite"`  City  string  `json:"city"`  Zipcode  string  `json:"zipcode"`  Geo  struct  {  Lat  string  `json:"lat"`  Lng  string  `json:"lng"`  }  `json:"geo"`  } 
```

我有点想跳出`Geo`，但最终我认为它可以作为地址结构的一部分。现在我们就搬出`Company`。

```
type  Company  struct  {  Name  string  `json:"name"`  CatchPhrase  string  `json:"catchPhrase"`  Bs  string  `json:"bs"`  } 
```

最后是使用新创建的`Address`和`Company`结构的`Users`块。

```
type  Users  []struct  {  ID  int  `json:"id"`  Name  string  `json:"name"`  Username  string  `json:"username"`  Email  string  `json:"email"`  Address  Address  `json:"address"`  Phone  string  `json:"phone"`  Website  string  `json:"website"`  Company  Company  `json:"company"`  } 
```

迭代与我们从开始的[相同的基础代码的好处是我们只需要做微小的改变。我们将改变原来的变量`r`，将其命名为`u`，它将保存我们的`Users`。](https://github.com/shindakun/atlg/blob/master/go-api-02/main.go)

```
 var  u  Users  json.Unmarshal(body,  &u) 
```

好了，现在我们已经把用户存在内存中了，我们能对他们做些什么呢？让我们假设他们在我们的邮件列表中，并且希望每次有新的帖子出现时都能得到更新。从上次的内容中我们知道，通过 MailGun 发送电子邮件非常简单——事实上，我们有一个可以导入的包。就像我计划的那样！在我们开始之前...

* * *

## 模板化

让我们快速浏览一下 Go 模板。如果你做过任何 web 开发，你可能对把手或类似的模板系统很熟悉——这或多或少是一回事。我们可以使用 struct 来替换“标记”文本中的文本。这是一个在 [Golang 操场](https://play.golang.org/p/u5mDBNinT7r)上跑步的基本例子。让我们回顾一下我们正在做的事情。

```
package  main  import  (  "os"  "text/template"  )  type  Data  struct  {  Name  string  City  string  }  func  main()  {  o  :=  Data{"Steve",  "Portland"}  msgText  :=  "It's {{.Name}} from {{.City}}!"  t  :=  template.Must(template.New("msg").Parse(msgText))  err  :=  t.Execute(os.Stdout,  o)  if  err  !=  nil  {  panic(err)  }  } 
```

在这个例子中，你可以看到我们正在声明模板文本，`It's {{.Name}} from {{.City}}!`。然后我们用`template.Must()`包装`template.New().Parse()`。如果`.New()`或`.Parse()`失败`.Must()`将会死机。`t.Execute()`执行实际工作——在这种情况下，替换`{{}}`标记并将结果直接写入标准输出。你可以在 Golang 游乐场看到这一幕。剧透，它打印出`It's Steve from Portland!`。

我们可以使用我们的`Users`和模板非常快速地进行替换，最终得到一封漂亮的个性化电子邮件。

* * *

## 工作列表

回到我们的原始代码，我们将在现在更新的`Users`变量和`Unmarshal()`调用下添加模板代码的第一部分。

```
 msgText  :=  "To: {{.Email}}\nHi {{.Username}}! There is a new post!\n\n\n"  t  :=  template.Must(template.New("msg").Parse(msgText)) 
```

如你所见，我们使用了与模板示例非常相似的设置。在这里，我们用传入的对象中包含的内容替换 email 和 username。但是我们如何用我们的`Users`来使用它呢？在这种情况下，我们将只使用`for` / `range`进行循环。

```
 for  _,  v  :=  range  u  {  err  :=  t.Execute(os.Stdout,  v)  if  err  !=  nil  {  panic(err)  }  } 
```

在每次 for 循环中，`v`将保存其中一个`Users`的值。在这种情况下我们不需要索引，所以我们使用`_`告诉 Go 我们不关心这个变量。和前面的例子一样，我们调用`t.Execute()`，让它直接写入标准输出。最后，让我们看看我们更新的代码。

*完整代码列表*

```
package  main  import  (  "encoding/json"  "io/ioutil"  "net/http"  "os"  "text/template"  )  type  Address  struct  {  Street  string  `json:"street"`  Suite  string  `json:"suite"`  City  string  `json:"city"`  Zipcode  string  `json:"zipcode"`  Geo  struct  {  Lat  string  `json:"lat"`  Lng  string  `json:"lng"`  }  `json:"geo"`  }  type  Company  struct  {  Name  string  `json:"name"`  CatchPhrase  string  `json:"catchPhrase"`  Bs  string  `json:"bs"`  }  type  Users  []struct  {  ID  int  `json:"id"`  Name  string  `json:"name"`  Username  string  `json:"username"`  Email  string  `json:"email"`  Address  Address  `json:"address"`  Phone  string  `json:"phone"`  Website  string  `json:"website"`  Company  Company  `json:"company"`  }  func  main()  {  APIURL  :=  "https://jsonplaceholder.typicode.com/users"  req,  err  :=  http.NewRequest(http.MethodGet,  APIURL,  nil)  if  err  !=  nil  {  panic(err)  }  client  :=  http.DefaultClient  resp,  err  :=  client.Do(req)  if  err  !=  nil  {  panic(err)  }  defer  resp.Body.Close()  body,  err  :=  ioutil.ReadAll(resp.Body)  if  err  !=  nil  {  panic(err)  }  var  u  Users  json.Unmarshal(body,  &u)  msgText  :=  "To: {{.Email}}\nHi {{.Username}}! There is a new post!\n\n\n"  t  :=  template.Must(template.New("msg").Parse(msgText))  for  _,  v  :=  range  u  {  err  :=  t.Execute(os.Stdout,  v)  if  err  !=  nil  {  panic(err)  }  }  } 
```

不太令人兴奋的是，我们展示了如何嵌套结构，并介绍了一些基本的模板。这篇文章有点长了...下一次，我们将对其进行一点重构，并添加一些实际的邮件发送。之后呢，我还不确定！

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