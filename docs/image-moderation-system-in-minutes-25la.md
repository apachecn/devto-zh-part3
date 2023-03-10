# 几分钟内图像审核系统

> 原文：<https://dev.to/idoshamun/image-moderation-system-in-minutes-25la>

每个用户生成的内容平台都需要某种内容审核系统，以确保内容是适当的和受尊重的，否则你可能会从用户那里得到一些严重的负面反馈(根据经验来说😵).在这篇文章中，我想具体谈谈图像审核，以及如何轻松地建立一个系统，从你的应用程序中拒绝 NSFW 图像。🙈

## 谷歌云视觉

> 通过将强大的机器学习模型封装在易于使用的 REST API 中，使开发人员能够理解图像的内容。

我将使用 Cloud Vision API 来自动检测由 [SafeSearch](https://support.google.com/websearch/answer/510) 支持的不合适的图像。SafeSearch 根据以下可能性对您的图片进行评级:成人、恶搞、医学、暴力和色情。在我们(NSFW)成年人的情况下，暴力和下流可能是我们正在寻找的指标。你可以免费试用 API，看看这里的[怎么样](https://cloud.google.com/vision/)。
当然，云视觉有很多替代方案，但这是我最喜欢的。

## 服务器端

我们将使用 Node 来编写我们的审核代码和 [@google-cloud/vision](https://github.com/googleapis/nodejs-vision#readme) 包。

首先，我们必须初始化我们的注释器，以便稍后能够使用它

```
const vision = require(`@google-cloud/vision`);
const client = new vision.ImageAnnotatorClient(); 
```

接下来，让我们说一个用户想上传一个图像到我们的服务器，我们想拒绝图像，如果它是 NSFW。

```
const veryLikely = detection => detection === 'VERY_LIKELY';

const likelyOrGreater = detection =>
  detection === 'LIKELY' || veryLikely(detection);

const moderateContent = url =>
  client.safeSearchDetection(url)
    .then(([result]) => {
      const detections = result.safeSearchAnnotation;
      return likelyOrGreater(detections.adult) || likelyOrGreater(detections.violence) || veryLikely(detections.racy);
    }); 
```

我们的函数`moderateContent`得到一个`url`作为参数(它实际上也可以接收`buffer`)，这个`url`指向一个本地或者远程的图像文件。该函数返回一个承诺，如果内容必须被拒绝，则该承诺解析为 true，否则解析为 false。它实际上只包含一个对 Cloud Vision API 的第三方调用，以对提供的图像运行安全搜索检测。SafeSearch 用以下排名对图片进行排名:
`UNKNOWN, VERY_UNLIKELY, UNLIKELY, POSSIBLE, LIKELY, and VERY_LIKELY`。我将门槛设置为“可能”或更好的成人和暴力等级，将“非常可能”设置为“非常可能”，显然你可以将你的门槛设置为任何你想要的。

使用`moderateContent`函数，我们的服务器可以决定是继续处理提供的图像，还是用错误代码 400 拒绝它。

我希望现在你明白实现一个内容审核系统是多么容易，你所需要的只是几行代码和一个谷歌云账户。

祝你好运，请在下面的评论中告诉我进展如何:)