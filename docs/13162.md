# 尝试学习 Go - Building 开发日志第 5 部分

> 原文：<https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-05-4mo1>

# 创建开发博客

欢迎回到可能是我们静态站点生成器的最后一次旅行。至少现在是这样！本周我们将添加一个非常基本的命令行功能。这将允许我们定位源目录和目标目录。我们还会看看在评论中出现的一些事情。让我们开始吧！

* * *

## 嵌套 If/Else

[@ladydascalie](https://dev.to/ladydascalie) 对`makePost()`函数进行评论，指出啰嗦的`if/else`语句。通过将`else`上移和移出`if`使其更容易阅读。这是一个对错误过于详细使得代码难以理解的例子。

### 原片段

```
 descIntf,  ok  :=  fm["description"]  if  ok  {  description,  ok  :=  descIntf.(string)  if  ok  {  post.Description  =  description  }  else  {  post.Description  =  ""  }  }  else  {  post.Description  =  ""  } 
```

### 重构的代码片段

```
descIntf,  ok  :=  fm["description"]  post.Description  =  ""  if  ok  {  if  desc,  ok  :=  descIntf.(string);  ok  {  post.Description  =  desc  }  } 
```

我把`post.Description` -esc 部分移到了接口的分配上。无论哪种方式最终都会奏效。我也开始去掉许多散落在代码中的`panic()`。很多人在那里，因为我不太确定我想如何处理这些情况，并决定快速失败是一个很好的第一步。在我们阅读代码的时候，我会对它们进行更多的评论。

* * *

# 常数

[@dirkolbrich](https://dev.to/dirkolbrich) 也对我的常数`delimiter`的使用留下了评论。当我第一次开始时，我想我们会接受不同的分隔符。我们不会添加，我们将继续与三破折号，像哲基尔。但是，通过将它传递到`splitData()`中，这个函数将更容易测试和理解。我也应该把它传入`makePost()`但是签名已经很长了所以，现在，我没有。还需要指出的是，我没有在常量名`const delimiter = "---"`上使用全部大写字母。对于许多语言，你可以在代码中使用`DELIMITER`来表示它是一个常量。我当时没有注意到，也没有注意到`go lint`。有意思。Go 有一些优秀的资源，所以我查看了有效的 Go 和 Go Wiki。事实证明常量并不都是大写的，Go 规定了混合大小写的用法。这有一定的道理。常量/变量(或函数，或结构等)。)以大写字母开始导出。我们不希望分隔符导出为小写。在 Go 代码库中，这条规则也有例外，但我不会以这种或那种方式进行争论。也许等我更好地掌握了这门语言...

* * *

## 代码演练

我们再一次从头开始，我会试着说出发生了什么变化。在顶部附近，我们添加了`flag`和`log`。`flag`所以我们可以使用命令行选项和`log`来处理格式错误。

```
package  main  import  (  "bufio"  "bytes"  "flag"  "fmt"  "html/template"  "io"  "io/ioutil"  "log"  "os"  "regexp"  "strings"  "github.com/microcosm-cc/bluemonday"  "github.com/russross/blackfriday"  yaml  "gopkg.in/yaml.v2"  )  const  delimiter  =  "---"  type  post  struct  {  Title  string  Published  bool  Description  string  Tags  []string  CoverImage  string  Series  string  PostBody  template.HTML  }  type  index  struct  {  Pages  []Page  }  type  Page  struct  {  FileName  string  Title  string  }  var  indexTempl  =  `<!DOCTYPE html>
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

我可以在《T2》中去掉`getContentsOf()`，使用`ioutil.ReadAll()`。我现在已经离开了它，因为我一直告诉自己我要写测试，这将是一个很容易测试的函数。我让它根据一个`if`中的错误状态返回。没有必要这样做，因为`ioutil.ReadAll()`已经符合回报，所以它可以返回。

```
func  getContentsOf(r  io.Reader)  ([]byte,  error)  {  return  ioutil.ReadAll(r)  }  func  parseFrontMatter(b  []byte)  (map[string]interface{},  error)  {  fm  :=  make(map[string]interface{})  err  :=  yaml.Unmarshal(b,  &fm)  if  err  !=  nil  {  msg  :=  fmt.Sprintf("error: %v\ninput:\n%s",  err,  b)  return  nil,  fmt.Errorf(msg)  }  return  fm,  nil  }  func  splitData(fm  []byte,  delimiter  string)  ([][]byte,  error)  {  b  :=  bytes.Split(fm,  []byte(delimiter))  if  len(b)  <  3  ||  len(b[0])  !=  0  {  return  nil,  fmt.Errorf("Front matter is damaged")  }  return  b,  nil  } 
```

`makePost()`是发生最大变化的地方。如开篇所述，这些变化是对上一篇文章中一个非常好的评论的直接回应。我在考虑拆分这个函数，因为实际上我们正在返回模板和`post{}`。从长远来看，首先填充 post 结构可能更清楚。然后将其输入不同的函数来填充模板。如果我们在“ATLG”系列的这一部分再有一篇文章的话，我可以解决一些简单的重构问题。

```
// makePost creates the post struct, returns that and the template HTML  func  makePost(fm  map[string]interface{},  contents  []byte,  s  [][]byte)  (*template.Template,  *post,  bool)  {  post  :=  &post{}  post.Published  =  false  pubIntf,  ok  :=  fm["published"]  if  ok  {  if  published,  ok  :=  pubIntf.(bool);  ok  {  post.Published  =  published  }  }  if  !post.Published  {  return  nil,  nil,  true  }  post.Title  =  ""  titleIntf,  ok  :=  fm["title"]  if  ok  {  if  title,  ok  :=  titleIntf.(string);  ok  {  post.Title  =  title  }  }  if  post.Title  ==  ""  {  return  nil,  nil,  true  }  post.Description  =  ""  descIntf,  ok  :=  fm["description"]  if  ok  {  if  description,  ok  :=  descIntf.(string);  ok  {  post.Description  =  description  }  }  post.Tags  =  []string{}  tagsIntf,  ok  :=  fm["tags"]  if  ok  {  if  tags,  ok  :=  tagsIntf.(string);  ok  {  post.Tags  =  strings.Split(tags,  ", ")  }  }  post.CoverImage  =  ""  covIntf,  ok  :=  fm["cover_image"]  if  ok  {  if  coverImage,  ok  :=  covIntf.(string);  ok  {  post.CoverImage  =  coverImage  }  }  post.Series  =  ""  seriesIntf,  ok  :=  fm["series"]  if  ok  {  if  series,  ok  :=  seriesIntf.(string);  ok  {  post.Series  =  series  }  }  pBody  :=  contents[len(s[1])+(len(delimiter)*2):]  bf  :=  blackfriday.Run(pBody)  bm  :=  bluemonday.UGCPolicy()  bm.AllowAttrs("class").Matching(regexp.MustCompile("^language-[a-zA-Z0-9]+$")).OnElements("code")  post.PostBody  =  template.HTML(bm.SanitizeBytes(bf))  tm  :=  template.Must(template.New("post").Parse(postTempl))  return  tm,  post,  false  } 
```

`writeIndex()`有点变了。请注意，我们现在接收一个目标目录字符串并返回一个错误。这比出现错误时惊慌失措要好得多。在使用自己的错误结构和更好的方法来发现和处理错误方面，我还需要做更多的工作。

```
func  writeIndex(idx  index,  destination  string)  error  {  indexFile,  err  :=  os.Create(destination  +  "/"  +  "index.html")  if  err  !=  nil  {  return  err  }  defer  indexFile.Close()  buffer  :=  bufio.NewWriter(indexFile)  tm  :=  template.Must(template.New("index").Parse(indexTempl))  err  =  tm.Execute(buffer,  idx)  if  err  !=  nil  {  return  err  }  buffer.Flush()  return  nil  }  func  main()  {  var  idx  index 
```

这里是命令行选项代码的开始部分。注意，我使用的是标准库`flag`包，而不是类似 [Cobra](https://github.com/spf13/cobra) 的东西。这使得它现在非常简单。只需要一个用于源目录和目标目录的选项。源目录将是我们所有降价文件的位置。目的地是我们将要保存生成的 HTML 的地方。注意，如果我们不能创建目录或者目录已经存在，我仍然使用`panic()`。我已经决定，如果目录现在已经在那里，最好还是退出。我也一直在尝试`log.Panicf()`，虽然在这次迭代中它只出现了一次。稍后我们可以添加开关来覆盖现有文件，或者先删除所有内容，等等。

```
 destination  :=  flag.String("destination",  "",  "destination folder")  source  :=  flag.String("source",  "",  "source directory")  flag.Parse()  if  _,  err  :=  os.Stat(*destination);  os.IsNotExist(err)  {  err  :=  os.Mkdir(*destination,  0777)  if  err  !=  nil  {  panic(err)  }  }  else  {  log.Panicf("error: destination '%s' already exists",  *destination)  }  _,  err  :=  ioutil.ReadDir(*destination)  if  err  !=  nil  {  panic(err)  }  srcDir,  err  :=  ioutil.ReadDir(*source)  if  err  !=  nil  {  panic(err)  } 
```

我们还交换了主循环中的一些`panic()`和`continue`。我们不希望这个过程失败，所以记录任何问题并继续下去才是正确的做法。`continue`退出循环，从顶部“继续”。

```
 for  _,  file  :=  range  srcDir  {  if  fileName  :=  file.Name();  strings.HasSuffix(fileName,  ".md")  {  openedFile,  err  :=  os.Open(fileName)  if  err  !=  nil  {  log.Println(fileName,  err)  continue  }  contents,  err  :=  getContentsOf(openedFile)  if  err  !=  nil  {  openedFile.Close()  log.Println(fileName,  err)  continue  }  openedFile.Close()  s,  err  :=  splitData(contents,  delimiter)  if  err  !=  nil  {  log.Println(fileName,  err)  continue  }  fm,  err  :=  parseFrontMatter(s[1])  if  err  !=  nil  {  msg  :=  fmt.Sprintf("%v\n",  err)  log.Println(fileName,  msg)  continue  } 
```

是的，我确实留了一些东西。如果我们不能创建输出文件或执行模板，我们就会陷入停顿。我认为如果一个人有问题，那么最好不要尝试继续。

```
 template,  post,  skip  :=  makePost(fm,  contents,  s)  if  !skip  {  trimmedName  :=  strings.TrimSuffix(fileName,  ".md")  outputFile,  err  :=  os.Create(*destination  +  "/"  +  trimmedName  +  ".html")  if  err  !=  nil  {  panic(err)  }  buffer  :=  bufio.NewWriter(outputFile)  err  =  template.Execute(buffer,  post)  if  err  !=  nil  {  panic(err)  }  buffer.Flush()  outputFile.Close()  indexLinks  :=  Page{  FileName:  trimmedName  +  ".html",  Title:  post.Title,  }  idx.Pages  =  append(idx.Pages,  indexLinks)  }  }  } 
```

如果索引出了问题，我无法决定是否要出错。现在，我把它作为一条对数线。

```
 if  len(idx.Pages)  >  0  {  err  :=  writeIndex(idx,  *destination)  if  err  !=  nil  {  log.Println(err)  }  }  } 
```

* * *

# 下次

我还没决定下次我们要看什么。我可以继续建设这个或者我们可以尝试一些新的东西。我在考虑为一个很老的项目写一个 Go 后端，这个项目是我很久以前做的。这是一个 PHP/HTML5 音乐播放器。我把它的一部分移植到 Glitch 上，你可以在 https://carnation-motion.glitch.me/看到它的一部分。如果虚拟机已关闭，可能需要一秒钟才能启动。我不认为这个版本会播放任何歌曲，但基本的用户界面已经就绪。我们将会看到下周我的心情如何。

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