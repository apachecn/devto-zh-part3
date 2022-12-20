# 与 Dev.to Article API 交互——同样有点

> 原文：<https://dev.to/shindakun/interacting-with-the-devto-article-api---again-sort-of-2o8g>

# ATLG 边栏

## 清理

[欢迎回来](https://dev.to/shindakun/interacting-with-the-devto-article-api-4g34)本周，我们再次停留在“侧边栏”上，做了一些小的调整，使代码更具可读性。我还用前几篇帖子的代码更新了 [GitHub repo](https://github.com/shindakun/atlg) 。所以一切都应该是最新的！

* * *

## 重构 Go Go Go

我们将跳过完整的代码演练，因为只有几分钟的细节发生了变化。@ladydascalie 很好地指出了几个可以改进的小地方。我想我会很快地覆盖它们，试图把它们铭刻在我的脑海里，并把它们传递下去。第一个来自我的两个 URL“格式”函数。虽然它们“正确地”做了它们应该做的事情，但是它们有点冗长。

```
// FormatPagedRequest returns *http.Request ready to do() to get one page
func (dtc DevtoClient) FormatPagedRequest(param, paramValue string) (r *http.Request, err error) {
  URL := dtc.DevtoAPIURL + "articles/?" + param + "=" + paramValue
  fmt.Printf("%v\n", URL)
  r, err = http.NewRequest(http.MethodGet, URL, nil)
  if err != nil {
    return nil, err
  }
  return r, nil
}

// FormatArticleRequest returns http.Request ready to do() and get an article
func (dtc DevtoClient) FormatArticleRequest(i int32) (r *http.Request, err error) {
  URL := fmt.Sprintf(dtc.DevtoAPIURL+"articles/%d", i)
  r, err = http.NewRequest(http.MethodGet, URL, nil)
  if err != nil {
    return nil, err
  }
  return r, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

看到我们是如何从两个函数中返回`*http.Request, error`的了吗？注意到别的了吗？没错`http.NewRequest()`也回报`*http.Request, error`！这意味着我们可以删除我们的错误检查并返回新请求的结果。

```
// FormatPagedRequest returns *http.Request ready to do() to get one page
func (dtc DevtoClient) FormatPagedRequest(param, paramValue string) (*http.Request, error) {
  URL := dtc.DevtoAPIURL + "articles/?" + param + "=" + paramValue
  fmt.Printf("%v\n", URL)
  return http.NewRequest(http.MethodGet, URL, nil)
}

// FormatArticleRequest returns http.Request ready to do() and get an article
func (dtc DevtoClient) FormatArticleRequest(i int32) (*http.Request, error) {
  URL := fmt.Sprintf(dtc.DevtoAPIURL+"articles/%d", i)
  return http.NewRequest(http.MethodGet, URL, nil)
} 
```

Enter fullscreen mode Exit fullscreen mode

一点点的重构允许我们将错误检查提升一个级别，而不是对同样的事情检查两次。

* * *

## 看起来马没有犯错

第二个小调整是一个遗漏的错误检查的结果。我有点惊讶这不是在林挺抓到的。但是，我想这就是我们没有给我们的代码一个彻底的重读。

```
json.Unmarshal(body, &articles) 
```

Enter fullscreen mode Exit fullscreen mode

啊`if err != nil`！现在我们正在写一些 Go！

```
 err = json.Unmarshal(body, &articles)
    if err != nil {
      panic(err)
    } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 下次

就像我说的，这个星期又短又甜！实际上，我正在考虑用这个代码库至少再写一篇文章。我想清理所有的`panic()`，并尝试建立某种重试系统。我们可以使用一个 struct 来跟踪文章`ID`，最后一个 HTTP 错误代码，以及到目前为止的重试次数。如果`dtc.Client.Do(r)`产生的 HTTP 状态代码不是 200，我们可以把它添加到结构中重试。然后，我们将等待完整的运行，然后查看结构，如果需要的话，在整个范围内。简单。对吗？我想我们下周会看到。

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
| [发行人](https://dev.to/shindakun/attempting-to-learn-go-issuer-01-5f0k) |

…</article>

[View on GitHub](https://github.com/shindakun/atlg)

* * *

| 喜欢这篇文章吗？ |
| --- |
| 给我买杯咖啡怎么样？ |

* * *