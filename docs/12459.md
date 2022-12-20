# 尝试通过扩展学习围棋列表文件

> 原文：<https://dev.to/shindakun/attempting-to-learn-go---listing-files-by-extension-1n10>

# 你好世界

在[最后一篇文章](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-05-4mo1)的末尾，我注意到我们将暂时把静态站点生成器项目放在一边。我决定，为了让事情继续向前发展，我会每隔几个帖子就改变我们正在看的东西。 [@ladydascalie](https://dev.to/ladydascalie) 建议了几个我认为应该解决的练习。这一次我们将尝试第一个想法。

* * *

## 结束目标

> 写一个程序，在一个文件夹中按扩展名排序文件
> 
> *   After that, let it sort in the logical folder. In the document. Jpg in images, etc. ...

这一次我们将把重点放在第一点上。我们的想法是取一堆文件名(字符串)并按字母顺序打印到`standard out`。考虑到这一点，我决定从一段类似文件名的字符串开始。这是在某个地方有一个句点`.`的字符串。然后我们可以把这些弦片`range`穿过它们。在我们`range`的每一步中，我们都会在`.`处`strings.Split()`琴弦。如果`Split()`返回一个以上的元素，我们有一个扩展。扩展名是两到三个字符，但可以是任何数字。我们不做评判，并将接受最后一场`.`后的任何事情。扩展名和文件名将进入`map[string][]string`。我们可以把最终的地图想象成 JSON，看起来像这样:

```
{  "epub":  [  "lil-go-book.epub"  ],  "jpg":  [  "as23dsd.jpg"  ],  "md":  [  "README.md"  ],  "mp3":  [  "something.mp3"  ],  "pdf":  [  "go-in-action.pdf"  ],  "txt":  [  "asdf.txt",  "qwerwe.txt"  ]  } 
```

事实上，我将添加一个特性，在纯文本输出或 JSON 对象中打印列表。然后你可以把它传送到 [`jq`](https://stedolan.github.io/jq/) ，那可能会有用。

* * *

## 走吧

让我们来看看我们的第一次代码迭代！它遵循我在脑海中设计的模式，并如预期的那样工作——这是一个不错的尝试。

```
package  main  import  (  "fmt"  "strings"  )  func  main()  {  var  m  =  make(map[string][]string)  list  :=  []string{"no-ext",  "README.md",  "asdf.txt",  "qwe.rwe.txt",  "as23dsd.jpg",  "something.mp3",  "go-in-action.pdf",  "lil-go-book.epub"}  for  _,  s  :=  range  list  {  ext  :=  strings.Split(s,  ".")  if  len(ext)  >  1  {  m[ext[len(ext)-1]]  =  append(m[ext[len(ext)-1]],  s)  }  }  fmt.Printf("%v",  m)  } 
```

从这里开始，我添加了一个`if`语句来说明没有扩展名的文件。当我们这样做时，让我们添加一个`sort.Strings`,这样我们就可以按字母顺序打印每个组。虽然在这一点上我没有对扩展进行排序，但是这是以后的事情。你可以在下面的代码片段中看到我们的小调整。

```
...  for  _,  s  :=  range  list  {  ext  :=  strings.Split(s,  ".")  if  len(ext)  >  1  {  m[ext[len(ext)-1]]  =  append(m[ext[len(ext)-1]],  s)  }  if  len(ext)  ==  1  {  m["no-ext"]  =  append(m["no-ext"],  s)  }  sort.Strings(m[ext[len(ext)-1]])  }  fmt.Printf("%v",  m)  }  ... 
```

**编辑**为[指出](https://dev.to/detunized/comment/92ad)由[@失谐](https://dev.to/detunized)`sort.Strings()`不在最佳位置。因为在例子中它会触发每个循环，这不是我们最终想要的。

[![shindakun profile image](img/6daf0398bbaf4ee5f055e98994303ca7.png) ](/shindakun) [ Steve Layton ](/shindakun) [ ![twitter](img/82aa32a73d0a2eadd783a8a531ea2cc3.png) ](https://twitter.com/shindakun) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/shindakun) [<time datetime="2019-02-23T21:53:14Z">Feb 23</time>](/shindakun/comment/92al)

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN" "[http://www.w3.org/TR/REC-html40/loose.dtd">](http://www.w3.org/TR/REC-html40/loose.dtd%22%3E)

哈。你抓到我了！至少应该将`sort`移到打印功能中。这是一个糟糕的设计决策——我起初只是为了简单起见才把它放在那里，而且从来没有清理过它。这在只有几个文件的目录中并不重要，但在更大的目录中确实会影响性能。类似下面的内容可能是好的，并且仍然很容易理解。

```
func plainList(m map[string][]string, v []string) {
    for _, value := range v {
        sort.Strings(m[value])
        for _, file := range m[value] {
            fmt.Println(file)
        }
    }
} 
```

我想我可能会更新这篇文章，以确保它被称为清晰。

* * *

## 为真实而做

我们已经完成了基本程序！现在我们需要能够在实际的文件系统上运行它。为此，我们将使用`os`标准库和`reflect`。我们将在代码的这个迭代中添加几个不同的部分，所以让我们开始吧。

```
package  main  import  (  "fmt"  "io/ioutil"  "log"  "os"  "reflect"  "sort"  "strings"  ) 
```

在`main()`中，我们添加了`os.Getwd()`来获取用户当前的工作目录。如果由于某种原因我们不能确定它，我们会发出一条消息。请注意，我试图给出一个更详细的错误。我们也不慌反而`os.Exit()`。为什么？在这种情况下，用一个错误代码退出比用一个冗长的`panic()`更好。如果没有，我们将尝试读取目录，如果我们不能读取它，再次失败。我们还检查文件是否是一个目录并跳过，因为我们现在只看文件。我想，下次我们可以把它们归入“目录”组。

```
func  main()  {  wd,  err  :=  os.Getwd()  if  err  !=  nil  {  msg  :=  fmt.Sprintf("An error occured getting the current working directory.\n%s",  err)  fmt.Println(msg)  os.Exit(1)  }  dir,  err  :=  ioutil.ReadDir(wd)  if  err  !=  nil  {  msg  :=  fmt.Sprintf("An error occured reading the current working directory.\n%s",  err)  fmt.Println(msg)  os.Exit(1)  }  var  m  =  make(map[string][]string)  for  _,  file  :=  range  dir  {  if  !file.IsDir()  {  fileName  :=  file.Name()  ext  :=  strings.Split(fileName,  ".")  if  len(ext)  >  1  {  m[ext[len(ext)-1]]  =  append(m[ext[len(ext)-1]],  fileName)  }  if  len(ext)  ==  1  {  m["no-ext"]  =  append(m["no-ext"],  fileName)  }  sort.Strings(m[ext[len(ext)-1]])  }  } 
```

我们使用 [`reflect`](https://golang.org/pkg/reflect/#Value.MapKeys) 来获取扩展字符串的值。感谢上帝有 Go Docs！这将让我们把它们作为一个单独的列表打印出来，扩展名后面跟着每个组中的文件。

```
 values  :=  reflect.ValueOf(m).MapKeys()  for  i,  k  :=  range  values  {  fmt.Println(values[i])  for  _,  val  :=  range  m[k.String()]  {  fmt.Println(" -",  val)  }  }  } 
```

这似乎满足了基本计划的要求...

* * *

## 但等待还有更多

我们还没完呢！我们需要再做一次迭代。因为这篇文章已经有点长了，所以我们将向前跳过。我要补充几个我们上面提到的东西。一个输出 JSON 的开关，一个" plain" `ls`样式和上面暗示的嵌套样式。我们将从命令行读取输出格式，并使用一个简单的 switch 语句来选择正确的格式。我对变量名不是很明确，但它应该是可遵循的。

```
package  main  import  (  "encoding/json"  "fmt"  "io/ioutil"  "log"  "os"  "reflect"  "sort"  "strings"  ) 
```

在这次迭代中，我做的第一件事是将打印例程从主循环中抽出，放入它自己的函数中。然后，我为每种输出类型创建了两个打印函数。我打算试着变得聪明些，处理复杂的事情，但是只有一个“打印”功能。最后，我决定他们是足够不同的，有他们自己的程序是很好的。

```
func  plainList(m  map[string][]string,  v  []string)  {  for  _,  value  :=  range  v  {  for  _,  file  :=  range  m[value]  {  fmt.Println(file)  }  }  }  func  nestedList(m  map[string][]string,  v  []string)  {  for  i,  value  :=  range  v  {  fmt.Println(v[i])  for  _,  file  :=  range  m[value]  {  fmt.Println(" - ",  file)  }  }  } 
```

如果你看下三个`err`部分，你会发现它们或多或少是一样的。如果我把这个程序扩展到基础之外，可能有必要去掉这些部分。我想我们可以做一个`isOK()`类型的函数。该函数将检查错误，并在需要时退出或返回。

```
func  jsonList(m  map[string][]string)  {  j,  err  :=  json.Marshal(m)  if  err  !=  nil  {  msg  :=  fmt.Sprintf("An error occured formatting the JSON.\n%s",  err)  fmt.Println(msg)  os.Exit(1)  }  fmt.Printf("%s",  j)  }  func  main()  {  wd,  err  :=  os.Getwd()  if  err  !=  nil  {  msg  :=  fmt.Sprintf("An error occured getting the current working directory.\n%s",  err)  fmt.Println(msg)  os.Exit(1)  }  dir,  err  :=  ioutil.ReadDir(wd)  if  err  !=  nil  {  msg  :=  fmt.Sprintf("An error occured reading the current working directory.\n%s",  err)  fmt.Println(msg)  os.Exit(1)  }  var  m  =  make(map[string][]string)  for  _,  file  :=  range  dir  {  if  !file.IsDir()  {  fileName  :=  file.Name()  ext  :=  strings.Split(fileName,  ".")  if  len(ext)  >  1  {  m[ext[len(ext)-1]]  =  append(m[ext[len(ext)-1]],  fileName)  }  if  len(ext)  ==  1  {  m["no-ext"]  =  append(m["no-ext"],  fileName)  }  sort.Strings(m[ext[len(ext)-1]])  }  }  values  :=  reflect.ValueOf(m).MapKeys() 
```

为了按字母顺序打印扩展，我添加了这个快速循环。我们使用从`reflect`得到的`values`和一个扩展的有序列表。现在排序后的`extensions`被传递到我们的打印函数中。

```
 var  extensions  []string  for  _,  value  :=  range  values  {  extensions  =  append(extensions,  value.String())  }  sort.Strings(extensions) 
```

当程序执行时，我们检查命令行参数的数量。如果我们有不止一个，我们会检查它是否与其中一个案例相匹配。如果没有，我们打印使用说明。如果我们没有命令行参数，我们打印出嵌套的样式文件列表。

```
 if  len(os.Args)  >  1  {  switch  arg  :=  os.Args[1];  arg  {  case  "plain":  plainList(m,  extensions)  case  "nested":  nestedList(m,  extensions)  case  "json":  jsonList(m)  default:  fmt.Println("Usage: gls [plain|nested|json]")  }  }  else  {  nestedList(m,  extensions)  }  } 
```

* * *

## 下次

我们走吧！这篇文章有点长了，所以我们将推迟将文件分类到目录中的“额外目标”。这段代码将成为下一次的基础。与此同时，您会如何编写类似的程序呢？请在评论中告诉我！

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