# 用部署在 AWS 上的 GatsbyJS 和 Ghost 构建个人网站

> 原文：<https://dev.to/nishantdania/building-a-personal-website-with-gatsbyjs-and-ghost-deployed-on-aws-2298>

作为一名 web 开发人员，我喜欢完全控制我的网站的外观和行为。随着 ReactJS 的日益流行，我决定使用 [GatsbyJS](https://www.gatsbyjs.org/) ，这是一个使用 React 构建静态站点的框架。

然而...

虽然 GatsbyJS 对于我的网站前端来说是一个完美的解决方案，但它缺少一个用于我的博客文章的 CMS(内容管理系统)和一个在线编辑器。我知道我可以使用 Ghost 作为我的博客设置，但是我不想使用它的基于 EmberJS 的前端，我想坚持使用 Gatsby。

我开始在网上搜索如何将 Ghost 连接到我的 Gatsby 网站，幸运的是 Ghost 刚刚[发布了](https://blog.ghost.org/jamstack/)其内容 API V2，即一个将 Ghost 用作任何前端层的 CMS 的 API。

这就是我的个人网站的设置。这是一个基于 Gatsby 的网站，它从我在 AWS 上托管的一个 Ghost 服务器中为[博客](https://nishantdania.com/blog/)页面提取数据。我使用 Ghost 编辑器来写我的帖子，这意味着我不必每次发布东西时都推送任何降价文件。

我使用 AWS Route53 来托管我的域，一个 AWS S3 桶来服务 Ghost 生成的所有静态文件，一个 AWS Cloudfront 实例，这是亚马逊的 CDN 服务，也为我的 S3 桶提供免费的 SSL，一个运行我的 Ghost 服务器的 AWS EC2 实例，最重要的是，我使用 AWS CodeBuild 来允许我只需单击一下就可以自动部署我的站点。

"哇，对于经营一个网站的人来说，这可是一大笔钱啊！"

我知道。但这是值得的。我现在可以在 Ghost 编辑器中写作，并在移动中通过点击发布我的内容，同时仍然保持对我的网站外观的完全控制。

在本文之后的一系列博客文章中，我将介绍整个设置过程。我希望你在这个过程中学到一些新的东西，并且想知道你是否用这种方法建立了你的网站。围绕这个话题有什么问题，请发帖到[鬼盖茨比频谱频道](https://spectrum.chat/ghost-gatsby)。

给自己倒杯咖啡，放点好音乐，让我们开始吧。

[Ghost & Gatsby Part 1 -设置 AWS S3、Cloudfront 和 Route53 来托管项目](https://nishantdania.com/blog/ghost-gatsby-part-1-setting-up-the-s3-bucket-to-host-the-project)
[Ghost&Gatsby Part 2-在 AWS EC2](https://nishantdania.com/blog/ghost-gatsby-part-2-setting-up-a-ghost-blog-on-aws-ec2)
[Ghost&Gatsby Part 3-设置带有 gatsby-source-ghost 插件的 Gatsby 站点](https://nishantdania.com/blog/ghost-gatsby-part-3-setting-up-a-gatsby-site-with-gatsby-source-ghost-plugin)
[Ghost&Gatsby Part 4-设置用于自动部署站点的 AWS 代码构建](https://nishantdania.com/blog/ghost-gatsby-part-4-setting-up-aws-codebuild-for-auto-deploying-the-site)