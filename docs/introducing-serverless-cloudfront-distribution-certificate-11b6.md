# 无服务器简介-cloudfront-distribution-certificate

> 原文：<https://dev.to/pfulop/introducing-serverless-cloudfront-distribution-certificate-11b6>

<figure>[![](img/4d07f53dc6471d54c8e37d74f0fd8626.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U9w9tsMD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AKzqeWt5kJ10llTUTy7dNJA.png) 

<figcaption>**来源:undraw.co**</figcaption>

</figure>

我需要一个无服务器插件来为我的 CloudFront 发行版创建一个证书。虽然有一些插件可以请求证书，但它们通常只能将证书分配给 APIGateway，或者它们会为您创建 CloudFront 发行版。

#### 现有插件

[https://github . com/schwamster/server less-certificate-creator](https://github.com/schwamster/serverless-certificate-creator)T2【https://github . com/amplify-education/server less-domain-manager

#### My plugin

我需要的是可以简单地将证书分配给与 CloudFront 发行版相关的域的东西。使用 CloudFormation ACM entry 已经可以做到这一点，但是您需要自己验证证书。如果你不这样做，你的栈创建过程将会停滞。

因此，如果需要，这个插件可以很容易地创建证书，创建一个 route53 条目来验证它，最后将证书 ARN 分配给你的 CloudFront 发行版。

如果你觉得这是你需要的，去看看吧。

[pfulop/无服务器-cloudfront-distribution-certificate](https://github.com/pfulop/serverless-cloudfront-distribution-certificate)