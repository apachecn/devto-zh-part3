# 将内容从 WordPress 迁移到 Cosmic JS

> 原文：<https://dev.to/carsoncgibbons/migrating-content-from-wordpress-to-cosmic-js-54ah>

[![image](img/09793ccccce6a646172270fbde636ec9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yqYkbHWF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tgi5j7bdu4nps59eaux8.gif)

世界上超过 30%的网站由 WordPress 驱动，然而参与 2017 年堆栈溢出开发者调查的 64%的开发者表示他们害怕使用 WordPress。主要原因包括安全性、膨胀、糟糕的用户体验以及 WordPress 是为过时的网络架构而构建的。如果你目前正在使用 WordPress，你可能会有同样的感受，并且知道技术膨胀和缓慢的页面加载速度会降低整体用户体验。

Cosmic JS 是 WordPress 的替代产品，提供了一个创建和管理内容的 web 仪表板，以及将内容集成到任何新的或现有的网站或应用程序的 API 工具和资源。在这篇博客中，我将演示如何将 WordPress 的帖子导入到 Cosmic JS 中，以便集成到一个新的现代应用程序中。我们可以通过简单地创建一个新的 Bucket，安装 [WordPress 导入器扩展](https://cosmicjs.com/extensions/wordpress-importer)并运行它来完成这个任务。

**TL；DR:**
[WordPress 导入器扩展](https://cosmicjs.com/extensions/wordpress-importer)
[性能案例研究:从 WordPress 到 JAMstack](https://cosmicjs.com/articles/from-wordpress-to-jamstack-how-to-make-your-website-10x-faster)

**创建一个桶**
[![image](img/c688b7143feccf5a6ad6b2eca3748aed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DLjUTzUb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m42nxc2rpvdo1w06d1gt.png)

**导航到设置>扩展> WordPress 导入器扩展**
[![image](img/a98432b17044f19db9847ba02c3b5b7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UTD1qgSQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o1qlil8cmrm429t1602g.png)

**安装 WordPress 导入器扩展**
[![image](img/1692e0c0dd1c17a36dc56736b688dde7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mI9Tjiv2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r8as4kl7eniq38zzr1x6.png)

**添加你的 WordPress 博客订阅源 URL**
[![image](img/7c9390f3269e4764ffed372c9c3a99e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XIezRQJl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oe0x8gnsf2vtivw836g0.png)

**导入 WordPress 内容**
[![image](img/9f262ba4995c6efe1e2936302ca2c2c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6v2thvoi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vf94c6o0w7jup019uvzg.gif)

**帖子导入成功屏幕**
[![image](img/5490529477926e2552340dc0c4d1f54b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H3qqAchy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k2djheewa457riew55jt.png)

**迁移宇宙 JS 仪表盘中的 WordPress 帖子**
[![image](img/7a94c2642eea6cd85be866a10a41b282.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vwKhfuE4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4niphq39oy2fk9pupjcy.png)

**使用 NPM 模块、GraphQL、Bash 或 Curl 整合内容**
[![image](img/f949902e9e06eedb1f3682cd62ac7ab0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vtKjAQXS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gfj34v685o26tu0c8kyb.png)

既然我们的 WordPress 帖子已经导入到了 Cosmic JS 中，我们就可以检查我们的内容模型，并使用 JavaScript、GraphQL、Bash 或 Curl 的代码片段资源准备内容，以便集成到新的应用程序中。你可以很容易地从 [Cosmic JS Apps Marketplace](https://cosmicjs.com/apps) 安装一个内容就绪的应用程序，并导入你的 WordPress 文章用于 Netlify 部署，以测量页面加载速度的增加。

**结论**
将开发者和内容创作者从 WordPress 中解放出来是一个很大的目标，WordPress Importer 扩展是一个很好的工具，有助于说明当使用 Cosmic JS 管理时，你的内容变得多么灵活、可移植和可扩展。如果你对将你的 WordPress 内容迁移到 Cosmic JS 有任何意见或问题，[在 Twitter 上联系我们](https://twitter.com/cosmic_js)和[加入 Slack 上的对话](https://cosmicslack.now.sh)。