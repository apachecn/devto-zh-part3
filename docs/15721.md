# 使用 Go 和云功能调整图像大小

> 原文：<https://dev.to/didil/image-resizing-with-go-and-cloud-functions-47f8>

#### Go +无服务器= ❤️

Google 云平台现在[支持 Go 1.11 的云功能](https://cloud.google.com/blog/products/application-development/cloud-functions-go-1-11-is-now-a-supported-language)。Go 和无服务器是这几天我非常兴奋的两件事情，所以我决定尝试一下这个新的 GCP 功能，并使用[分解/成像](https://github.com/disintegration/imaging)包在 pure Go 中构建一个基本的图像大小调整程序。

#### 调整地鼠大小

有时你只是有太大的地鼠图片，无法在你的网站上显示。他们会吃掉你的用户宝贵的带宽！就像这个巨大的:

[![](img/f68afd40ddb8223178b45b5fede3cc52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cMOD8g8N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ak74qnaAcJd3bzRj7PnLIbg.jpeg) 

<figcaption>使用 gopherize.me 生成的大地鼠</figcaption>

所以我们会把它变成这个更小更合理的地鼠:

[![](img/53e4829ec4fbfde80b21391a91e86083.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NW6CLrAe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/289/1%2AXHNGwmff9RaxUtRkVaggXA.jpeg) 

<figcaption>小地鼠</figcaption>

为了实现这一点，我们将构建一个带有端点的云函数，可以通过以下方式调用该函数:

https://{ region }-{ project-name }。_cloudfunctions.net/ResizeImage？_url={url} &高度={height} &宽度={width}

我们只需发送一个 HTTP GET 请求，其中包含我们的图像 url、所需的宽度和高度，然后我们会在 HTTP 响应正文中收到一个 JPEG 格式的图像。

#### 设置

为了跟进，你需要在你的开发盒上安装 Go 1.11，一个谷歌云平台账户和 gcloud 命令行工具[设置](https://cloud.google.com/functions/docs/quickstart#functions-update-install-gcloud-go)。

我已经设置了一个 github repo ,如果你想在本地浏览/克隆它，并在阅读时进行测试。

#### resize image 云函数

我们将使用的云函数是一个 *HTTP 函数。*这个云函数类型为我们的代码提供了一个 HTTPS 端点，而不需要额外的 API 网关，这对于我们的用例来说非常方便。另一种云功能类型:*后台功能*可以由发布/订阅或云存储事件等事件触发。我们今天不会使用它，但它可以在其他任务中派上用场，例如缓存我们的图像，定期清理缓存等。我可能会在以后的文章中写一些关于*背景函数*的内容。

ResizeImage 函数需要做以下事情:

*   将查询字符串解析为 url、高度和宽度
*   获取原始图像
*   调整图像大小
*   将输出图像编码为 Jpg
*   将编码的输出图像写入 HTTP 响应流

为了简单起见，我将所有代码放在一个文件中。文件名不重要。