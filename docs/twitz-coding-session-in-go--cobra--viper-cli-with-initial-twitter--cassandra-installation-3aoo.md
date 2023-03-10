# Go–Cobra+Viper CLI 中的 Twitz 编码会话，初始安装 Twitter + Cassandra

> 原文：<https://dev.to/adron/twitz-coding-session-in-go--cobra--viper-cli-with-initial-twitter--cassandra-installation-3aoo>

*第 2 部分，共 3 部分*-Go-Cobra+Viper CLI 中的编码会话，用于解析文本文件、检索 Twitter 数据、导出到各种文件格式以及导出到 Apache Cassandra。

**更新部分:(转到原始复合代码博客文章)**

1.  用于解析文本文件的 Go - Cobra + Viper CLI 中的 Twitz 编码会话
2.  [在 Go - Cobra + Viper CLI 中的 Twitz 编码会话，初始安装 Twitter + Cassandra(本文)](http://compositecode.blog/2018/12/07/twitz-coding-session-in-go-cobra-viper-cli-with-initial-twitter-cassandra-installation/)
3.  [Go-Cobra 中的 Twitz 编码会话+ Viper CLI 总结+ Twitter 数据检索](http://compositecode.blog/2018/12/27/twitz-coding-session-in-go-cobra-viper-cli-wrap-up-twitter-data-retrieval/)

当我发布的时候，每个部分的更新链接将会发布在这篇文章的底部。对于代码，可以在视频和时间戳下面向下滚动。

[https://www.youtube.com/embed/YQ7eeErPNY4](https://www.youtube.com/embed/YQ7eeErPNY4)

组装一个 CLI 安装 Cassandra，设置 Twitter API，ENV Vars 等等。

[0:04](https://www.youtube.com//watch?v=YQ7eeErPNY4&t=4s) 踢屁股介绍。只是标准的摇滚曲调。

[3:40](https://www.youtube.com//watch?v=YQ7eeErPNY4&t=220s) 快速回顾一下。查看之前在 Go - Cobra + Viper CLI 中编写的" [Twitz 编码会话，以解析本系列的文本文件](http://compositecode.blog/2018/12/04/twitz-coding-session-in-go-cobra-viper-cli-for-parsing-text-files/)。

[4:30](https://www.youtube.com//watch?v=YQ7eeErPNY4&t=270s) 开始完成导出到 XML、JSON 和 CSV 的`twitz parse`命令(在之前的会话中已经完成了文本导出)。这一部分还包括一些重构，以清理功能，打破控制结构，使代码更具可读性。

在重构的最后`twitz parse`就这样出来了。通过调用实际位于 helpers.go 文件中的`buildTwitterList()`函数将完整的列表放在一起。然后按原样打印出该列表，并检查是否应该进行文件导出。如果为文件导出设置了配置设置，那么该过程从调用`exportParsedTwitterList(exportFilename string, exportFormat string, ... etc ... )`开始。然后是一个简单的单级控制 if then else 结构，以确定将数据导出到哪种格式，并调用相应的导出函数来进行数据的实际导出，并将文件写入底层系统。还可以做更多的重构，但是现在，考虑到我开始时代码的飞溅，这已经清理得很好了。

```
var  parseCmd  =  &cobra.Command{  Use:  "parse",  Short:  "This command will extract the Twitter Accounts form a text file.",  Long:  `This command will extract the Twitter Accounts and clean up or disregard other characters 
or text around the twitter accounts to create a simple, clean, Twitter Accounts only list.`,  Run:  func(cmd  *cobra.Command,  args  []string)  {  completedTwittererList  :=  buildTwitterList()  fmt.Println(completedTwittererList)  if  viper.Get("fileExport")  !=  nil  {  exportParsedTwitterList(viper.GetString("fileExport"),  viper.GetString("fileFormat"),  completedTwittererList)  }  },  }  func  exportParsedTwitterList(exportFilename  string,  exportFormat  string,  twittererList  []string)  {  if  exportFormat  ==  "txt"  {  exportTxt(exportFilename,  twittererList,  exportFormat)  }  else  if  exportFormat  ==  "json"  {  exportJson(exportFilename,  twittererList,  exportFormat)  }  else  if  exportFormat  ==  "xml"  {  exportXml(exportFilename,  twittererList,  exportFormat)  }  else  if  exportFormat  ==  "csv"  {  exportCsv(exportFilename,  twittererList,  exportFormat)  }  else  {  fmt.Println("Export type unsupported.")  }  }  func  exportXml(exportFilename  string,  twittererList  []string,  exportFormat  string)  {  fmt.Printf("Starting xml export to %s.",  exportFilename)  xmlContent,  err  :=  xml.Marshal(twittererList)  check(err)  header  :=  xml.Header  collectedContent  :=  header  +  string(xmlContent)  exportFile(collectedContent,  exportFilename+"."+exportFormat)  }  func  exportCsv(exportFilename  string,  twittererList  []string,  exportFormat  string)  {  fmt.Printf("Starting txt export to %s.",  exportFilename)  collectedContent  :=  rebuildForExport(twittererList,  ",")  exportFile(collectedContent,  exportFilename+"."+exportFormat)  }  func  exportTxt(exportFilename  string,  twittererList  []string,  exportFormat  string)  {  fmt.Printf("Starting %s export to %s.",  exportFormat,  exportFilename)  collectedContent  :=  rebuildForExport(twittererList,  "\n")  exportFile(collectedContent,  exportFilename+"."+exportFormat)  }  func  exportJson(exportFilename  string,  twittererList  []string,  exportFormat  string)  {  fmt.Printf("Starting %s export to %s.",  exportFormat,  exportFilename)  collectedContent  :=  collectContent(twittererList)  exportFile(string(collectedContent),  exportFilename+"."+exportFormat)  }  func  collectContent(twittererList  []string)  []byte  {  collectedContent,  err  :=  json.Marshal(twittererList)  check(err)  return  collectedContent  }  func  rebuildForExport(twittererList  []string,  concat  string)  string  {  var  collectedContent  string  for  _,  twitterAccount  :=  range  twittererList  {  collectedContent  =  collectedContent  +  concat  +  twitterAccount  }  if  concat  ==  ","  {  collectedContent  =  strings.TrimLeft(collectedContent,  concat)  }  return  collectedContent  }  func  exportFile(collectedContent  string,  exportFile  string)  {  contentBytes  :=  []byte(collectedContent)  err  :=  ioutil.WriteFile(exportFile,  contentBytes,  0644)  check(err)  } 
```

[50:00](https://www.youtube.com//watch?v=YQ7eeErPNY4&t=3000s) 我快速安装了一个 Apache Cassandra 单节点，稍后我将用于开发。我还快速展示了如何启动和停止后安装。

**参考** : [Apache Cassandra](http://cassandra.apache.org/) ，[下载页面，安装说明](http://cassandra.apache.org/download/)。

[53:50](https://www.youtube.com//watch?v=YQ7eeErPNY4&t=3230s) 为 go 选择 go-twitter API 库。我很快地看了几个，以确保那是我想要使用的库。

**参考** : [go-twitter 图书馆](https://github.com/dghubble/go-twitter)

[56:35](https://www.youtube.com//watch?v=YQ7eeErPNY4&t=3395s) 现在，我将介绍如何在 API 接口中设置 Twitter 应用程序。这是本系列的一个关键部分，我将介绍消费者密钥、访问令牌和访问令牌机密，以及它们在 Twitter 界面中的位置，以及如果只是在流中显示密钥，需要如何重置它们(就像我刚刚做的那样，令人震惊！)

在这里，我将讨论并展示如何在 [Goland IDE](https://www.jetbrains.com/go/) 中设置环境变量来构建和执行 CLI。一旦这些都设置好了，它们将成为我在 IDE 中测试 CLI 的主要机制，我将逐步构建更多的功能。

[1:00:18](https://www.youtube.com//watch?v=YQ7eeErPNY4&t=3618s) 更新`twitz config`命令以显示我们刚刚作为环境变量添加的键。我还设置了一些字符串解析，并删除了秘密的结尾，这样整个变量值就不会显示出来，但足以确认它确实是一个设置的配置或环境变量。

在这一点上，我通过一些额外的函数重构来清理一些存在的代码混乱。使用 Goland 的 extract method 特性和其他工具，我完成了几次清理代码的重构工作。

[1:23:17](https://www.youtube.com//watch?v=YQ7eeErPNY4&t=4997s) 在 Goland 中复制构建配置。当您有许多构建配置选项时，您可以做一件方便的小事。

[1:37:32](https://www.youtube.com//watch?v=YQ7eeErPNY4&t=5852s) 在视频的这一部分，我查看了代码库中的 app-auth 示例，但我必须补充说明，我在使用确切的示例时遇到了问题。但是我处理了它，得到了第一个错误消息，任何人都会得到，他们使用了相同的例子。不过，我在下一次会议中修复了它们，不过，这段视频为我将提交给回购的未决公关和相关工作提供了基础。

视频的其余部分试图找出到底发生了什么或没有发生什么错误。

我将在本系列的下一篇文章中介绍工作代码。在此之前，请观看总结并享受！

[1:59:20](https://www.youtube.com//watch?v=YQ7eeErPNY4&t=7160s)Twitch 上的视频摘要和即将发布的流媒体时间表。

**更新系列零件**

我在 Twitter [@Adron](https://twitter.com/Adron) 和 Twitch [@adronhall](https://twitch.tv/adronhall/) 上一直听金属和编码，写关于编码、学习和教授各种主题，从数据库技术到编码印章。感谢阅读！