# 尝试学习 Go - Building 开发日志第 3 部分

> 原文：<https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-03-7lk>

# 创建开发博客

我们再来一次！如果你一直在关注这些帖子，欢迎回来，如果不欢迎加入！这一次，我们将继续我们的工作，扩展我们放在一起创建一个静态站点生成器的基本代码。事实上，我们这周前进了不少。现在，我们可以将格式良好的 markdown 文件的整个目录转换成 HTML 格式，以便托管。无论如何，让我们继续努力学习围棋吧...

* * *

## 潜入

我仍然在尝试用不同的方式来展示我们更新的代码，但是现在，我认为代码库已经足够小了，我可以从头开始，一步一步来。我将掩饰自上周以来没有改变的部分，因此，如果你还没有读过，我建议你读一读！

现在，让我们跳到我们的第一个真正的变化。

```
package  main  import  (  "bufio"  "bytes"  "fmt"  "html/template"  "io/ioutil"  "os"  "regexp"  "strings"  "github.com/microcosm-cc/bluemonday"  "github.com/russross/blackfriday"  yaml  "gopkg.in/yaml.v2"  )  const  delim  =  "---"  type  post  struct  {  Title  string  Published  bool  Description  string  Tags  []string  CoverImage  string  Series  string  PostBody  template.HTML  }  var  templ  =  `<!DOCTYPE html>
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

## 给东西命名很难

这里我们开始第一个(小的)变化，功能和上周一样，但是我把它从`loadFile()`重命名为`getContents()`。它似乎能更准确地捕捉到它所做的事情。

```
func  getContents(f  *string)  ([]byte,  error)  {  b,  err  :=  ioutil.ReadFile(*f)  if  err  !=  nil  {  return  nil,  err  }  return  b,  nil  } 
```

在本节中，你应该注意到的一件事是，我试图确保我们将代码分解成独立的函数。这应该让我们的`main()`保持良好，这样我们就可以编写测试了。下面我们有`parseFM()`，它总结了我们的解组和返回我们前面的第一阶段的事情。它可能被更正确地称为`parseYAML()`，但是，我们现在让它保持原样。`isNil()`没有改变，仍然没有真正使用，我会在下一篇文章之前看到这一点，可能。

```
func  parseFM(b  *[]byte)  (map[string]interface{},  error)  {  m  :=  make(map[string]interface{})  err  :=  yaml.Unmarshal(*b,  &m)  if  err  !=  nil  {  msg  :=  fmt.Sprintf("error: %v\ninput:\n%s",  err,  b)  return  nil,  fmt.Errorf(msg)  }  return  m,  nil  }  func  isNil(i  interface{})  bool  {  if  i  !=  nil  {  return  false  }  return  true  } 
```

