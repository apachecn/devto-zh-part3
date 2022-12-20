# 如何使用 REST 客户端/原生应用程序让 Shrine 客户端上传和 S3 为您工作(没有 Javascript，只有后端)

> 原文：<https://dev.to/rob117/how-to-use-rest-clients--native-apps-to-make-shrine-client-uploads-and-s3-work-for-you-no-javascript-just-backend-322h>

# 神社和 AWS

我想为我正在编写的新应用程序提供直接上传(客户端上传文件，然后将链接传递给服务器)解决方案。后端将被许多不同的服务共享。我环顾四周--神社似乎是最有希望的，所以我去了。

这些信息大多只适用于 S3 直接上传和 API 签名，所以如果你对此感到困惑，请阅读。

我不想使用带有预制库的前端解决方案，因为我试图为移动应用程序解决这个问题。我想我做到了——如果你能在休息时做到，你就能在任何地方做到。

我写这篇文章是因为我已经*刚刚*发现如何让邮递员/失眠起作用，我不想撒谎——我很恼火。不订阅 GoRails(我订阅了)或不使用 Uppy 库(很多时候不是一个选项)就能获得如何使用 Shrine 的简单示例，这真的不需要花很多时间。

先不说这个——虽然 GoRails 对我来说真的不值得，当他们也使用 JS 时，我几乎立刻就取消了，但 Chris(一个指导者)在他的视频中非常酷，很容易理解。我并没有批评整个服务，他们只是没有我需要的东西。嗯，除了我用的那个甜甜甜甜的测试图(谷歌“金钱树懒”)。

好吧。进入主要部分。

## 设定-分解

对于服务器端来说，设置 Shrine 实际上非常简单。我将跳过整个事情，因为教程真的很直截了当，我只想解释 AWS 部分，并给你一个快速提示。确保像 shrine 告诉你的那样用 CORS 设置你的桶，除了我们所关心的，把接受域留给`*`，因为我们是 rest-client 测试。

快速提示——如果您对使用 shrine 犹豫不决，因为您想使用 devise 或某种认证解决方案来保护您的预设计端点(您应该这样做！)，高枕无忧。这些文件很简单。

我们之所以使用 POST over PUT，是因为一系列原因，这些原因在这里得到了最好的阐述(阅读链接，阅读答案)。

### 快速概述其工作原理

您希望客户端将图像上传到 S3，而无需您的服务器处理它们。然而，如果你只是让任何人上传任何东西，有一天你会有一个可怕的一天，当你发现有人一直在使用你的 AWS 帐户存储他们的异国情调的长颈鹿跳舞视频集合。全部 20tb 的数据。

署名文章可以让你通过按顺序做以下事情来避免这个问题-

1)进行身份验证，并向您的服务器请求上传文件的权限。你告诉他们什么类型的文件(你可以告诉他们文件名，但不，不需要)。比如“我会上传一张 jpeg。”

2) Shrine 运行它的魔法，生成一堆特定的东西让你这么做。我是认真的，除了文件本身，你还需要上传文件的 8 件事。

3)你收集大量信息并上传。

### 错误列表

如果您看到其中一个错误，以下是修复它的方法:

与过期请求相关的一些错误-过期时间已过，请重试。一些与 403 过期请求策略相关的错误——你的服务器时间被打乱了。在我这个荒谬的例子中，我通过自制程序更新了 Postgresql，这打乱了数据库时钟。我重新开始。解决了。

一些与签名相关的错误-你的一个标题是混乱的。值得注意的是，一些策略在生成时，在它们的末尾有一个`=`符号。复制粘贴时，确保正确写入/删除。

某个错误说“Policy something something`"eq", "Some header", "some value"`”——你遗漏了那个值。将该值与错误中指出的键/值一起放入表单数据中。即“您缺少`eq, content-type, image/jpeg`进入您的表单数据并添加关键字:内容类型，值:图像/jpeg

### 甚至在我们开始之前

您发送到 S3 的 post 请求必须是表单数据。您根本不需要任何标题，因为表单数据将包含您需要的所有内容。显然，这必须是一个 post 请求。下一部分非常非常非常重要:**存储在桶中的对象的键必须放在最前面，附加文件必须放在最后**。如果不这样做，你的帖子就不会起作用。此外，一旦你写了整个请求，如果你没有得到要发布的文件，继续检查请求后面的实际 curl 代码。查找标有`"filename="`的部分，并确保文件名确实已附上。我的直到升级到最新版的邮差才知道。

