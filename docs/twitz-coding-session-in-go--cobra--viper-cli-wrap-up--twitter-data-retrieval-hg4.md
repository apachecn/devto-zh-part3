# Go 中的 Twitz 编码会话–Cobra+Viper CLI 总结+ Twitter 数据检索

> 原文：<https://dev.to/adron/twitz-coding-session-in-go--cobra--viper-cli-wrap-up--twitter-data-retrieval-hg4>

第 3 部分 3-Go-Cobra+Viper CLI 中的编码会话，用于解析文本文件、检索 Twitter 数据以及导出到各种文件格式。

**更新部分:(导航到原始复合代码博客)**

1.  用于解析文本文件的 Go - Cobra + Viper CLI 中的 Twitz 编码会话
2.  [Go-Cobra+Viper CLI 中的 Twitz 编码会话，初始安装 Twitter+Cassandra](http://compositecode.blog/2018/12/07/twitz-coding-session-in-go-cobra-viper-cli-with-initial-twitter-cassandra-installation/)
3.  [Go 中的 Twitz 编码会话- Cobra + Viper CLI 总结+ Twitter 数据检索(本帖)](http://compositecode.blog/2018/12/27/twitz-coding-session-in-go-cobra-viper-cli-wrap-up-twitter-data-retrieval/)

当我发布的时候，每个部分的更新链接将会发布在这篇文章的底部。对于代码，可以在视频和时间戳下面向下滚动。

[https://www.youtube.com/embed/j-jEQaG6byA](https://www.youtube.com/embed/j-jEQaG6byA)

[0:54](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=54s) 打草惊蛇游戏攻略。
[3:40](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=220s) 开始，回顾之前的会议，但我还没有打开声音，所以忽略它，直到 [5:20](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=320s) 。
[5:20](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=320s) 我注意到了，并打开了音量。现在我试着总结一下，谈谈 Twitter API 的一些问题。我逐步完成应用程序的设置，并为 Twitter API 密钥和秘密获取适当的 ID。
[9:12](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=552s) 我打开代码库，回顾之前的会议让我们到了哪里。使用 Cobra w/ Go，解析和重构之前完成的工作。
[10:30](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=630s) 在这里，我将再次讨论配置以及为运行应用程序进行 it 设置的细节。
[12:50](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=770s) 说起围棋的致命恐慌我是越来越。应用程序对 Github 的依赖引用与应用程序中的不同，并且没有显示实际执行的代码。我给出了一个快速解决方案，然后继续前进。
[17:12](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=1032s) 通过使用 go-twitter 库回到 Twitter API 使用。在这里，我回顾了这个问题，以及我在之前的会话中遇到的获取活动令牌的另一个问题的解决方案。我以为是图书馆处理的，但事实并非如此！
[19:26](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=1166s) 现在，我将逐步创建一个函数来获取要使用的活动誓言持有者令牌。在删除了上一次会议的大部分无效代码后，我开始编写代码，处理各种 Twitter 账户的 Twitter 搜索结果。

下一部分的大部分是我完成一些功能，一点点重构，以及回答观众/Twitch 的一些问题(正在想办法把它放到视频里！)，与一些依赖树问题进行斗争，以及一大堆愚蠢的事情。完成后，我将一些东西提交到 Github repo 中，并完成 Twitz 应用程序的核心功能。

[58:00](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=3480s) 回顾 go-twitter library repo 中的其他一些例子。我还快速回顾了库的其他函数调用，这些调用对 Twitter API 采取了行动。
[59:40](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=3580s) 我提交给项目本身的 PR 之一，我审查并合并到 repo 中，该 repo 为 README.md 添加了文档和构建徽章。
[1:02:48](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=3768s) 在这里，我向 README.md 文件添加了一些关于配置设置的更多信息。

[1:05:48](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=3948s)Twitz 页面现已更新:T3】https://adron.github.io/twitz/
[1:06:48](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=4008s)Travis CI 项目本身持续集成的设置:[https://travis-ci.org/Adron/twitz](https://travis-ci.org/Adron/twitz)
[1:08:58](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=4138s)为 Go 设置实际的 travis.yml 文件。在这之后，我经历了几个故障排除阶段，让构建继续进行，在 ole' yaml 文件中留一些空白等等。也包括著名的肠衣问题！啊！
[1:26:20](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=5180s) 现在，我开始总结本次会议的成果。

**注:** *是的，我意识到我忘了把它导出到 Apache Cassandra 的功能。是的，我确实会有一个未来的流，在其中我构建出将响应导出到 Apache Cassandra 的部分！所以订阅吧，继续关注，我会尽快完成的！！！*

[1:31:10](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=5470s) 由于一个构件为绿色，另一个构件为黄色，进一步进行 CI 故障排除。更多 CI 故障排除！了解特拉维斯 yaml 在这里。
[曲云霞](https://youtu.be/j-jEQaG6byA/watch?v=j-jEQaG6byA&t=5672s)完成了，现在就差那个烂屁股了！

## 法典

在之前的帖子中，我概述了两个构建的特定功能:

*   [第 1 部分](http://compositecode.blog/2018/12/04/twitz-coding-session-in-go-cobra-viper-cli-for-parsing-text-files/)-`twitz config`命令的`config`功能。
*   [第 2 部分](http://compositecode.blog/2018/12/07/twitz-coding-session-in-go-cobra-viper-cli-with-initial-twitter-cassandra-installation/)-`twitz parse`命令的`parse`功能。

在这篇文章中，我重点更新了这两个功能，并针对 Twitter API 和其他功能为 auth 和 ident 的载体令牌检索添加了额外的功能。最后一节课结束后，让我们看看函数是什么样子的。

`config`命令基本上由 5 行`fmt.Printf`函数组成，用于打印出 CLI 所需的相关配置值和环境变量。

```
var  configCmd  =  &cobra.Command{  Use:  "config",  Short:  "A brief description of your command",  Long:  `A longer description that spans multiple lines and likely contains examples
and usage of using your command. For the custom example:
Cobra is a CLI library for Go that empowers applications.
This application is a tool to generate the needed files
to quickly create a Cobra application.`,  Run:  func(cmd  *cobra.Command,  args  []string)  {  fmt.Printf("Twitterers File: %s\n",  viper.GetString("file"))  fmt.Printf("Export File: %s\n",  viper.GetString("fileExport"))  fmt.Printf("Export Format: %s\n",  viper.GetString("fileFormat"))  fmt.Printf("Consumer API Key: %s\n",  viper.GetString("consumer_api_key")[0:6])  fmt.Printf("Consumer API Secret: %s\n",  viper.GetString("consumer_api_secret")[0:6])  },  } 
```

`parse`命令做了一点小改动。我将相当一部分功能重构到了`buildTwitterList()`和`exportFile`以及`rebuildForExport`功能中。我放在 helper.go 文件中的`buildTwitterList()`,稍后我会谈到它。但是在这个文件中，我会用到一些重构，我有几个功能；导出到格式功能，以及`exportParsedTwitterList`功能的逻辑。

```
var  parseCmd  =  &cobra.Command{  Use:  "parse",  Short:  "This command will extract the Twitter Accounts form a text file.",  Long:  `This command will extract the Twitter Accounts and clean up or disregard other characters 
or text around the twitter accounts to create a simple, clean, Twitter Accounts only list.`,  Run:  func(cmd  *cobra.Command,  args  []string)  {  completedTwittererList  :=  buildTwitterList()  fmt.Println(completedTwittererList)  if  viper.Get("fileExport")  !=  nil  {  exportParsedTwitterList(viper.GetString("fileExport"),  viper.GetString("fileFormat"),  completedTwittererList)  }  },  }  func  exportParsedTwitterList(exportFilename  string,  exportFormat  string,  twittererList  []string)  {  if  exportFormat  ==  "txt"  {  exportTxt(exportFilename,  twittererList,  exportFormat)  }  else  if  exportFormat  ==  "json"  {  exportJson(exportFilename,  twittererList,  exportFormat)  }  else  if  exportFormat  ==  "xml"  {  exportXml(exportFilename,  twittererList,  exportFormat)  }  else  if  exportFormat  ==  "csv"  {  exportCsv(exportFilename,  twittererList,  exportFormat)  }  else  {  fmt.Println("Export type unsupported.")  }  }  func  exportXml(exportFilename  string,  twittererList  []string,  exportFormat  string)  {  fmt.Printf("Starting xml export to %s.",  exportFilename)  xmlContent,  err  :=  xml.Marshal(twittererList)  check(err)  header  :=  xml.Header  collectedContent  :=  header  +  string(xmlContent)  exportFile(collectedContent,  exportFilename+"."+exportFormat)  }  func  exportCsv(exportFilename  string,  twittererList  []string,  exportFormat  string)  {  fmt.Printf("Starting txt export to %s.",  exportFilename)  collectedContent  :=  rebuildForExport(twittererList,  ",")  exportFile(collectedContent,  exportFilename+"."+exportFormat)  }  func  exportTxt(exportFilename  string,  twittererList  []string,  exportFormat  string)  {  fmt.Printf("Starting %s export to %s.",  exportFormat,  exportFilename)  collectedContent  :=  rebuildForExport(twittererList,  "\n")  exportFile(collectedContent,  exportFilename+"."+exportFormat)  }  func  exportJson(exportFilename  string,  twittererList  []string,  exportFormat  string)  {  fmt.Printf("Starting %s export to %s.",  exportFormat,  exportFilename)  collectedContent  :=  collectContent(twittererList)  exportFile(string(collectedContent),  exportFilename+"."+exportFormat)  }  func  collectContent(twittererList  []string)  []byte  {  collectedContent,  err  :=  json.Marshal(twittererList)  check(err)  return  collectedContent  }  func  rebuildForExport(twittererList  []string,  concat  string)  string  {  var  collectedContent  string  for  _,  twitterAccount  :=  range  twittererList  {  collectedContent  =  collectedContent  +  concat  +  twitterAccount  }  if  concat  ==  ","  {  collectedContent  =  strings.TrimLeft(collectedContent,  concat)  }  return  collectedContent  }  func  exportFile(collectedContent  string,  exportFile  string)  {  contentBytes  :=  []byte(collectedContent)  err  :=  ioutil.WriteFile(exportFile,  contentBytes,  0644)  check(err)  } 
```

解析之后，接下来似乎应该覆盖 *[helpers.go](https://github.com/Adron/twitz/blob/master/cmd/helpers.go)* 文件代码。首先我有`check`函数，它简单地包装了例行复制的错误处理代码片段。请直接查看该文件。然后在它下面我有一个`buildTwitterList()`函数，它获取文件名的配置设置以打开 Twitter 账户进行解析。然后，代码读取文件，将文本文件的结果拆分成字段，然后遍历并解析出 Twitter 帐户。这是用正则表达式(*完成的，我知道我现在有两个问题，但是，嘿，这超级简单！*)。它基本上是找到以@开头的字段，然后验证字母数字的性质，结合可能的下划线，然后删除这些字段中不必要的字符。通过将这些字段放入一个字符串/切片数组并将该字符串数组返回给调用代码来完成所有这些工作。

```
func  buildTwitterList()  []string  {  theFile  :=  viper.GetString("file")  theTwitterers,  err  :=  ioutil.ReadFile(theFile)  check(err)  stringTwitterers  :=  string(theTwitterers[:])  splitFields  :=  strings.Fields(stringTwitterers)  var  completedTwittererList  []string  for  _,  aField  :=  range  splitFields  {  if  strings.HasPrefix(aField,  "@")  &&  aField  !=  "@"  {  reg,  _  :=  regexp.Compile("[^a-zA-Z0-9_@]")  processedString  :=  reg.ReplaceAllString(aField,  "")  completedTwittererList  =  append(completedTwittererList,  processedString)  }  }  return  completedTwittererList  } 
```

*[Helpers.go](https://github.com/Adron/twitz/blob/master/cmd/helpers.go)* 文件中的下一个函数是`getBearerToken`函数。这是一段复杂的代码。这个函数从 Twitter 应用程序获取消费者密钥和秘密(查看在 [5:20](https://www.youtube.com/watch?v=j-jEQaG6byA&t=320s) 的视频，了解在哪里设置它)。它返回一个字符串和错误，如果有错误就返回空字符串，如下所示。

代码首先针对 Twitter API 建立一个 POST 请求，请求一个令牌并传递客户端凭证。如果不成功，则捕获一个错误，但如果可以，代码会在接收到令牌字符串字节数组时用标准编码功能设置 b64Token 变量(第 9 行和第 10 行)。之后，请求基于所需的 authoriztaion 和 content-type 属性( *properties，values？我不记得 spec 怎么称呼这些*，然后用`http.DefaultClient.Do(req)`发出请求。返回响应，或者错误和空响应(或者 nil？我没有检查确切的函数签名逻辑)。接下来是 defer，以确保当所有事情都完成时响应关闭。

接下来，JSON 结果被解析(解组)成`v struct`，我现在意识到，在我写这篇文章的时候，我可能应该把它重命名为不是单个字母的东西。但是现在它工作了，v 有一个相关的 AccessToken 变量，然后返回这个变量。

```
func  getBearerToken(consumerKey,  consumerSecret  string)  (string,  error)  {  req,  err  :=  http.NewRequest("POST",  "https://api.twitter.com/oauth2/token",  strings.NewReader("grant_type=client_credentials"))  if  err  !=  nil  {  return  "",  fmt.Errorf("cannot create /token request: %+v",  err)  }  b64Token  :=  base64.StdEncoding.EncodeToString(  []byte(fmt.Sprintf("%s:%s",  consumerKey,  consumerSecret)))  req.Header.Add("Authorization",  "Basic "+b64Token)  req.Header.Add("Content-Type",  "application/x-www-form-urlencoded;charset=UTF-8")  resp,  err  :=  http.DefaultClient.Do(req)  if  err  !=  nil  {  return  "",  fmt.Errorf("/token request failed: %+v",  err)  }  defer  resp.Body.Close()  var  v  struct  {  AccessToken  string  `json:"access_token"`  }  if  err  :=  json.NewDecoder(resp.Body).Decode(&v);  err  !=  nil  {  return  "",  fmt.Errorf("error parsing json in /token response: %+v",  err)  }  if  v.AccessToken  ==  ""  {  return  "",  fmt.Errorf("/token response does not have access_token")  }  return  v.AccessToken,  nil  } 
```

哇，好吧，这是一个公平的工作。接下来是 twitz 的`findem.go`文件和相关函数。在这里，我从控制台的几个信息输出开始，只是为了了解 CLI 在某些点上到达了哪里。twitter 列表放在一起，重用同一个功能——耶代码重用权利！则检索访问令牌。接下来构建 http 客户端，twitter 客户端被传递并初始化，用户查找请求被发送。最后，用户被打印出来，下面是一个计数和用户计数的打印结果。

```
var  findemCmd  =  &cobra.Command{  Use:  "findem",  Short:  "A brief description of your command",  Long:  `A longer description that spans multiple lines and likely contains examples
and usage of using your command. For example:
Cobra is a CLI library for Go that empowers applications.
This application is a tool to generate the needed files
to quickly create a Cobra application.`,  Run:  func(cmd  *cobra.Command,  args  []string)  {  fmt.Println("Starting Twitter Information Retrieval.")  completedTwitterList  :=  buildTwitterList()  fmt.Printf("Getting Twitter details for: \n%s",  completedTwitterList)  accessToken,  err  :=  getBearerToken(viper.GetString("consumer_api_key"),  viper.GetString("consumer_api_secret"))  check(err)  config  :=  &oauth2.Config{}  token  :=  &oauth2.Token{AccessToken:  accessToken}  // OAuth2 http.Client will automatically authorize Requests  httpClient  :=  config.Client(context.Background(),  token)  // Twitter client  client  :=  twitter.NewClient(httpClient)  // users lookup  userLookupParams  :=  &twitter.UserLookupParams{ScreenName:  completedTwitterList}  users,  _,  _  :=  client.Users.Lookup(userLookupParams)  fmt.Printf("\n\nUsers:\n%+v\n",  users)  howManyUsersFound  :=  len(users)  fmt.Println(howManyUsersFound)  },  } 
```

我意识到，就在我完成这个的时候，我完全忘记了 Apache Cassandra 的出口。我将很快发布这些帖子，并可能在我调用这个完成之前进行另一次重构，以使输出达到更可用的状态。但是核心功能、工具所需的系统环境的设置、相关的数据和 API 访问以及其他元素都已经完成。现在，这是一个总结，如果你对最终的重构和 Apache Cassandra 导出感到好奇，那么订阅我的 Twitch [@adronhall](https://www.twitch.tv/adronhall) 和/或我的 YouTube 频道 [ThrashingCode](https://www.youtube.com/channel/UCHSCXYCMOOS-VSUdRsn13GQ) 。

**更新系列零件**

1.  1.  用于解析文本文件的 Go - Cobra + Viper CLI 中的 Twitz 编码会话
    2.  [Go-Cobra+Viper CLI 中的 Twitz 编码会话，初始安装 Twitter+Cassandra](http://compositecode.blog/2018/12/07/twitz-coding-session-in-go-cobra-viper-cli-with-initial-twitter-cassandra-installation/)
    3.  [Go 中的 Twitz 编码会话- Cobra + Viper CLI 总结+ Twitter 数据检索(本帖)](http://compositecode.blog/2018/12/27/twitz-coding-session-in-go-cobra-viper-cli-wrap-up-twitter-data-retrieval/)

我在 Twitter [@Adron](https://twitter.com/Adron) 和 Twitch [@adronhall](https://twitch.tv/adronhall/) 上一直听金属和编码，写关于编码、学习和教授各种主题，从数据库技术到编码印章。感谢阅读！