# 无服务器化的一个教训

> 原文：<https://dev.to/mhausenblas/a-lesson-learned-in-going-serverless-1ko8>

当我在开发 imgn 的[无服务器版本时，一个简单的图片共享演示应用，我遇到了一个关于使用 API 网关和 AWS Lambda 处理多部分表单数据`POST`请求的问题。这篇文章展示了我是如何解决这个问题的。](https://github.com/mhausenblas/imgn/tree/master/functions)

这款应用非常简单:它允许你上传图像文件，然后你可以在一个公共图库中查看它们。在后台，它提取一些图像元数据(目前只是尺寸)并与图像一起显示:

[![imgn app](img/3300e70f7e85a61af5897c4446d5dd12.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HFU-jcfR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mhausenblas/imgn/master/img/monolith_gallery-with-images.png)

在开发`imgn`的无服务器版本时，我在通过 API 网关使用 Lambda 函数将图像上传到 S3 时遇到了一个问题。你可以在 [StackOverflow](https://stackoverflow.com/questions/53537939/posting-image-using-form-data-via-api-gateway-to-lambda-function-results-in-inva) 上阅读更详细的问题描述，但本质是:API 网关移交给 Lambda 函数的有效负载不知何故被破坏了，所以当它们到达各自的 S3 桶时，图像最终被破坏了。

为了缩小混乱发生的范围，我首先替换了我的初始代码，该代码将创建一个`http.request`并使用 [ParseForm](https://golang.org/pkg/net/http/#Request.ParseForm) 方法通过我自己的[解析多部分表单数据](https://github.com/mhausenblas/imgn/blob/42912b2334400f969d08481b3d5341042211c31a/functions/app/uploadimg/main.go#L31)的实现来获取图像数据。然而，腐败是一样的。

接下来，为了排除“上传到 S3 存储桶”部分是数据损坏的罪魁祸首，我返回了作为 Lambda 调用的结果从 API 网关获得的解析后的图像数据。这让我可以使用`hexdump`来比较结果文件和原始文件，但是仍然没有效果。问题依然存在。

[![CloudWatch logs of the UploadImageFunction](img/0a13bc377557cb5d642feeba62d62b80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YMmg4pND--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.stack.imgur.com/Pdfjl.png)

请求路径中唯一剩下的事情是将(二进制)图像数据从 API 网关移交给 Lambda 函数。

在搜罗了 StackOverflow 并阅读了 AWS 论坛上许多看似相关的帖子后，我决定是时候换一种方法了。我仔细阅读了[预签名网址](https://docs.aws.amazon.com/sdk-for-go/v1/developer-guide/s3-example-presigned-urls.html)，决定尝试一下。这些预签名的 URL 使您能够允许人们操作 S3 存储桶中的对象，并且您可以根据什么操作、在哪个对象上以及允许它发生多长时间来设置限制。

因此，我们的想法是，当用户通过 UI 发出一个多部分表单数据`POST`请求时，`UploadImageFunction`不会直接将图像上传到 S3，而是为进入所述 S3 桶的`PUT`请求创建一个[预先签名的 URL](https://github.com/mhausenblas/imgn/blob/master/functions/app/uploadimg/main.go#L118-L126) ，对之前表单数据`POST`请求中引用的文件有效，并且限制为 5 分钟:

```
req,  _  :=  s3c.PutObjectRequest(&s3.PutObjectInput{  Bucket:  aws.String(gallerybucket),  Key:  aws.String(imgname),  })  presurl,  err  :=  req.Presign(5  *  time.Minute) 
```

…然后 UI 中的 [JavaScript 代码](https://github.com/mhausenblas/imgn/blob/42912b2334400f969d08481b3d5341042211c31a/functions/ui/upload.js#L31)会发出第二个`PUT`请求，使用预先签名的 URL 将图像直接上传到 S3 桶中。这非常有效！

我仍然不确定我是否应该把这个策略定义为一个黑客或者一个好的实践，当然也希望听听其他无服务器从业者的想法。