### 现在我们可以开始了

好吧。哇哦。让我们开始吧。

首先向服务器上的预签名端点发送一个简单的 get 请求。我把我的设置为`http://localhost:3000/api/s3/params`。

get 请求应该有一个查询参数-“类型:”

例如，如果您的 rails 代码在您的预设计端点中看起来是这样的(或多或少应该是这样的):

```
Shrine.storages = {
  cache: Shrine::Storage::S3.new(prefix: 'cache', **s3_options),
  store: Shrine::Storage::S3.new(**s3_options)
}

Shrine.plugin :activerecord
Shrine.plugin :restore_cached_data # refresh metadata when attaching the cached file
Shrine.plugin :determine_mime_type
Shrine.plugin :presign_endpoint, presign_options: lambda { |request|
  filename = request.params['filename']
  type     = request.params['type']
  {
    content_disposition: ContentDisposition.inline(filename), # set download filename
    content_type: type, # set content type (required if using DigitalOcean Spaces)
    content_length_range: 0..(1 * 1024 * 1024) # limit upload size to 1 MB
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在第
行

```
filename = request.params['filename']
type     = request.params['type'] 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们的 GET 请求应该使用 type 作为键。如果您将其设置为类似于`type = request.params['content']`的值，那么键名应该是`content`。不管怎样，一个有效的参数可能看起来像`type=image/jpeg`(当然，url 被 REST 客户端转义)

我们实际上不需要文件名参数。您可以包含它，但如果文件名参数不完全匹配，它不会发送。因此，如果你有一张图片，比如说，*一只华丽的金钱树懒*，你想把它命名为 MONAY.jpg，如果你把文件名命名为 MONAY.jpg，然后从你的 rest 客户端附上它，你不会让它工作。这是因为你的 rest 客户端会将其附加为`/User/me/Documents/MONAY.jpg`，上传时那些名称并不匹配。

总之。

好的，所以在您发送了简单的 get 请求(如果您使用的是 auth 解决方案，请确保包含 auth 头)之后，您将得到类似如下的结果:

```
{
  "fields": {
    "key": "cache/ac48d4a7764610579df3f26c316f3947",
    "Content-Disposition": "inline",
    "Content-Type": "image/jpeg",
    "policy": "eyJleHBpcmF0aW9uIjoiMjAxOS0wMi0yMlQwOTo1MjozMloiLCJjb25kaXRpb25zIjpbeyJidWNrZXQiOiJ2aXNpb24td2ViLWltYWdlLWRldiJ9LHsia2V5IjoiY2FjaGUvYWM0OGQ0YTc3NjQ2MTA1NzlkZjNmMjZjMzE2ZjM5NDcifSx7IkNvbnRlbnQtRGlzcG9zaXRpb24iOiJpbmxpbmUifSx7IkNvbnRlbnQtVHlwZSI6ImltYWdlL2pwZWcifSxbImNvbnRlbnQtbGVuZ3RoLXJhbmdlIiwwLDEwNDg1NzZdLHsieC1hbXotY3JlZGVudGlhbCI6IkFLSUFKT0VSVTNMTkFaSExOUFRBLzIwMTkwMjIyL2FwLW5vcnRoZWFzdC0xL3MzL2F3czRfcmVxdWVzdCJ9LHsieC1hbXotYWxnb3JpdGhtIjoiQVdTNC1ITUFDLVNIQTI1NiJ9LHsieC1hbXotZGF0ZSI6IjIwMTkwMjIyVDA4NTIzMloifV19",
    "x-amz-credential": "********/20190222/ap-northeast-1/s3/aws4_request",
    "x-amz-algorithm": "AWS4-HMAC-SHA256",
    "x-amz-date": "20190222T085232Z",
    "x-amz-signature": "769489a916a228d142e9bff18688bf45f44c7e77eac81f4797b8c48e3b8ab756"
  },
  "headers": {},
  "method": "post",
  "url": "https://BUCKETNAME.s3.ap-northeast-1.amazonaws.com"
} 
```

Enter fullscreen mode Exit fullscreen mode

不要惊慌。一个接一个-

关键-S3 中哪个神龛为你命名了这个物体？
内容-处置- [与我们无关，但无论如何都是文档](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition)。只是复制它。

