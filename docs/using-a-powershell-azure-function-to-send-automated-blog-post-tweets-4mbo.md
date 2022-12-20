# 使用 Powershell Azure 函数发送自动化的博客帖子

> 原文：<https://dev.to/devblackops/using-a-powershell-azure-function-to-send-automated-blog-post-tweets-4mbo>

如果你在 [Twitter](https://twitter.com/devblackops) 上关注我，你可能会注意到我偶尔会从随机的以前的博客帖子中发出推文。我不想记住手动发送这些，在阅读了[乔希·金](https://twitter.com/WindosNZ)在他的[自动化博客存档推文](https://king.geek.nz/2018/05/30/automatic-blog-archive-tweets/)文章中是如何做的之后，我想我应该加入我的观点。对于我的实现，我将使用一个 [Azure 函数](https://azure.microsoft.com/en-us/services/functions/)以及一点 blob 存储来跟踪以前的 tweets。这样，我就不需要依赖我的本地电脑了，而且我可以跟踪我已经发了哪些帖子，所以我不会重复它们。下面是一个自动推文的例子:

> 这个过程的所有代码都可以在 [GitHub repo](https://github.com/devblackops/blog-archive-tweeter-example) 中找到。

> ![Brandon Olin [1x Engineer] profile image](img/61f6fedc5fe60420728cdf73383779b0.png)布兰登奥林【1x 工程师】[@ devblackops](https://dev.to/devblackops)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)来自博客存档:基础设施测试与纠缠和运营验证框架
> 
> [bit.ly/2MQZPZf](https://t.co/1JGELPKtko)
> 
> [# PowerShell](https://twitter.com/hashtag/PowerShell)[#基础设施](https://twitter.com/hashtag/Infrastructure)[#测试](https://twitter.com/hashtag/Testing)[#纠缠](https://twitter.com/hashtag/Pester)[【OVF】](https://twitter.com/hashtag/OVF)

## TL；速度三角形定位法(dead reckoning)

这个博客帖子 tweeter 的工作原理是使用以前博客帖子的 JSON feed，随机选择一个，生成一个指向该帖子的 [Bitly](https://bitly.com/) 链接，然后发送该帖子。这条推文的记录随后被存储在 Azure 存储中，因此在所有可用的推文都被发出之前，该函数的后续调用不会重新发送相同的推文。一旦所有可用的帖子都被发布，跟踪器就会被重置。关于这个过程如何运作的所有细节，请继续阅读。

## 博文 JSON 提要

我为我的博客使用静态站点生成器 [Jekyll](https://jekyllrb.com/) ,并且每当我用新内容更新博客时，创建一个包含所有以前博客文章的 JSON 文件。这个 JSON 文件可以在 https://devblackops.io/feed.json 的[找到，你可以在这里](https://devblackops.io/feed.json)看到我是如何生成它的[。](https://github.com/devblackops/devblackops.github.io/blob/master/jsonfeed.html)

下面是这个 JSON 的一个片段:

```
{  "title":  "DevBlackOps",  "description":  "Thoughts about DevOps and automation from a Windows guy",  "url":  "https://devblackops.io/",  "date":  "Fri, 07 Sep 2018 04:02:33 +0000",  "posts":  [  {  "title":  "The PowerShell Conference Book",  "url":  "https://devblackops.io/the-powershell-conference-book/",  "date":  "Mon, 09 Jul 2018 00:00:00 +0000",  "tags":  [  "PowerShell"  ],  "categories":  [  "PowerShell"  ]  },  {  "title":  "The Operation Validation Framework: Test your infrastructure using Pester",  "url":  "https://devblackops.io/the-operation-validation-framework-test-your-infrastructure-using-pester/",  "date":  "Mon, 25 Jun 2018 00:00:00 +0000",  "tags":  [  "PowerShell",  "Infrastructure",  "Testing",  "Pester",  "OVF",  "Operation Validation"  ],  "categories":  [  "PowerShell"  ]  }  ]  } 
```

## 天蓝色功能

这个基于 PowerShell 的 Azure 函数使用来自我博客的 JSON 提要，选择一个随机的帖子，然后发布出去。标签也是基于博客文章中定义的任何标签创建的。我还从 [InvokeTwitterAPIs](https://github.com/MeshkDevs/InvokeTwitterAPIs) 库中的 [MeshkDevs](https://github.com/MeshkDevs) 借用了一些代码来使用 PowerShell 发送推文。

Azure 函数按计划运行，并使用来自我博客的 JSON 提要。当基于时间表触发函数时，Azure 存储中托管的基于 JSON 的跟踪器文件作为输入被传递。通过从 JSON 提要中获取帖子并删除 tracker 文件中包含的任何以前发布的帖子，可以创建一个可发布的帖子数组。从剩下的帖子中随机选择一个帖子。这篇文章随后被发布出去，并被添加到跟踪文件中，因此在所有可用的文章都被发布出去之前，它不会被再次发送。

该函数的相关位如下。要查看完整的功能，包括如何使用 Bitly 创建短链接并将推文发送到 Twitter，请查看 GitHub [repo](https://github.com/devblackops/blog-archive-tweeter-example) 中的[完整文件](https://github.com/devblackops/blog-archive-tweeter-example/blob/master/sendblogtweet/run.ps1)。

#### run.ps1

```
# I don't want these URLs tweeted out as they're not very relevant  $excludedPosts  =  @()  # Load tracker file  $tracker  =  Get-Content  $inBlob  |  ConvertFrom-Json  Write-Output  "Last tweeted: $($tracker.lastTweetedTime)"  # Get random blog post from feed  $blog  =  Invoke-RestMethod  -Uri  $env:BLOG_FEED_URL  $candidatePosts  =  $blog.posts.Where({$_.url  -notin  $excludedPosts})  # Get a post from the list of available posts that we haven't already tweeted  $tweetedUrls  =  $tracker.tweetedPosts  |  Select-Object  -ExpandProperty  url  $availablePosts  =  $candidatePosts.Where({$_.url  -notin  $tweetedUrls})  $post  =  $availablePosts  |  Get-Random  $availablePosts  =  $availablePosts.Where({$_.url  -ne  $post.Url})  if  (-not  $post)  {  # We've exhausted all available posts so reset  # the tracker and get a new post from the candidates  $post  =  $candidatePosts  |  Get-Random  $tracker.tweetedPosts  =  @()  $availablePosts  =  $candidatePosts.Where({$_.url  -ne  $post.Url})  }  $tracker.candidatePostsCount  =  $candidatePosts.Count  $tracker.availablePostsCount  =  $availablePosts.Count  if  ($post)  {  $postJson  =  $post  |  ConvertTo-Json  Write-Output  "Retrieved post:`n$postJson"  # Create hashtags  $hashtags  =  ''  $post.tags  |  Foreach-Object  {  $tag  =  $_  -replace  ' ',  ''  $hashtags  +=  (' #'  +  $tag)  }  $hashtags  =  $hashtags.Trim()  # Create tweet text  $title  =  $post.title  $link  =  Get-ShortUrl  -Url  $post.url  -OAuthToken  $env:BITLY_OAUTH_TOKEN  $tweetText  =  "From the blog archive: $Title`n`n$link`n`n$hashtags"  Write-Output  "Sending tweet:`n$tweetText"  $oAuth  =  @{  ApiKey  =  $env:TWITTER_CONSUMER_KEY  ApiSecret  =  $env:TWITTER_CONSUMER_SECRET  AccessToken  =  $env:TWITTER_ACCESS_TOKEN  AccessTokenSecret  =  $env:TWITTER_ACCESS_SECRET  }  $tweetParams  =  @{  ResourceURL  =  'statuses/update.json'  RestVerb  =  'POST'  Parameters  =  @{  status  =  $tweetText  }  OAuthSettings  =  $oAuth  }  $tweet  =  Invoke-TwitterRestMethod  @tweetParams  $tweetJson  =  $tweet  |  ConvertTo-Json  Write-Output  "Tweet sent:`n$tweetJson"  # Add tweeted post to tracker  $now  =  (Get-Date).ToUniversalTime().ToString('u')  $tracker.lastTweetedTime  =  $now  $tweetedPost  =  @{  url  =  $post.Url  lastTweeted  =  $now  }  $tracker.lastTweetedPost  =  $tweetedPost  $tracker.tweetedPosts  +=  $tweetedPost  $tracker.tweetedPostCount  =  $tracker.tweetedPosts.Count  $tracker.candidatePostsCount  =  $candidatePosts.Count  $tracker.availablePostsCount  =  $availablePosts.Count  $trackerJson  =  $tracker  |  ConvertTo-Json  Write-Output  "Saving tracker to blob:`n$trackerJson"  $trackerJson  |  Out-File  -Encoding  UTF8  -FilePath  $outBlob  } 
```

函数绑定在 [function.json](https://github.com/devblackops/blog-archive-tweeter-example/blob/master/sendblogtweet/function.json) 中定义。您可以看到，我已经设置了一个基于计时器的触发器来触发这个函数。你可以在微软文档中查看一下 [cron 表达式](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-timer#cron-expressions)是如何工作的。在本例中，该功能每隔`Monday, Wednesday, and Friday at 6:24am UTC`触发一次。

我还定义了一个包含在 Azure 存储中的 tracker 文件的输入绑定。这个相同的文件也被定义为一个输出绑定，因为函数既读取也写入它。

#### function.json

```
{  "bindings":  [  {  "type":  "timerTrigger",  "name":  "myTimer",  "schedule":  "0 24 6 * * 1,3,5",  "direction":  "in"  },  {  "type":  "blob",  "name":  "inBlob",  "path":  "sendblogtwitter/posts.json",  "connection":  "blogarchivetweeter_STORAGE",  "direction":  "in"  },  {  "type":  "blob",  "name":  "outBlob",  "path":  "sendblogtwitter/posts.json",  "connection":  "blogarchivetweeter_STORAGE",  "direction":  "out"  }  ],  "disabled":  false  } 
```

## 部署功能

如果您想在家里跟进，最好将 [GitHub repo](https://github.com/devblackops/blog-archive-tweeter-example) 和`cd`克隆到其中，以运行部署命令

```
git clone https://github.com/devblackops/blog-archive-tweeter-example
cd ./blog-archive-tweeter-example 
```

首先，我们要预先定义所有的变量。在这些表格中填入你的相关信息。为了生成这个进程使用的 Twitter 令牌，您可以在这里启动。要生成一个 Bitly OAuth 令牌，请遵循这里的指示。

```
# Settings  $subscription  =  '<YOUR-AZURE-SUBSCRIPTION>'  $resourceGroup  =  '<RESOURCE-GROUP-NAME>'  $region  =  '<AZURE-REGION>'  $storageAcct  =  '<STORAGE-ACCOUNT-NAME>'  $storageContainerName  =  '<STORAGE-CONTAINER-NAME>'  $functionApp  =  '<FUNCTION-APP-NAME>'  $blogFeedUrl  =  '<YOUR-FEED-URL>'  $twitterAccessSecret  =  '<TWITTER-ACCESS-SECRET>'  $twitterAccessToken  =  '<TWITTER-ACCESS-TOKEN>'  $twitterConsumerKey  =  '<TWITTER-CONSUMER-KEY>'  $twitterConsumeSecret  =  '<TWITTER-CONSUMER-SECRET>'  $bitlyOauthToken  =  '<BITLY-OAUTH-TOKEN>' 
```

现在，我们可以使用 [AZ CLI](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) 登录 Azure，并创建一个资源组来保存我们的功能和存储帐户。

```
az  login  az  account  set  --subscription  $subscription  az  group  create  --name  $resourceGroup  --location  $region 
```

创建一个新的存储帐户，并检索到它的连接字符串。

```
az  storage  account  create  --resource-group  $resourceGroup  --name  $storageAcct  --location  $region  --sku  Standard_LRS  $storageConnStr  =  az  storage  account  show-connection-string  --resource-group  $resourceGroup  --name  $storageAcct  --output  tsv 
```

创建一个存储容器并上传空的跟踪文件。这个文件可以在 GitHub repo [这里](https://github.com/devblackops/blog-archive-tweeter-example/blob/master/posts.json)找到。

```
az  storage  container  create  --account-name  $storageAcct  --name  $storageContainerName  az  storage  blob  upload  --account-name  $storageAcct  --container-name  $storageContainerName  --name  posts.json  --file  ./posts.json 
```

现在创建功能应用程序并设置应用程序设置。

```
az  functionapp  create  --resource-group  $resourceGroup  --name  $functionApp  --storage-account  $storageAcct  --consumption-plan-location  $region  az  functionapp  config  appsettings  set  --resource-group  $resourceGroup  --name  $functionApp  --settings  "FUNCTIONS_EXTENSION_VERSION = ~1"  az  functionapp  config  appsettings  set  --resource-group  $resourceGroup  --name  $functionApp  --settings  "BLOG_FEED_URL = $blogFeedUrl"  az  functionapp  config  appsettings  set  --resource-group  $resourceGroup  --name  $functionApp  --settings  "TWITTER_ACCESS_SECRET = $twitterAccessSecret"  az  functionapp  config  appsettings  set  --resource-group  $resourceGroup  --name  $functionApp  --settings  "TWITTER_ACCESS_TOKEN = $twitterAccessToken"  az  functionapp  config  appsettings  set  --resource-group  $resourceGroup  --name  $functionApp  --settings  "TWITTER_CONSUMER_KEY = $twitterConsumerKey"  az  functionapp  config  appsettings  set  --resource-group  $resourceGroup  --name  $functionApp  --settings  "TWITTER_CONSUMER_SECRET = $twitterConsumeSecret"  az  functionapp  config  appsettings  set  --resource-group  $resourceGroup  --name  $functionApp  --settings  "BITLY_OAUTH_TOKEN = $bitlyOauthToken"  az  functionapp  config  appsettings  set  --resource-group  $resourceGroup  --name  $functionApp  --settings  "blogarchivetweeter_STORAGE = $storageConnStr" 
```

现在我们必须部署实际的功能。为此，我们将压缩整个 GitHub 存储库，并将其部署到 Function App 中。

```
Compress-Archive  -Path  *  -DestinationPath  function.zip  az  functionapp  deployment  source  config-zip  --resource-group  $resourceGroup  --name  $functionApp  --src  ./function.zip 
```

## 总结

就是这样。此时，您应该已经在资源组中部署了功能应用程序和存储帐户，并且功能是基于计时器触发的。确保替换你的 Azure 环境、Twitter/Bitly 凭据和博客订阅源 URL 的所有相关设置。

现在你有了一个无服务器的博客推客，可以愉快地向你的追随者发送你过去的博客文章。

微博快乐！

干杯。