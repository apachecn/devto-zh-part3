# 在 AWS Cloudfront 上使用 Lambda@Edge 实现单页面应用(SPA)

> 原文：<https://dev.to/mengjiann/using-lambda-edge-for-single-page-application-spa-on-aws-cloudfront-4f98>

最近，我在工作中一直在做的项目上有一个新的要求，就是要启用社交分享。这是一个股票摄影网站，允许用户购买不同分辨率的照片，类似于 Shutterstock 或 GettyImages。这项新功能要求在社交网站上分享个人照片的网址。

[![Social Sharing](img/4ef30eca34dae2712b486e8e09096344.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5pGnX7cy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3n3mzxgetlkutaum5s0j.jpg)

对于普通网站来说，实现这样的功能是微不足道的，因为普通网站可以将基本元数据添加到每个 HTML 页面中。由于前端 app 是用 Vuejs 实现的，所以比较复杂。这是因为像脸书或 Twitter **这样的社交网站不会在抓取的页面上执行 javascript** 来分享。因此，不可能使用库来动态更新 HTML 页面元数据。

解决这个问题的方法之一是实现页面的服务器端呈现。但是，这将需要额外的前端应用程序托管，因为当前的设置利用 AWS S3 和 AWS Cloudfront 进行静态 web 托管。这种设置被认为是具有成本效益的，并且很好地照顾了前端应用程序的可扩展性。

[![Lambda@Edge](img/dabe5b25b26f9d586656a3fcc3e46ff2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vzs3dU9g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1lcerw1bro8b548bv4xc.png)

几个小时的网上研究让我发现 Lambda@Edge 可以部署到 AWS Cloudfront 来拦截和定制 HTTP 请求。通过**检查用户代理头**的请求来源，我们可以在 Lambda@Edge 函数中修改返回的响应体。

这就是它的工作原理。

[![Flow Diagram](img/d512afd59fe2dd9390acdfca7ce7deb2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E5ETfQxo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uhb1xb9pkirjnkl2lp9i.jpg)

绿线代表普通用户从浏览器访问的请求流。然后，红线代表来自社交网站的请求。

正如我所写的，AWS Lambda@Edge 有一些限制:

*   Cloudfront 的 Lambda@Edge 支持的运行时只有 **Nodejs** 。
*   Lambda@Edge 功能只能部署在**北弗吉尼亚地区**。
*   Lambda@Edge 功能超时只能配置为 **5 秒**。
*   Lambda@Edge 函数**不支持环境变量**，我已经用 Gulp 脚本处理过了。或者您可以尝试 AWS 系统管理器参数存储。
*   Lambda@Edge 函数无法部署到 VPC。

对于部署，您必须:

*   发布 Lambda@Edge 函数的新版本。
*   用新的 Lambda ARN 更新 Cloudfront 发行版。

并且您需要向 Lambda 角色添加所需的权限。参见此处的

有关 AWS Lambda@Edge 代码示例，请参考以下 Github repo:

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png) [蒙嘉恩](https://github.com/mengjiann)/[AWS-lambda-edge-cloudfront](https://github.com/mengjiann/aws-lambda-edge-cloudfront)

### Cloudfront 的 AWS Lambda Edge

<article class="markdown-body entry-content p-5" itemprop="text">

# Cloudfront 的 AWS Lambda Edge

这是一个 Lambda@Edge 函数，用于为通过 Cloudfront 服务的单页面应用程序(SPA)定制响应主体。

它允许为社交网站(包括脸书、WhatsApp、Twitter)返回静态 html，用于丰富的社交共享(由于单页面应用程序的性质——SPA)。

# 先决条件

*   npm -可以通过自制软件安装`brew install node`
*   一饮而尽

# 准备用于部署的 zip 文件的指南

*   更新 environment-config.json 中的 externalApi
*   `npm install`
*   环境:开发/生产
*   `archive.zip`将在根文件夹中创建并可以部署到 AWS Lambda

# 注意事项

*   部署到 Cloudfront 的 Lambda@Edge 不支持环境变量。因此，gulp 用于自动执行替换 env 变量的任务，并为上传创建工件。或者可以使用 AWS 系统管理器参数存储。
*   AWS Lambda…

</article>

[View on GitHub](https://github.com/mengjiann/aws-lambda-edge-cloudfront)

参考:

1.  打开图形协议:[http://ogp.me/](http://ogp.me/)
2.  Vue 社交分享插件:[https://github.com/nicolasbeauvais/vue-social-sharing](https://github.com/nicolasbeauvais/vue-social-sharing)
3.  Vue 服务器端渲染:[https://vuejs.org/v2/guide/ssr.html](https://vuejs.org/v2/guide/ssr.html)
4.  官方 AWS Lambda @ Edge doc:[https://docs . AWS . Amazon . com/Lambda/latest/DG/Lambda-Edge . html](https://docs.aws.amazon.com/lambda/latest/dg/lambda-edge.html)
5.  官方 AWS Lambda@Edge 限制:[https://docs . AWS . Amazon . com/Amazon cloudfront/latest/developer guide/Lambda-requirements-limits . html # Lambda-requirements-Lambda-function-configuration](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-requirements-limits.html#lambda-requirements-lambda-function-configuration)