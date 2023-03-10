# 如何在 AWS Lambda 中使用 Akamai API 库

> 原文：<https://dev.to/roymartinezblanco/how-to-use-akamai-api-libraries-in-aws-lambda-2fdo>

这个博客旨在帮助你在 AWS Lambda 中使用 Akamai 的 T2 API 和 T4 库。

我们有如此多的选择去创造/创新。我们已经从物理硬件转移到虚拟化、云、容器、无服务器等，但是无论随着受众的增长使用何种技术，扩展都会在资源/成本方面变得昂贵。

这就是为什么内容交付网络总是你的武器库中的一个很好的工具，你应该总是把它放在你的脑海中。基础设施中的弹性不会取代 CDN 的优势，因此，我们应该考虑如何将它们集成到我们的应用流程中。

在这篇博客中，我们将使用 Akamai 的 API 毫不费力地为 AWS s3 提供 100%的卸载。

在 Python 中使用以下库。

*   [NetstorageAPI](https://github.com/akamai/NetStorageKit-Python)

*   [Akamai Edgegrid](https://github.com/akamai/AkamaiOPEN-edgegrid-python)

*   [用于 Python 的 AWS SDK](https://github.com/boto/boto3)

## 让我们用这个场景:

> 我的公司正在为我们的应用程序使用 AWS 基础设施，我们的大部分静态文件都存储在 s3 上。我们有一个需求，需要尽可能多地卸载(原因并不重要)我们的存储桶。我们特别关注一个文件，该文件每天每小时读取一次，但每天更改一次。这家公司已经使用 Akamai 的分布式平台来处理大量逻辑并提供应用卸载。

## 选项 A:

首先想到的一件事就是将 Akamai 上的 TTL(生存时间)增加到 2 天。

[![Akamai Property Manager Cache Setting](img/b04f75d26c96e7ced2c906c867fe565a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J8ZupeJb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/njl8jg0m2qn4rp0n7kjn.png)

然后，在对象每天发生变化时安排重复的缓存失效。

[![Akamai Property Manager Cache Invalidation](img/aad3f1b5260e587f19d2ba284083f68b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oumnAVcx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jidnxo40b02p9i05jyy8.png)

除了我将要提到的一些情况之外，这在几乎所有情况下都是可行的。假设有一天我们没有在计划的时间更新文件，这意味着:

1.  一旦出现这种情况，我们需要手动清除缓存

2.  即使对象没有刷新，缓存也是无效的。使得这个解决方案对于今天的世界来说有点静态，并且请求到达我们的基础设施。

这种解决方案总会出现的一个问题是 TTL 总会在某个时候过期，这意味着请求总会到达 AWS。

## 选项 B:

这是一个稍微复杂一点的解决方案。Akamai 有一个[存储解决方案“net Storage”](https://www.akamai.com/us/en/products/media-delivery/netstorage.jsp)使用 Akamai 的 API，我们可以将其与 AWS Lambda 集成。

[![Lambda Function Flow](img/66cf07f3b96f92420efe91294e33c2bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K2nFDbOq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4ows34azd9nxhaekoud7.png)

使用 Lambda，我们可以在 s3 中刷新对象时触发一个函数，并执行以下操作:

*   通过 API 将对象上传到[网络存储](https://www.akamai.com/us/en/products/media-delivery/netstorage.jsp)

*   Akamai 缓存一旦上传就失效，通过 API 刷新内容。

这将使我们能够确保仅在刷新对象时清除缓存/使缓存失效，并且通过使用 [NetStorage](https://www.akamai.com/us/en/products/media-delivery/netstorage.jsp) ，我们将实现 100%卸载，满足我们示例的要求。

## 我们如何做到这一点？

如前所述，我们将使用一些可以在 Akamai 的 Github 页面找到的库。

总之，我们需要:

*   **触发函数**:这将使用事件**“放”**在 s3 的对象上。如果您在这里看一下 AWS [的文档，您可以看到一个示例事件，它提供了对其中可用数据的洞察。](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html)

[![s3 Events](img/508906b3604830297e246661983b1a46.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zb_1oI8d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zureut3fm02y03hnvr1z.png)

*   **从 s3** 下载对象:AWS 有 [Boto3](https://github.com/boto/boto3) 非常容易使用，它允许我们下载对象。

*   **将对象上传到网络存储器**:使用[网络存储器](https://github.com/akamai/NetStorageKit-Python)。

*   **使缓存失效**:使用 [Akamai 快速清除 API](https://developer.akamai.com/api/core_features/fast_purge/v3.html) ，我们将能够在几秒钟内使缓存失效。

我们将使用 boto3 从 s3 下载对象，然后使用 NetStorage API 上传它，并使用快速清除 API 使它无效

。…听起来很简单，对吗？你会惊讶地发现它确实如此。

[![Seriously?](img/214c2bfffd62cb1f1b86bb5c195e5827.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_GdNVE3M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oobicm0pg6rlcovaivug.jpeg)

这是我们将在 Lambda 中使用的最终代码，可以在我的 GitHub [这里](https://github.com/roymartinezblanco/Akamai-on-AWS-Lambda/blob/master/src/main.py)找到

让我们仔细看看。

啊！差点忘了！开始之前，请确保您具备以下条件！:

```
 # You need a role for lambda to access s3
    # You need to provision Akamai NetStorage API Key/Upload Account.
    # You need Akamai Open API User Account
    # You need to know the NetStorage HOSTNAME, KEYNAME, KEY AND CPCODE
    # You need to s3 Object Key (Path/File), optionally you can get this from the s3 event 
```

### 注意:我不打算介绍如何创建 lambda 函数和 Akamai 凭证。

看看这个 [Github 仓库](https://github.com/roymartinezblanco/Akamai-on-AWS-Lambda)，你首先会注意到的是 **"/src/"** 目录下的一堆文件夹和文件。

[![](img/8c465fabe9618104bcf778bb8f037bfc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GncRa_Xu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tsg1ikdql5j26qrza673.png)

这是因为在 Lambda 中你不能安装或引用库，这意味着你需要为它的工作提供一切。

您需要在您的终端中打开项目目录安装/下载软件包:

```
 pip install edgegrid-python -t .
    pip install netstorageapi -t .
    pip install urllib.parse -t . 
```

一旦完成，您的导入将按预期工作。

好，我们有三个函数:

## **getObjectFromS3():**

在这个函数中，我们将对象从 s3 下载到一个临时目录中(您可以[获取对象](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#id219)而不是下载它)。

```
 s3 = boto3.resource('s3')
    s3.meta.client.download_file(bucket, path+'/'+file, '/tmp/'+file) 
```

如果你注意到我们使用了变量“路径”和“文件”。所有变量都存储在环境变量中，以避免对它们进行硬编码，特别是因为我们需要存储 API 凭证。为了从 Env 变量中获取值，我们使用了 **"os.environ["KEYNAME"]"** 并将值赋给一个可以重用的变量。

```
 #Fetch Credentials from Env Variables from AWS
    AO_ACCESS_TOKEN = os.environ['AO_ACCESS_TOKEN']
    AO_CLIENT_SECRET = os.environ['AO_CLIENT_SECRET']
    AO_API_HOST = os.environ['AO_API_HOST']
    AO_CLIENT_TOKEN = os.environ['AO_CLIENT_TOKEN']

    # Fetch s3 Variables from Env from AWS
    S3_Bucket = os.environ['S3_Bucket']
    S3_Path = os.environ['S3_PATH']
    S3_File = os.environ['S3_File']

    #Fetch Akamai NS Variables from Env from AWS
    NS_HOSTNAME = os.environ['NS_HOSTNAME']
    NS_KEYNAME = os.environ['NS_KEYNAME']
    NS_KEY = os.environ['NS_KEY']
    NS_CP = os.environ['NS_CP'] 
```

## **【上传数】**:

这个函数使用了上面提到的两个变量(变量和从 s3 中获取的对象)。

在这里，我们获得了获取对象的变量和使用 NetStorage API 的凭据，如果仔细观察，只需 2 行代码即可上传，但在这里，我们验证了对象已下载并存在于 temp 目录中。

```
 ns = Netstorage(NS_HOSTNAME, NS_KEYNAME, NS_KEY, *ssl*=False)
    ns.upload('/tmp/'+S3_File,'/'+S3_Path+*str*(NS_CP)+'/')
    # Note: SSL can be enabled 
```

## **【purgecpcode()**:

就像前面的函数一样，我们获得所需的凭证，并开始使用 [EdgeGrid](https://github.com/akamai/AkamaiOPEN-edgegrid-python) 身份验证方法创建 HTTP 请求，以便能够与 Akamai 对话。我们还根据[端点规范](https://developer.akamai.com/api/core_features/fast_purge/v3.html#postinvalidatecpcode)设置请求体和内容类型头，一旦准备好，我们就发出请求。如果一切顺利，我们将从 Akamai 获得 201 响应。

至此，我们已经浏览了脚本的不同部分，看看它们做了什么以及如何做。一旦上传到 AWS，您应该会看到类似这样的内容。

[![](img/b8143702af6712123d1f92c8639530ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NGRKzsrI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tuloyymth7tn6dmvkx87.png)

## 结论

我知道你可以在 Lambda 中实现/使用 Akamai API，这开启了一个无限可能的世界，我想分享这篇博客来提供一个快速的例子，告诉你如何去做，希望你会觉得有用。

最后的免责声明:

> 这个脚本是一个例子，我强烈建议您在将它投入生产之前尽可能地改进它。

你可以在 medium 上找到这篇博客这里:
[https://medium . com/@ roymartinezblanco/how-to-use-akamai-API-libraries-in-AWS-lambda-5204 fedc 21 F3](https://medium.com/@roymartinezblanco/how-to-use-akamai-api-libraries-in-aws-lambda-5204fedc21f3)