和以前一样，我们需要将从 markdown 文件中加载的数据分开。给`splitData()`打个电话就能搞定。我输入了各种格式错误的数据，而`if`语句似乎每次都能按预期工作。我还运行了我当前的一组 markdown [dev.to](https://dev.to/shindakun) 帖子，每一个都被顺利转换了。他们都可以在 dev.shindakun.net 的[找到。](https://dev.shindakun.net)

```
func  splitData(f  *[]byte)  ([][]byte,  error)  {  b  :=  bytes.Split(*f,  []byte(delim))  if  len(b)  <  3  ||  len(b[0])  !=  0  {  return  nil,  fmt.Errorf("Front matter is damaged")  }  return  b,  nil  } 
```

一旦我们拆分了数据并解析出前面的内容，我们就调用`makePost()`来构建 post 结构和模板。我将传递回`*post`,因为我们正在将文件写入磁盘之前执行模板，而不是在这个函数本身中。当我构建出一些东西来创建一个`index.html`的时候，这也是很方便的。再说一次，我们并没有像预期的那样使用`isNil()`，主要是因为我不喜欢它将产生的`if/else`混乱。我们总有一天会处理好的。

```
// makePost creates the post struct, returns that and the template HTML  func  makePost(fm  map[string]interface{},  contents  []byte,  s  [][]byte)  (*template.Template,  *post)  {  p  :=  &post{}  if  isNil(fm["title"])  {  panic("isNil tripped at title")  }  else  {  p.Title  =  fm["title"].(string)  }  p.Published  =  fm["published"].(bool)  p.Description  =  fm["description"].(string)  // TODO: Strip space after comma prior to parse?  tmp  :=  fm["tags"].(string)  p.Tags  =  strings.Split(tmp,  ", ")  p.CoverImage  =  fm["cover_image"].(string)  p.Series  =  fm["series"].(string)  pBody  :=  contents[len(s[1])+(len(delim)*2):]  out  :=  blackfriday.Run(pBody)  bm  :=  bluemonday.UGCPolicy()  bm.AllowAttrs("class").Matching(regexp.MustCompile("^language-[a-zA-Z0-9]+$")).OnElements("code")  p.PostBody  =  template.HTML(bm.SanitizeBytes(out))  tm  :=  template.Must(template.New("msg").Parse(templ))  return  tm,  p  } 
```

好了，我们进入了我们的`main()`功能！在蝙蝠的右边，你会看到我们现在使用`ioutil.ReadDir()`来获取当前目录的内容列表。

```
func  main()  {  d,  err  :=  ioutil.ReadDir(".")  if  err  !=  nil  {  panic(err)  } 
```

然后我们`range`遍历当前目录的列表，并立即检查当前文件是否以`.md`结尾。如果它成功了，我们的字符串的地址就被传递到`getContents()`中，如果一切顺利，我们就可以取回内容了。

```
 for  _,  f  :=  range  d  {  if  t  :=  f.Name();  strings.HasSuffix(t,  ".md")  {  contents,  err  :=  getContents(&t)  if  err  !=  nil  {  panic(err)  } 
```

我们将把指向我们内容的指针传递到`splitData()`中，然后将`s`的第二部分，也就是`s[1]`传递到`parseFM()`中。这为我们的帖子提供了基础构件。`splitData()`和`parseFM()`都可能被移到`makePost()`中，然后我们可以将内容直接传递给它，并在那里完成所有的工作。这是重构时要考虑的事情。

```
 s,  err  :=  splitData(&contents)  if  err  !=  nil  {  panic(err)  }  fm,  err  :=  parseFM(&s[1])  if  err  !=  nil  {  msg  :=  fmt.Sprintf("error: %v\ninput:\n%s",  err,  s[1])  panic(msg)  }  tm,  p  :=  makePost(fm,  contents,  s) 
```

在这个当前版本的代码中，我们将获取当前文件名并去掉扩展名。然后，我们将创建一个与原始文件同名的新文件，并追加`.html`。同样，这一切目前都发生在同一个目录中，我们真的很粗心，没有检查现有的文件或任何东西。 [`os.Create()`](https://golang.org/pkg/os/#Create) 使用`0666`(或 Windows 中的等效权限)作为文件权限，因此理论上我们应该能够毫无问题地写入和覆盖。这是我们可能没有做被认为是“正确”的事情的另一个例子——相反，我们只是在经历一个迭代过程。这让每个人都能看到代码是如何变化的，包括所有的缺点。

文件创建完毕，`o`，我们再用 [`bufio.NewWriter()`](https://golang.org/pkg/bufio/#NewWriter) 创建一个带缓冲区的“Writer”。然后，我们可以执行我们的模板，并直接写入文件，该文件在我们刷新缓冲区后关闭。

```
 fin  :=  strings.TrimSuffix(t,  ".md")  o,  err  :=  os.Create(fin  +  ".html")  if  err  !=  nil  {  panic(err)  }  defer  o.Close()  buf  :=  bufio.NewWriter(o)  err  =  tm.Execute(buf,  p)  if  err  !=  nil  {  panic(err)  }  buf.Flush()  }  }  } 
```

如果我们还有剩余的文件，我们将跳回到循环的顶部，重新做所有的事情！

* * *

## 下次

我们差不多一切就绪了！但是首先，我们需要创建并写出`index.html`。我想我会在接下来的一天左右实现它，并发布一个快速跟进的帖子。之后，我需要决定我们是否要在“开发站点”系列中再写一篇文章，并扩展代码以使用外部模板。应该没有那么多工作，我想我会等着看下周一会有多忙。下次见！

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