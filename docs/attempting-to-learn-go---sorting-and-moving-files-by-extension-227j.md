# 尝试学习 Go -通过扩展名排序和移动文件

> 原文：<https://dev.to/shindakun/attempting-to-learn-go---sorting-and-moving-files-by-extension-227j>

# 出轨

原来我们[上周](https://dev.to/shindakun/attempting-to-learn-go---listing-files-by-extension-1n10)出轨了。这实际上是一个很好的起点。我们上次的目标是编写基于文件扩展名对文件进行排序的代码。再次感谢 [@ladydascalie](https://dev.to/ladydascalie) 的创意！

> *   Write a program to sort files in a folder by extension.

当我阅读最初的 ask 时，我认为我们应该对每个扩展“桶”中的文件进行分类和排序。事实证明，这并不是“必需的”。我的假设导致我写了很多额外的代码。所以，虽然最后我们确实得到了我们想要的，但是有一堆额外的代码来做一大堆其他的事情。哦好吧。

我意识到我也没有像应该的那样依赖标准库。我花了很多时间拆分一个文件名来获得扩展名，而`filepath.Ext()`本应该为我返回它。哦，好吧，再来一次。

当我们前进的时候，我会试着更好地留意这一点——这毕竟是尝试学习的意义所在。尽管这一周我没有尽全力，因为我没有给自己足够的时间去做我想做的编码练习。

## 六神无主

这一次，我们将处理问题的第二部分，也就是“奖金部分”。

> *   Let it sort them in the logical folder in the future ex:. In the document. Jpg in images, etc. ...

好的，让我们来考虑一下，我们已经有了扩展名和文件名的映射，我们应该怎么做呢？在这个版本中，我们将基于扩展“bucket”创建一个简单的 switch 语句！好的，这很好，但是我们是要在文件系统上还是在标准输出上这样做呢？我将把它解释为在文件系统上。我又开始假设了！我们将检查文件扩展名。然后，如果需要，我们将为每个目录创建目标目录。之后，我们将遍历桶中的实际文件，并将它们移动到新的位置。听起来很简单，让我们从几个小片段开始，我们将在新代码中应用它们。

* * *

## 创建目标目录

感谢`os.Mkdir()`我们可以创建一个目录。如果我们希望能够沿着一条路径创建一整套目录，那么`MkdirAll()`可能是一个选项。我们不想，至少现在，保持简单。

```
 err  :=  os.Mkdir(destination,  0777)  if  err  !=  nil  {  return  err  } 
```

* * *

## 移动文件

标准库再次覆盖了我们！`os.Rename(src, dest)`将通过用不同的路径重命名来“移动”文件。

```
 err  =  os.Rename("./file.md",  "./documents/file.go")  if  err  !=  nil  {  return  err  } 
```

这就是我们的新特性，让我们开始修改之前的代码。我们将删除打印到屏幕上的整个部分，因为不需要。然后插入一些新的函数来创建目录，然后移动文件。

* * *

## 代码演练

我们将跳过我们的进口货，直接切入正题！

```
package  main  import  (  "encoding/json"  "fmt"  "io/ioutil"  "os"  "strings"  ) 
```

我想我不需要把它分解成自己的函数，因为它可以放在主函数中。将`createDestination()`拆分出来，使得即将到来的`prepAndMove()`最终更具可读性。

```
func  createDestination(s  string)  error  {  if  _,  err  :=  os.Stat(s);  os.IsNotExist(err)  {  err  :=  os.Mkdir(s,  0777)  if  err  !=  nil  {  return  err  }  }  else  {  // already exists, probably, maybe, hopefully  return  nil  }  return  nil  } 
```

听起来很有希望！这与 tin 上显示的完全一样，它试图使用`os.Rename()`移动文件。

```
func  doMove(file,  dir  string)  error  {  err  :=  os.Rename(file,  dir+"/"+file)  if  err  !=  nil  {  return  err  }  return  nil  } 
```

是我们节目的新“核心”。它接收我们的扩展名和文件名字符串映射。现在我想我们可以发挥创意，制作第二张地图，就像在`map[string][]string`中一样。它可以保存要排序的目录以及该目录中的扩展名。然后我们可以循环遍历它，进行一系列嵌套循环和疯狂的操作。但是，对于一个可能有些过分的小实用程序，让我们坚持使用 switch 语句。我们总是可以在以后添加它，现在让我们坚持尝试解决“问”的问题。

因此，对于每个扩展桶，我们将`range`遍历并匹配扩展。一旦匹配，我们将尝试创建一个目标目录。如果一切顺利，我们将为该扩展名排列文件名，并将它们传递到`doMove()`。完成一个桶后，我们将继续下一个桶，直到我们完成所有的桶。为了简洁，我只包括了`documents`和`images`部分。

```
func  prepAndMove(files  map[string][]string)  {  for  i,  list  :=  range  files  {  switch  i  {  case  "text",  "txt",  "md":  dir  :=  "documents"  err  :=  createDestination(dir)  if  err  !=  nil  {  msg  :=  fmt.Sprintf("An error occured creating destiation.\n%s",  err)  fmt.Println(msg)  os.Exit(1)  }  for  j  :=  range  list  {  doMove(list[j],  dir)  }  case  "png",  "jpg",  "gif",  "webp":  dir  :=  "images"  err  :=  createDestination(dir)  if  err  !=  nil  {  msg  :=  fmt.Sprintf("An error occured creating destiation.\n%s",  err)  fmt.Println(msg)  os.Exit(1)  }  for  j  :=  range  list  {  doMove(list[j],  dir)  }  }  }  } 
```

第一位的`main()`一点都没变。我仍在考虑将我们的“出错后退出”代码放到一个单独的函数中。我们在很多地方使用类似的代码，这是值得的。当重构的时候，有一个！

```
func  main()  {  wd,  err  :=  os.Getwd()  if  err  !=  nil  {  msg  :=  fmt.Sprintf("An error occured getting the current working directory.\n%s",  err)  fmt.Println(msg)  os.Exit(1)  }  dir,  err  :=  ioutil.ReadDir(wd)  if  err  !=  nil  {  msg  :=  fmt.Sprintf("An error occured reading the current working directory.\n%s",  err)  fmt.Println(msg)  os.Exit(1)  } 
```

我可以把本杰明在评论中做的例子换过来，但这感觉有点像作弊。我很喜欢[中对`continue`](https://dev.to/ladydascalie/comment/92k2) 的使用，我会记住它要干净得多。我们删除了寻找没有扩展名的文件的代码。这是不需要的，因为我们不能排序。

```
 var  m  =  make(map[string][]string)  for  _,  file  :=  range  dir  {  if  !file.IsDir()  {  fileName  :=  file.Name()  ext  :=  strings.Split(fileName,  ".")  if  len(ext)  >  1  {  m[ext[len(ext)-1]]  =  append(m[ext[len(ext)-1]],  fileName)  }  }  }  prepAndMove(m)  } 
```

## 下次

这有点脱节，但完成了任务。它不是非常有用，因为我没有返回并更新它，以获取一个源目录或根目的地。我很高兴在我花了很短的时间来完成它的过程中，我把自己奉献给了它。

请在下面的评论中告诉我你会做什么改变或不同的做法！

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