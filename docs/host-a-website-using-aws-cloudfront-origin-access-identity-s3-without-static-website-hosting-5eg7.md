# 使用 AWS CloudFront、Origin Access Identity、S3 托管网站，无需静态网站托管

> 原文：<https://dev.to/sanamsoodan/host-a-website-using-aws-cloudfront-origin-access-identity-s3-without-static-website-hosting-5eg7>

第二个教程是在 AWS 上托管一个网站，并通过使用原始访问身份来限制对亚马逊 S3 内容的访问。

第一步:注册域名
购买你选择的域名。例如 sanamdeep.com。你可以从 AWS、Google domains、Go Daddy 和其他提供商那里购买你的域名。对于这个博客的范围，我们将考虑域是 sanamdeep.com。您将使用您选择的域，并用下面步骤中的用法替换它们。
要从 AWS 购买域名，请访问 Route53 并查看您选择的可用域名。
第二步:AWS 设置
这一步包括多个子步骤。

设置 ACM 或 AWS 证书管理器。ACM 处理为基于 AWS 的网站和应用程序创建和管理公共 SSL/TLS 证书的复杂性。
进入 AWS 证书管理器，选择“域名”下的“申请证书”>“申请公共证书”>，输入 sanamdeep.com 和*.sanamdeep.com。第二个域名是所有的子域名，如 www.sanamdeep.com 的。>选择【DNS 验证】>审核>确认和请求。
在这一步之后，如果你已经从 AWS 购买了你的域名，那么你将需要等待 10-15 分钟，证书才会被颁发。否则，如果您已经从第三方域提供商处购买了您的域，那么您将需要在您请求的证书中单击“将 DNS 配置导出到文件”选项，并将 DNS 名称和值复制到您的域设置中。
例如，对于谷歌域名，您可以转到您的特定域名，在 DNS >自定义资源记录下，您将添加 DNS 条目。仅输入唯一的条目。
只有在完成此步骤后，AWS 才会花费 10-15 分钟来颁发证书。
设置 S3 水桶
去 www.sanamdeep.com S3 制作水桶。出于示例网站的目的，使用 Hello world HTML 文件。
设置 CloudFront
现在我们将设置两个 CloudFront，一个用于 sanamdeep.com，另一个用于 www.sanamdeep.com
首先让我们从 www.sanamdeep.com创建 CloudFront。
创建分销>网站>入门>在源域路径下你需要为[www.sanamdeep.com](http://www.sanamdeep.com)添加 S3 桶。
注意:选择显示在源域路径下的选项。
输入原点域路径后，原点 ID 会自动填充。
在限制铲斗访问下选择是。现在，您将看到源访问身份选项。如果您之前没有创建身份，请选择“是”,否则使用现有身份。
在授予对存储桶的读取权限下选择是。这将自动添加一个 bucket 策略，授予 CloudFront 访问 S3 对象的权限。
在默认缓存行为设置下>查看器协议策略选择重定向 Http 到 Https。
在分发设置下>备用域名>输入[www.sanamdeep.com](http://www.sanamdeep.com)T29】在分发设置下> SSL 证书>选择自定义 SSL 证书，在分发设置下>默认根对象类型 index.html
点击创建分发。此后，部署 CloudFront 将需要 20-30 分钟。
对 sanamdeep.com
重复这个过程，因为我们只创建了一个桶，所以我们在这里也将使用[www.sanamdeep.com](http://www.sanamdeep.com)。
使用 sanamdeep.com 作为备用域名
在分发设置下输入 index.html>默认根对象为 sanamdeep.com
其余的设置将与之前的 CloudFront 类似。
注意:这个云锋的目的是重定向到 sanamdeep.com 的流量，以重定向到[www.sanamdeep.com](http://www.sanamdeep.com)桶。因此设置将保持不变。在此步骤中，在托管区域>创建托管区域
下，设置路由 53
T42 输入域为 sanamdeep.com，然后单击创建。
现在，在新创建的托管区域 sanamdeep.com 下，您会发现两个自动创建的记录集。
其中一个将拥有包含四个条目的名称服务器。
现在点击创建记录集>下的名字>把 sanamdeep.com
检查别名为是>选择别名目标为对应的 CloudFront。确保选择正确的别名目标。
按照上述步骤为[www.sanamdeep.com](http://www.sanamdeep.com)创建一个记录集。
为第三方提供商复制 DNS 条目
我们已经从证书管理器复制了 DNS 条目。但是对于从 AWS 使用域名的人，如果没有自动添加，你需要复制 Route53 下的 DNS 条目。
复制域名服务器
此步骤针对来自第三方提供商的域，复制 Route53 中生成的域名服务器，并将其添加到域中。例如，对于谷歌域名，它们可以添加到 DNS >域名服务器
下。在这一步之后，你可以通过[www.sanamdeep.com](http://www.sanamdeep.com)或 sanamdeep.com 打开你的网站。两者都将被重定向到[https://www.sanamdeep.com](https://www.sanamdeep.com)

网站上没有反映的更改
同样，如果您通过更改 S3 桶中的内容来更改您的网站。CloudFront 可能需要一段时间才能自动反映这些变化。一个更快的方法是去 CloudFront>Invalidations>Create Invalidation，如果你想使所有文件无效，输入/*。在[www.sanamdeep.com](http://www.sanamdeep.com)和 sanamdeep.com 上创建无效后，更改将立即反映出来。