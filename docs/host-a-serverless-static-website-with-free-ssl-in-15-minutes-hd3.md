# 15 分钟内用免费 SSL 托管一个无服务器静态网站。

> 原文：<https://dev.to/arswaw/host-a-serverless-static-website-with-free-ssl-in-15-minutes-hd3>

在本指南中，我将向您展示如何以无服务器的方式使用 AWS 托管一个静态网站。

将使用以下 AWS 服务:

*   简单存储服务(S3)
*   53 号公路
*   云锋
*   证书管理员

Angular、Vue 和 React 应用程序可以编译成静态文件，这些文件可以从 S3 桶中提供。如果你能提供这些文件，你可以从 S3 主办一个动态网站。

您需要一个 AWS 帐户来阅读本指南。

对于本指南，在 Route53 中有一个域是最容易的。如果没有，请转到当前注册商的 DNS 设置。以主机“www”和 Cloudfront URL 为目标创建一个 CNAME 记录。当您请求证书时，请确保将您的域指定为*.yourdomain.com，然后将 www 和 no www 变体添加到您的 Cloudfront 发行版的备用域名字段中。

可以通过 SDK 或使用 Cloudformation 来完成这些步骤。

#### 步骤 1 -创建一个 S3 存储桶。

我们稍后将重新访问我们的 S3 存储桶，以调整存储桶策略。

1.  在 AWS 控制台中访问 [S3](https://s3.console.aws.amazon.com/s3/home) 。
2.  点击左上角的“创建存储桶”。
3.  建议您的 S3 存储桶名称与您的域相同。(yoursite.com)没有 www 或 http。
4.  选择您喜欢的地区。这可能是离你最近的一个。
5.  单击“下一步”，您将看到“配置选项”一切保留默认值。
6.  点击“下一步”，你会看到“设置权限。”同样，一切都保持默认。
7.  单击“下一步”，然后单击“创建存储桶”
8.  为你的网站上传所有的静态文件和文件夹。

在我们创建一个 Cloudfront 发行版之前，我们无法继续前进。一旦我们这样做了，我们就可以调整 bucket 策略来限制对我们创建的 Cloudfront 发行版的访问。

#### 步骤 2——创建一个 Cloudfront 发行版。

这是 Route53 将访问您的静态网站的资源。它将从你的 S3 桶中提取网站的文件。

1.  在 AWS 控制台中访问 [Cloudfront](https://console.aws.amazon.com/cloudfront/home) 。
2.  点击左上角的“创建分发”。
3.  在“Web”下，单击“开始”
4.  在“源域名”输入中点击，并选择您的 S3 桶。
5.  点击“原产地 ID”输入，并输入您的 S3 桶名称。
6.  在“查看器协议策略”旁边，选择“将 HTTP 重定向到 HTTPs”
7.  选择对您有意义的最小、最大和默认 TTL。在默认 TTL 中，输入 Cloudfront 每次查看您的 S3 桶和缓存您对网站所做的更改之间的秒数。我用 86400 秒，或者一天。
8.  在“价格等级”旁边，选择您想要支持的边缘位置。使用更多的边缘位置会更昂贵。
9.  您可以指定另一个域名，如“[www.yoursite.com](http://www.yoursite.com)
10.  在“SSL 证书”旁边，选择“自定 SSL 证书”，然后点按“使用 ACM 请求或导入证书”这将在新标签中打开证书管理器。

让 Cloudfront 选项卡保持打开状态。我们很快会回来。

#### 步骤 3 -请求 SSL 证书。

您应该在证书管理器中，Cloudfront 发行版设置在另一个选项卡中。

请确保您的域名在 Route53 中，并且注册人的联系方式是您可以访问的电子邮件。

1.  在“域名”下输入你的域名
2.  点击“下一步”并选择“电子邮件验证”这通常是最简单的方法。
3.  点击“审核”，然后点击“确认和请求”
4.  转到您的电子邮件，然后确认请求。
5.  您的证书应该将“验证状态”显示为“成功”
6.  复制你的证书 ARN。

您已经创建了有效的证书。AWS 证书管理器将处理续订。关闭这个选项卡，切换回 Cloudfront 设置。

#### 步骤 4 -完成 Cloudfront 设置。

您正在将刚刚创建的证书添加到正在进行的 Cloudfront 发行版中。

1.  在“自定义 SSL 证书”下的字段中，粘贴您的 ARN。
2.  将其余选项保留为默认选项，并选择“创建发行版”
3.  当您的分发状态为“已启用”时，请在最左边的列中选择“原始访问身份”。
4.  选择左上角的“创建原始访问身份”。
5.  你的评论应该是你的 S3 桶的名字。
6.  单击“创建”,然后复制刚刚创建的身份的 ID 列。
7.  回到 S3。

接下来，我们将把您的静态文件上传到 S3，并添加一个 bucket 策略来允许从 Cloudfront 进行安全读取。

#### 步骤 5 -调整您的存储桶策略。

你应该回到你的 S3 桶里。

1.  选择您的存储桶上的“权限”选项卡。
2.  选择“存储桶策略”按钮。
3.  将以下存储桶策略粘贴到。您需要将 XXXXXXX 更改为您的原始访问身份。您还需要将 yourdomain.com 更改为您的新 S3 存储桶的名称。

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "2",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity XXXXXXXXXXX"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::yourdomain.com/*"
        }
    ]
} 
```

1.  点击“保存”并返回“概述”选项卡。
2.  去 53 号公路。

最后一步是将您的域解析到您的 Cloudfront 发行版。

#### 步骤 6 -将您的域解析到 Cloudfront

你应该在 53 路。如果您尚未为您的域创建托管区域，请执行以下步骤:

1.  点击左上角的“创建托管区域”按钮。
2.  请输入您的域名，不要使用 www 或 http。
3.  在“评论”栏中输入您网站的名称。
4.  点击“创建”
5.  单击“创建记录集”
6.  名字是“www”
7.  类型是 CNAME。
8.  该值是您的域名，不含 www 或 http。
9.  点击“保存记录集”
10.  再次单击“创建记录集”。
11.  名称为空。
12.  类型是“A”
13.  选择“别名”旁边的“是”
14.  在“别名目标”旁边，选择您的 Cloudfront 发行版。
15.  选择“创建记录集”

在 1-2 分钟内，打开一个新的标签，看看你的域名是否有 www 解析到你的静态网站。如果没有，请检查您的步骤是否正确。如果还是不行，就在帖子下面留言评论。

我不知道如何结束 markdown 中的代码块。我正在使用 dilinger.io 进行检查，当我放入一个三重反斜杠时，代码块发生了转义。似乎不在这里。我已经在 Twitter 上联系了 dev.to，希望会有答案。

似乎语法已经固定下来了。奇怪。

我希望这能帮助那些正在寻找一种廉价的方法来托管一个安全的、无服务器的静态网站的人。

谢谢你。