内容-类型-是的，这就是我们所说的类型。政策-只是复制它。这告诉亚马逊什么被允许上传，以及如何上传。

接下来的四个以 x-amx-* -这四个一起，是 Amazon V4 签名协议的一部分。他们告诉亚马逊这个请求是合法的。就是这样，只要确保每次都正确地复制它们，就不会有问题了。

标题-这是一团糟。完全忽略这些。我花了太多太多的时间在标题上，直到我意识到它们是转移注意力的东西。

方法——贴吧。Url -发布到这个 Url。

好的，现在我将继续向您展示您的客户需要的样子。

如果你用的是 postman，把你的网址复制到地址栏，设置为 post。单击“正文”选项卡，并将其设置为“表单数据”。现在点击右上角的批量编辑橙色文本，并通过这个在-

```
key:YourKeyHere
x-amz-credential:YourCredentialsHere
policy:YourPolicyHere
x-amz-algorithm:AWS4-HMAC-SHA256
x-amz-date:YourDateHere
x-amz-signature:YourSigHere
Content-Disposition:inline
Content-Type:YourContentTypeHere 
```

Enter fullscreen mode Exit fullscreen mode

只需用您的值替换这些值，**在底部添加文件**，您就成功了。或者，如果您使用的是另一个 rest 客户端，请使用上面的键，慢慢地填写您自己的值，因为您不会进行批量复制粘贴，所以您需要逐行进行。然后**在底部添加文件**

好吧。现在，点击发送。如果一切正常，您应该得到一个 204，并且图像在您的桶中。如果一切都不工作，哭泣，看看上面的错误列表，或者发表评论，我会尽最大努力在大约一天/一周的时间里解决问题。

### 使用该响应保存文件

这个其实很简单。确保你有可用的`:restore_cached_data`插件(见上面我的神龛代码)。在您的 Ruby 代码中，一旦您进行了身份验证，就调用以下代码:

`ModelInQuestion.update_attributes(attachment_name: {id: "ID OF S3 OBJECT", storage: "cache"}.to_json)`

例如，如果您的对象类似于`books`，并且您在文件夹中设置的图像上传程序设置为`image:`，假设您的 AWS 响应如下:

```
x-amz-id-2 → stuff
x-amz-request-id → stuff
Date →Sat, 23 Feb 2019 10:04:20 GMT
ETag →"daa14e86f803fca11a1baff256fa259b"
Location →https://bucket.s3.ap-northeast-1.amazonaws.com/cache%2Fdabe031bee03ae8365e2ec48b523bb33
Content-Length →0
Server →AmazonS3 
```

Enter fullscreen mode Exit fullscreen mode

你想叫这个代码:

`YourBookModelHere.update_attributes(image: {id: "dabe031bee03ae8365e2ec48b523bb33", storage: "cache"}.to_json)`

我们从位置标签末尾获取图像 ID。文档中实际上有一个正则表达式可以为你解析这个- [最后一段](//image:%20%7Bid:%20)

快速注意——我发现，如果您想传递字符串并使用正则表达式，您必须接受 url 字符串，恢复它，然后从正则表达式中删除行字符的开头。

或者，在代码中:

`URI.decode(params[:link])[/cache\/(.+)/, 1]`

现在，当您运行更新属性时，您应该会得到这个很酷的东西:

```
<ImageUploader::UploadedFile:0x00007fbb995c8eb8
@data={
"id"=>"e68f1910d4fb723d6538383dbbac5f78",
"storage"=>"store",
"metadata"=>{"filename"=>nil, "size"=>92931, "mime_type"=>"image/jpeg"}}> 
```

Enter fullscreen mode Exit fullscreen mode

注意，如果你的 mime_type 是 nil，你需要将`Shrine.plugin :determine_mime_type`添加到你的插件列表中。

现在，如果你调用`yourbook.image_url`，你会得到一个持续 15 分钟的超级酷的签名网址！

最后，如果你想设置一个比 15 分钟更短的到期时间(比如 5 分钟)，使用 default_url_options 插件-

```
 # 90 seconds to use the upload URL, 5 minutes to use the GET url
  Shrine.plugin :default_url_options,
    cache: { expires_in: 90 },
    store: { expires_in: 300 } 
```

Enter fullscreen mode Exit fullscreen mode

向宝石维护者 Janko 大声欢呼，感谢他在圣地谷歌小组的所有帮助。如果你有问题，我建议你去那里——他非常友好！