# 尝试学习 Go - Building 开发日志第 4 部分

> 原文：<https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-04-2bok>

# 创建开发博客

看那个！我们又开始行动了。首先，我想对我在上一篇文章中收到的建设性评论表示感谢。我试着花时间考虑任何评论——毕竟，我还在学习中，所以我(不可否认)不是做的每件事都是正确的。我能确定的是在我机器上编译的代码。😜

* * *

## 后退一步

在查看了代码和注释之后，我意识到我可能对变量名过于简洁了。Go 通常以变量名短而著称，但它似乎有可能使代码更难理解。考虑到这一点，我已经开始使变量名更加明确。仍然有我使用短名字的情况，例如，`[]byte`用`b`。希望那些案例仍然非常清晰，容易理解，因为我试图确保它们在使用它们的地方附近被声明。

说了这么多，让我们看看我们在哪里。

```
package  main  import  (  "bufio"  "bytes"  "fmt"  "html/template"  "io"  "io/ioutil"  "os"  "regexp"  "strings"  "github.com/microcosm-cc/bluemonday"  "github.com/russross/blackfriday"  yaml  "gopkg.in/yaml.v2"  )  const  delimiter  =  "---"  type  post  struct  {  Title  string  Published  bool  Description  string  Tags  []string  CoverImage  string  Series  string  PostBody  template.HTML  }  type  index  struct  {  Pages  []Page  }  type  Page  struct  {  FileName  string  Title  string  } 
```

我已经添加了索引模板，所以我们可以为网站生成一个简单的索引页面。我们通过`Pages`传递我们的`index`结构和范围，以获得包含文件名和页面标题的每个`Page`。

```
var  indexTempl  =  `<!DOCTYPE html>
<html lang="en">
  <head>
    shindakun's dev site
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="HandheldFriendly" content="True">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="referrer" content="no-referrer-when-downgrade" />
    <meta name="description" content="shindakun's dev site" />
  </head>
  <body>
    <div class="index">
    {{ range $key, $value := .Pages }}
      <a href="/{{ $value.FileName }}">{{ $value.Title }}</a>
    {{ end }}
    </div>
  </body>
  </html>
`  var  postTempl  =  `<!DOCTYPE html>
<html lang="en">
  <head>
    {{.Title}}
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="HandheldFriendly" content="True">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="referrer" content="no-referrer-when-downgrade" />
    <meta name="description" content="{{.Description}}" />
  </head>
  <body>
    <div class="post">
      <h1>{{.Title}}</h1>
      {{.PostBody}}
    </div>
  </body>
  </html>
` 
```

