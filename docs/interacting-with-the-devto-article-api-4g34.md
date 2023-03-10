# 与开发到文章 API 交互

> 原文：<https://dev.to/shindakun/interacting-with-the-devto-article-api-4g34>

# ATLG 边栏

## 好的

大家好！如果这是你的第一次，欢迎！如果不欢迎回来！本周我将在 Dev.to .上开始一个新的“系列”,所以我决定试着发挥我的肌肉记忆，编写一个可以与 API 交互的小工具。在这种情况下，我使用的是`dev.to/api/articles`端点。这与我想开始探索的另一个项目相吻合！为此，我手头需要一大块数据(如果有可能的话，我们会在另一篇侧边栏文章中再讨论这个问题)。

* * *

## 代码演练

```
package  main  import  (  "encoding/json"  "fmt"  "io/ioutil"  "net/http"  "sync"  "time"  ) 
```

如果你写围棋， [JSON-to-Go](https://mholt.github.io/json-to-go/) 将成为你的朋友。如果我们需要解组 JSON，我们可以简单地粘贴一个 JSON 的样本，它会给你一个结构。很好。我们实际上不需要整个`struct`,我们可以只留下`ID int32 'json:"id"'`,因为它是我们唯一使用的。我现在包括整个事情，我们可能会在未来使用它。

```
// Articles array JSON struct  type  Articles  []struct  {  TypeOf  string  `json:"type_of"`  ID  int32  `json:"id"`  Title  string  `json:"title"`  Description  string  `json:"description"`  CoverImage  string  `json:"cover_image"`  PublishedAt  time.Time  `json:"published_at"`  TagList  []string  `json:"tag_list"`  Slug  string  `json:"slug"`  Path  string  `json:"path"`  URL  string  `json:"url"`  CanonicalURL  string  `json:"canonical_url"`  CommentsCount  int  `json:"comments_count"`  PositiveReactionsCount  int  `json:"positive_reactions_count"`  User  struct  {  Name  string  `json:"name"`  Username  string  `json:"username"`  TwitterUsername  string  `json:"twitter_username"`  GithubUsername  interface{}  `json:"github_username"`  WebsiteURL  string  `json:"website_url"`  ProfileImage  string  `json:"profile_image"`  ProfileImage90  string  `json:"profile_image_90"`  }  `json:"user"`  } 
```

我开始把它摆出来，好像我们要把它作为一个可进口的包裹。在`main()`中，它可能大部分是内联完成的，但也许我们会将其充实到一个完整的开发 API 客户端中。我将不得不更仔细地看看 v0 API，看看它到底支持什么。

```
// DevtoClient struct  type  DevtoClient  struct  {  DevtoAPIURL  string  Client  *http.Client  }  // New returns our DevtoClient  func  New(apiurl  string,  client  *http.Client)  *DevtoClient  {  if  client  ==  nil  {  client  =  http.DefaultClient  }  return  &DevtoClient{  apiurl,  client,  }  } 
```

以这种方式格式化我们的请求可能有些矫枉过正，但是，首先，它让我们处于一个很好的位置。这些请求可以接受几个不同的参数，但是我们不关心这些参数。至少这次不会，我们只想要文章本身。

```
// FormatPagedRequest returns *http.Request ready to do() to get one page  func  (dtc  DevtoClient)  FormatPagedRequest(param,  paramValue  string)  (r  *http.Request,  err  error)  {  URL  :=  dtc.DevtoAPIURL  +  "articles/?"  +  param  +  "="  +  paramValue  fmt.Printf("%v\n",  URL)  r,  err  =  http.NewRequest(http.MethodGet,  URL,  nil)  if  err  !=  nil  {  return  nil,  err  }  return  r,  nil  }  // FormatArticleRequest returns http.Request ready to do() and get an article  func  (dtc  DevtoClient)  FormatArticleRequest(i  int32)  (r  *http.Request,  err  error)  {  URL  :=  fmt.Sprintf(dtc.DevtoAPIURL+"articles/%d",  i)  r,  err  =  http.NewRequest(http.MethodGet,  URL,  nil)  if  err  !=  nil  {  return  nil,  err  }  return  r,  nil  } 
```

这一次我在试验`sync.waitGroup`。 [WaitGroups](https://golang.org/pkg/sync/#WaitGroup) 允许我们开始一系列的 Goroutines，等待它们完成后再继续代码。当`getArticle()`作为 Goroutine 执行时，我们将在代码中看到更多。它实际上是从 API 获取文章并将其写入磁盘。通过这种方式，我们可以获得一组 30 个商品 id。当我们解析它们时，我们开始得到文章。一旦我们都收到了，我们才开始下一组。

```
func  getArticle(dtc  *DevtoClient,  i  int32,  wg  *sync.WaitGroup)  {  defer  wg.Done()  r,  err  :=  dtc.FormatArticleRequest(i)  if  err  !=  nil  {  panic(err)  }  resp,  err  :=  dtc.Client.Do(r)  if  err  !=  nil  {  panic(err)  }  defer  resp.Body.Close()  body,  err  :=  ioutil.ReadAll(resp.Body)  if  err  !=  nil  {  panic(err)  }  fileName  :=  fmt.Sprintf("%d.json",  i)  ioutil.WriteFile("./out/"+fileName,  body,  0666)  } 
```

够简单了。我们使用`http.DefaultClient`创建我们的客户端。我们提供了在将来需要时使用替代配置的能力。`doit`和`c`将控制我们的`for`回路和程序的主体。

```
func  main()  {  dtc  :=  New("https://dev.to/api/",  nil)  doit  :=  true  c  :=  1 
```

在每次运行中，通过循环，我们得到一页的文章。然后我们设置我们的`WaitGroup`和`articles`变量。一旦我们解组了 JSON 的文章，我们就获得了数组的长度。这个长度告诉`WaitGroup`要等待多少次。注意，我们称`defer wg.Done()`为`getArticle()`中的第一行。这将从总数`WaitGroup`中减去 1，让我们在完成后继续前进。当页面没有数据时，当前的 Dev.to article API 返回一个空数组，`[]`。我们检查是否有这样的响应，如果有就停止。

```
 for  doit  {  req,  err  :=  dtc.FormatPagedRequest("page",  fmt.Sprintf("%d",  c))  if  err  !=  nil  {  panic(err)  }  resp,  err  :=  dtc.Client.Do(req)  if  err  !=  nil  {  panic(err)  }  defer  resp.Body.Close()  body,  err  :=  ioutil.ReadAll(resp.Body)  if  err  !=  nil  {  panic(err)  }  var  wg  sync.WaitGroup  var  articles  Articles  json.Unmarshal(body,  &articles)  wg.Add(len(articles))  for  i  :=  range  articles  {  go  getArticle(dtc,  articles[i].ID,  &wg)  }  wg.Wait()  if  string(body)  !=  "[]"  {  c++  continue  }  doit  =  false  }  } 
```

* * *

## 包装完毕

开始了，第一个“侧边栏”！我可能会把这个代码添加到 GitHub 上的主“尝试学习围棋”报告中。现在想起来我还是要把上周贴在那边！

你在 Go 里做过 API 的工作吗？有什么你想做的不一样的吗？请在下面的评论中告诉我。建设性的意见总是受欢迎的！

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