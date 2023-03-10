# 尝试学习 Go - Building 开发日志第 1 部分

> 原文：<https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-01-1c3m>

# 创建开发博客

我想把我的 [dev.to](https://dev.to/shindakun) 帖子交叉发布到我自己的网站上已经有一段时间了，但是还没有决定我想怎么做。有几个不同的选择——我可以建立一个新的 Ghost 实例，也可以使用静态站点生成器。我爱鬼！我很难过地说，我最近并没有太多的工作。然而，将它用于我的“开发站点”似乎有些错误——尤其是当我认为该站点的主要焦点将是 Go 时。我只是很享受摆弄它。Gophercon 或半身像！(大概是胸围因为不确定自己能不能“去”，哈哈。)

* * *

## 要不要雨果

如果你碰巧了解“静态站点生成器”的世界，你可能听说过 [Hugo](https://gohugo.io/) 。Hugo 是最受欢迎的生成器之一，它恰好是用 go 编写的。我和 Hugo 玩了一会儿，甚至向文档站点提交了一个 pull 请求，但是我非常喜欢我接触到的这一点。然而，我认为使用 Hugo 将违背这些帖子的精神和目的。我们正试图编写代码来帮助我们学习更多，所以记住这一点，让我们...

* * *

## 不是雨果

好吧，所以我们决定我们的网站不使用 Hugo。那么，我们接下来要做什么？在这一点上我有点犹豫不决，所以我只是想尝试一些东西，然后我们看看会怎么样。我认为开发网站的第一次迭代将坚持成为一个静态网站，为此，我们将设置 Nginx 背后的一切，并使用我们自己的静态网站生成器将我们现有的 markdown 文件转换为 HTML。稍后，我们可能会切换到与 Go 程序托管，所以我们可以看看建立与 Go 的网络服务器。

* * *

## 可能会出什么差错

我们来分析一下。我们将从一个测试降价文件开始。首先，我们需要打开它。使用`ioutils.ReadFile()`非常简单，所以我们将构建一个小的`loadFile()`函数。它将接受一个字符串，在这种情况下，它将是一个文件的路径，并返回一个字节片或一个错误。这应该涵盖了第一个潜在的问题，即文件由于某种原因无法读取。

然后，我们需要分成两个部分-前面的问题和降价。在此之前，我们能做些什么来确保文件是“有效的”呢？让我们看看文件的预期格式。

```
---
title: "test  file"
published: false
description: "This  is  the  description  from  the  test  file"
tags: these, are, tags
cover_image: https://someimage
series: "Attempting  to  Learn  Go"
---
Post body goes here. 
```

所以我们有了一个开头，后面是一些 YAML，然后是结尾，以此来结束前面的内容。我们想要一个快速的测试来确保文件有一个有效的前端内容部分，在考虑了一会儿之后，我认为最快的方法是使用`bytes.Split()`传入我们的字节片和前端内容分隔符`---`。分割应该给我们一个切片切片，这是一个有效的文件应该至少有 3 的长度。第一个(`b[0]`)应该是空的，第二个(`b[1]`)将包含构成我们前面内容的`[]byte`，任何剩余的位应该是我们文章主体的剩余部分。

* * *

## 获取编码

像往常一样，我们将申报我们的包裹和进口货物。我们使用了一些来自 Go 标准库的非常基本的东西，以及一个用于处理 front matter 头中的 YAML 的导入。Go 标准库不支持 YAML，所以我基本上只是谷歌了一下“golang yaml”并点击了第一个 GitHub 链接。

```
package  main  import  (  "bytes"  "fmt"  "io/ioutil"  "strings"  yaml  "gopkg.in/yaml.v2"  ) 
```

让我们创建我们的定界符常量。我们也知道了 front matter header 的基本构成，现在让我们为它创建一个结构。

```
const  delim  =  "---"  type  post  struct  {  title  string  published  bool  description  string  tags  []string  coverImage  string  series  string  } 
```

对于这一步，我们只有一个在`main()`之外的函数，那就是`loadFile()` func。我们可能会在接下来的几个版本中扩展它，这也是它不仅仅停留在`main()`的唯一原因，但是现在我们会让它简短而甜蜜。这是不言而喻的，因为[标准库](https://golang.org/pkg/io/ioutil/#ReadFile)为我们做了所有繁重的工作。

```
func  loadFile(s  string)  (b  []byte,  err  error)  {  f,  err  :=  ioutil.ReadFile(s)  if  err  !=  nil  {  return  nil,  err  }  return  f,  nil  } 
```

* * *

## Crusin 降总管

我们首先调用`loadFile()`将我们的测试文件加载到内存中。

```
func  main()  {  f,  err  :=  loadFile("test.md")  if  err  !=  nil  {  panic(err)  } 
```

现在，我们将尝试快速和肮脏的方法来验证文件。

```
 b  :=  bytes.Split(f,  []byte(delim))  if  len(b)  <  3  ||  len(b[0])  !=  0  {  panic(fmt.Errorf("Front matter is damaged"))  } 
```

你在下面看到的第一件事是我们正在制作一张新地图。这是把前面事关 YAML 的资料从文件的最上面扯下来的。我不会公正地解释接口——所以，我推荐阅读[乔丹·奥雷利的*如何在 Go*](http://jordanorelli.com/post/32665860244/how-to-use-interfaces-in-go) 帖子中使用接口，它做得很好。我们将直接把我们的`b[1]`输入其中。这样做的好处是我们知道格式不正确的 YAML 会抛出一个错误。我肯定有一些边缘情况，但我有足够的信心，这个版本的代码，它将是好的。我们将简单地使用`yaml.Unmarshal()`来创建我们的地图。因此 post 对象可能看起来像这样，`&post{title:"test file", ...}`。

```
 m  :=  make(map[string]interface{})  err  =  yaml.Unmarshal([]byte(b[1]),  &m)  if  err  !=  nil  {  msg  :=  fmt.Sprintf("error: %v\ninput:\n%s",  err,  b[1])  panic(msg)  } 
```

我们现在创建一个空结构`p`,我们将使用它来保存我们的 post 数据。稍后，我们将使用这个帖子数据来归档到我们的 HTML 模板中。我们不能只使用`m["title"]`并将其赋给我们的结构，因为目前所有东西都有一个`interface{}`类型，所以我们将使用`.(type)`断言该类型。您也可以使用类似于`fmt.Sprintf("%v", m["title"])`的东西将接口转换为适当的类型，在本例中为 string。我们对标签采取了额外的步骤，以确保我们最终得到的是一个切片，而不是一串逗号分隔的值。这一次，我们只是将我们的结构打印到标准输出，以验证它看起来像预期的那样。

```
 p  :=  &post{}  p.title  =  m["title"].(string)  p.published  =  m["published"].(bool)  p.description  =  m["description"].(string)  // TODO: Strip space after comma prior to parse?  tmp  :=  m["tags"].(string)  p.tags  =  strings.Split(tmp,  ", ")  p.coverImage  =  m["cover_image"].(string)  p.series  =  m["series"].(string)  fmt.Printf("%#v\n",  p)  } 
```

现在，这段代码中至少有一个我们需要考虑的主要缺陷——目前，它假设任何加载的文件都有确切的 front matter 头。如果有人试图加载一个没有`series`标签的降价文件，这会导致它死机。我们将构建一个函数来检查一个值，并在下一篇文章中返回它。

```
➜  go run main.go
&main.post{title:"test file", published:false, description:"This is the description from the test file", tags:[]string{"these", "are", "tags"}, coverImage:"https://someimage", series:"Attempting to Learn Go"} 
```

* * *

## 下次

这是下次要考虑的事情。使用我们加载的文件，提取文章正文的一种方法是什么？我们将讨论这一点，并在输出加载的降价时重新查看模板。

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