# 与 Dev.to Article API 交互——一个简单的重试系统

> 原文：<https://dev.to/shindakun/interacting-with-the-devto-article-api---a-simple-retry-system-551m>

# ATLG 边栏

## 但是错误呢

像往常一样，我似乎没有时间了！本周，我想建立一个简单的重试系统。这将允许我们进行第二次尝试下载，并删除我们拥有的一些`panic()`。

* * *

## 更新

这次我不打算做完整的代码演练，因为我们没有做太多的改动。相反，我们将看看之前和之后的位，看到一些工作。

我们要做的第一件事是建立一个结构来保存我们想要重试的文章 id。

```
type Retry struct {
  IDs []int32
} 
```

Enter fullscreen mode Exit fullscreen mode

完美！如果我们说要重试 3 次或什么的，我们可以延长它。事实上，以我的经验，我们根本不需要它。我已经能够从 API 中取出每一篇公开文章，一点也不担心。但是，我敢肯定，它可能不会每次都运行顺利。

所以不用换回[第一部](https://dev.to/shindakun/interacting-with-the-devto-article-api-4g34)，这里是我们原来的`main()`。

```
func main() {
  dtc := New("https://dev.to/api/", nil)
  doit := true
  c := 1

  for doit {
    req, err := dtc.FormatPagedRequest("page", fmt.Sprintf("%d", c))
    if err != nil {
      panic(err)
    }
    resp, err := dtc.Client.Do(req)
    if err != nil {
      panic(err)
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
      panic(err)
    }

    var wg sync.WaitGroup
    var articles Articles

    json.Unmarshal(body, &articles)
    wg.Add(len(articles))

    for i := range articles {
      go getArticle(dtc, articles[i].ID, &wg)
    }
    wg.Wait()

    if string(body) != "[]" {
      c++
      continue
    }
    doit = false
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在是我们的更新版本，有所改动。

```
func main() {
  dtc := New("https://dev.to/api/", nil)
  doit := true
  c := 1 
```

Enter fullscreen mode Exit fullscreen mode

我们正在添加`retries`和`report`。第一个将保存我们想要第二次尝试获取的文章 id。第二个将保存第二次失败的所有 id，我们将输出到控制台。这次我们没有消除`main()`的任何恐慌。我认为我们可以扩展重试系统，在某个时候覆盖它。

```
 retries := Retry{}
  report := Retry{}

  for doit {
    req, err := dtc.FormatPagedRequest("page", fmt.Sprintf("%d", c))
    if err != nil {
      panic(err)
    }
    resp, err := dtc.Client.Do(req)
    if err != nil {
      panic(err)
    } 
```

Enter fullscreen mode Exit fullscreen mode

正如 VirgileMathieu 在 [Twitter 上指出的，我们目前正在`for`循环中使用`defer`。这可能不是最好的主意，并可能导致意想不到的后果。我们有几个选择来处理这个问题。首先，我们可以移除`defer`，只移除`.Close()`，或者我们可以将整个部分包装在一个匿名函数中。](https://twitter.com/VirgileMathieu/status/1109609005125652480) 

```
 defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
      panic(err)
    }

    var wg sync.WaitGroup
    var articles Articles
    err = json.Unmarshal(body, &articles)
    if err != nil {
      panic(err)
    }

    wg.Add(len(articles)) 
```

Enter fullscreen mode Exit fullscreen mode

我们将传入一个指向`retries`的指针，这样我们就可以在尝试`getArticle()`时保持它的最新状态。

```
 for i := range articles {
      getArticle(dtc, articles[i].ID, &retries, &wg)
    }
    wg.Wait()

    if string(body) != "[]" {
      c++
      continue
    }
    doit = false
  } 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们的主循环结束，我们将设置第二个`WaitGroup`。然后，我们将尝试获取我们可能错过的任何文章。也许值得在这里建立一个循环，一次处理 10 个左右。我会这样做并更新帖子。如果`retries`为空，我们也应该用`if`把这个部分包装起来。

```
 // Lets try to get the ones we couldn't before
  var wg sync.WaitGroup
  wg.Add(len(retries.IDs))

  for i := range retries.IDs {
    getArticle(dtc, retries.IDs[i], &report, &wg)
  }
  wg.Wait()

  fmt.Printf("Unable to grab the following articles: %v\n", report)
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 获取那些文章

现在来看看我们的原创`getArticles()`。还不算太糟，它完成了任务！我们想消除恐慌，让程序继续运行，即使我们遇到了错误。

```
func getArticle(dtc *DevtoClient, i int32, wg *sync.WaitGroup) {
  defer wg.Done()
  r, err := dtc.FormatArticleRequest(i)
  if err != nil {
    panic(err)
  }

  resp, err := dtc.Client.Do(r)
  if err != nil {
    panic(err)
  }
  defer resp.Body.Close()

  body, err := ioutil.ReadAll(resp.Body)
  if err != nil {
    panic(err)
  }
  fileName := fmt.Sprintf("%d.json", i)
  ioutil.WriteFile("./out/"+fileName, body, 0666)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要将签名更新为，因为我们现在传入了一个指针`*Retry`。其次，我们将更新我们的每个`err`检查，以更新 retries 结构并返回到`main()`。

```
func getArticle(dtc *DevtoClient, i int32, retries *Retry, wg *sync.WaitGroup) {
  defer wg.Done()
  r, err := dtc.FormatArticleRequest(i)
  if err != nil {
    retries.IDs = append(retries.IDs, i)
    return
  } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们添加了一个二级检查来查看我们是否遇到了超过 399 的`statusCode`。这将导致我们为任何返回[客户端或服务器错误](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)的文章添加文章 ID。

```
 resp, err := dtc.Client.Do(r)
  if err != nil || resp.StatusCode > 399 {
    retries.IDs = append(retries.IDs, i)
    return
  }

  defer resp.Body.Close()

  _, err = ioutil.ReadAll(resp.Body)
  if err != nil {
    retries.IDs = append(retries.IDs, i)
    return
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 下次

这一次到此为止。我们已经在这个代码示例上走得很远了，所以我将寻找一些其他的东西来使用。有什么想法吗？请在评论中告诉我！

* * *

你可以在 GitHub 上的 repo 中找到这个和大多数其他试图学习围棋的帖子的代码。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [【新达昆】](https://github.com/shindakun) / [ atlg](https://github.com/shindakun/atlg)

### 我在 dev.to 上发布的“尝试学习围棋”帖子的来源报告

<article class="markdown-body entry-content container-lg" itemprop="text">

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
| [发行人](https://dev.to/shindakun/attempting-to-learn-go-issuer-01-5f0k) |

…</article>

[View on GitHub](https://github.com/shindakun/atlg)

* * *

| 喜欢这篇文章吗？ |
| --- |
| 给我买杯咖啡怎么样？ |

* * *