[@ladydascalie](https://dev.to/ladydascalie) 在评论中指出，我们最好接受`io.Reader`这将允许我加载文件并轻松模拟该函数的测试。我们还重命名了该函数，使其更具描述性。我决定用`getContentsOf()`，所以在代码中它读作`getContentsOf(openedFile)`。

```
func  getContentsOf(r  io.Reader)  ([]byte,  error)  {  b,  err  :=  ioutil.ReadAll(r)  if  err  !=  nil  {  return  nil,  err  }  return  b,  nil  }  func  parseFrontMatter(b  []byte)  (map[string]interface{},  error)  {  fm  :=  make(map[string]interface{})  err  :=  yaml.Unmarshal(b,  &fm)  if  err  !=  nil  {  msg  :=  fmt.Sprintf("error: %v\ninput:\n%s",  err,  b)  return  nil,  fmt.Errorf(msg)  }  return  fm,  nil  }  func  splitData(fm  []byte)  ([][]byte,  error)  {  b  :=  bytes.Split(fm,  []byte(delimiter))  if  len(b)  <  3  ||  len(b[0])  !=  0  {  return  nil,  fmt.Errorf("Front matter is damaged")  }  return  b,  nil  } 
```

我还采纳了 [@krusenas](https://dev.to/krusenas) 的建议，实际上为`makePost()`做了一些处理。我们不会总是处理已知良好的文件，我们真的应该考虑到，我们不久就会有“坏”的 YAML。可能有一个不太冗长的方法来处理这个问题，但它目前是有效的。然而，这并没有做到！在这个系列的这一部分，我们至少还会有一个帖子，如果没有标题，我们需要拒绝这个文件。毕竟，如果没有标题，我们在索引中就没有链接。

```
// makePost creates the post struct, returns that and the template HTML  func  makePost(fm  map[string]interface{},  contents  []byte,  s  [][]byte)  (*template.Template,  *post)  {  post  :=  &post{}  // TODO: Reject post if we don't have a title  titleIntf,  ok  :=  fm["title"]  if  ok  {  title,  ok  :=  titleIntf.(string)  if  ok  {  post.Title  =  title  }  else  {  post.Title  =  ""  }  }  else  {  post.Title  =  ""  }  pubIntf,  ok  :=  fm["published"]  if  ok  {  published,  ok  :=  pubIntf.(bool)  if  ok  {  post.Published  =  published  }  else  {  post.Published  =  false  }  }  else  {  post.Published  =  false  }  descIntf,  ok  :=  fm["description"]  if  ok  {  description,  ok  :=  descIntf.(string)  if  ok  {  post.Description  =  description  }  else  {  post.Description  =  ""  }  }  else  {  post.Description  =  ""  }  tagsIntf,  ok  :=  fm["tags"]  if  ok  {  tags,  ok  :=  tagsIntf.(string)  if  ok  {  post.Tags  =  strings.Split(tags,  ", ")  }  else  {  post.Tags  =  []string{}  }  }  else  {  post.Tags  =  []string{}  }  covIntf,  ok  :=  fm["cover_image"]  if  ok  {  coverImage,  ok  :=  covIntf.(string)  if  ok  {  post.CoverImage  =  coverImage  }  else  {  post.CoverImage  =  ""  }  }  else  {  post.CoverImage  =  ""  }  seriesIntf,  ok  :=  fm["series"]  if  ok  {  series,  ok  :=  seriesIntf.(string)  if  ok  {  post.Series  =  series  }  else  {  post.Series  =  ""  }  }  else  {  post.Series  =  ""  }  pBody  :=  contents[len(s[1])+(len(delimiter)*2):]  out  :=  blackfriday.Run(pBody)  bm  :=  bluemonday.UGCPolicy()  bm.AllowAttrs("class").Matching(regexp.MustCompile("^language-[a-zA-Z0-9]+$")).OnElements("code")  post.PostBody  =  template.HTML(bm.SanitizeBytes(out))  tm  :=  template.Must(template.New("post").Parse(postTempl))  return  tm,  post  } 
```

我们的`writeIndex()`函数非常简单，我们可以扩展一下，所以我们只有一个写文件函数。我们在主循环中有基本相同的代码。目前，虽然一点额外的代码对我来说是可以的。

```
func  writeIndex(idx  index)  {  indexFile,  err  :=  os.Create("index.html")  if  err  !=  nil  {  panic(err)  }  defer  indexFile.Close()  buffer  :=  bufio.NewWriter(indexFile)  tm  :=  template.Must(template.New("index").Parse(indexTempl))  err  =  tm.Execute(buffer,  idx)  if  err  !=  nil  {  panic(err)  }  buffer.Flush()  }  func  main()  {  var  idx  index  dir,  err  :=  ioutil.ReadDir(".")  if  err  !=  nil  {  panic(err)  }  for  _,  file  :=  range  dir  {  if  fileName  :=  file.Name();  strings.HasSuffix(fileName,  ".md")  {  openedFile,  err  :=  os.Open(fileName)  if  err  !=  nil  {  panic(err)  }  contents,  err  :=  getContentsOf(openedFile)  if  err  !=  nil  {  panic(err)  }  s,  err  :=  splitData(contents)  if  err  !=  nil  {  panic(err)  }  fm,  err  :=  parseFrontMatter(s[1])  if  err  !=  nil  {  msg  :=  fmt.Sprintf("error: %v\ninput:\n%s",  err,  s[1])  panic(msg)  }  template,  post  :=  makePost(fm,  contents,  s)  trimmedName  :=  strings.TrimSuffix(fileName,  ".md")  outputFile,  err  :=  os.Create(trimmedName  +  ".html")  if  err  !=  nil  {  panic(err)  }  defer  outputFile.Close()  buffer  :=  bufio.NewWriter(outputFile)  err  =  template.Execute(buffer,  post)  if  err  !=  nil  {  panic(err)  }  buffer.Flush()  indexLinks  :=  Page{  FileName:  trimmedName  +  ".html",  Title:  post.Title,  }  idx.Pages  =  append(idx.Pages,  indexLinks)  }  }  writeIndex(idx)  } 
```

最后，本周我们没有太多的改变，只是做了一些清理和添加代码来编写索引文件。这使得代码相当完整，不完美，因为我们不能指定输入和输出目录，但仍然可用。无论如何，我这周的时间有点短，所以我会努力在下周完成我们最后的部分来结束这个系列的这一部分。我想知道在那之后我们应该处理什么...有什么想法吗？

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