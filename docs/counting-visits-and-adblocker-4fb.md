# 统计访问量和广告拦截器

> 原文：<https://dev.to/jaga/counting-visits-and-adblocker-4fb>

现在很多用户使用某种广告拦截器，我个人使用的是 [uBlock origin](https://chrome.google.com/webstore/detail/ublock-origin/cjpalhdlnbpafiamejdnhcphjbkeiagm?hl=it) 。

## 它这只是为了广告对吗？

嗯，不，大多数广告拦截器也阻止跟踪事件，包括最著名的跟踪系统，它是大多数公司非常简单的设置和转到工具[谷歌分析](https://analytics.google.com/analytics/web/)

## 拦截器是如何工作的？

作为一个浏览器扩展，拦截器位于互联网和浏览器之间，它可以阻止来自您正在导航到另一个 URL 匹配的网站的请求，例如，黑名单中的域。

[![request-blocked](img/e21f2a12e1dbe8f0e0929c47d57e0364.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SUM_avDE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jagascript.com/static/f87b71417a61ff8d45afdd18174347f7/a792c/request-blocked.png)

> 这个眼熟吗？

## 什么问题

显而易见的问题是，一篇文章的浏览量或最常用设备的数量等访问统计数据将不再符合现实，因为它只显示没有广告拦截器的设备，而且根据你的受众，这可能是很大一部分，如果不是大部分的话。

拥有您无法信任或做出决策的数据只是无用的数据。

对于我的个人项目，我想知道的是访问计数除以页面和访问的来源(slack，twitter，google 等)，来自哪个国家的访问者请求页面是一件好事，但不是必需的。

## 解

使用其他东西，不在黑名单上的东西

### 初次尝试

(这很有效，我现在正在使用它，最后没有成功是我的错，细节以后再说)

> ![Jaga santagostino profile image](img/5b96b70ecb6a987a88190a3e3e23d128.png)Jaga santagostino@ kand ROS 5591![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)什么是不被广告拦截器拦截的轻松网站分析 goto 工具？一个有 viewcount total 和 trend 的仪表盘就够了，位置好看有00:48AM-04 2019 年 5 月[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1124475843881861121)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1124475843881861121)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1124475843881861121)0

在 twitter 上我的一个朋友的建议下，我尝试使用 [simpleanalytics.io](https://simpleanalytics.io/) ，它提供了一个 HTML 脚本标签来插入到你的页面中，并提供了一个仪表板来查看请求，它显示设备类型、国家、来源。

够我受的了！

价格非常好，每月 9 美元，网站不限，所以我可以用它来管理我所有的域名，这太棒了。

不幸的是，我尝试了一下，我的广告拦截器阻止了它。

[![booo](img/a5ff945c2804710979e04f87e1f58bbb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QTLUJ5eP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media2.giphy.com/media/3ornjUrHrU17Cb1JFS/giphy.gif%3Fcid%3D790b76115cceb187393370332e89dd7e%26rid%3Dgiphy.gif)

嗯，这是一个跟踪服务，难怪它最终进入了巨大的黑名单。

### 第二次尝试

所以我继续在 *go* 的 *aws-lambda* 上使用 *dynamodb* 构建了我的穷人版访问计数器，它使用了 lambdas 的一个很好的特性，即 api-gateway 从 CloudFront 接收头，其中包含一些令人惊讶的信息

这里是访问跟踪器的报告 [go-visits-cloudfront](https://github.com/kandros/go-visits-cloudfront)

它的工作，没有前端不是仪表板在此刻，但它令人惊讶的有用的 2 小时周六项目建立在一个酒吧。

### 第三次尝试

与此同时，twitter 帖子继续，simpleanalytics.io 的作者补充了一条备受赞赏的信息

> ![Adriaan van Rossum profile image](img/7e1019b6d52a3e06d0830ab33073e3aa.png)阿德里安·范·罗斯姆@阿德里安·范·罗斯姆![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ kand ROS 5591](https://twitter.com/kandros5591)[@ siscia _](https://twitter.com/siscia_)[@ cloud flare](https://twitter.com/Cloudflare)[@ simple analytic](https://twitter.com/SimpleAnalytic)我们有一个功能叫子域广告拦截器绕过，你只要把一个 CNAME 链接到我们的服务器就大功告成了。我们设置 SSL 并完成剩下的工作。拜拜广告拦截器；-)2019 年 05 月 04 日下午 12:54[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1124658593146519553)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1124658593146519553)2[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1124658593146519553)2

添加一个 *cname* 重定向从您的一个子域到 simpleanalytics 的 api，我们可以绕过广告拦截器，鉴于 simpleanalitycs 是基于隐私和“做一个好的互联网公民”为核心的事实，我这样做没有问题。

解决方案既简单又聪明。

### SSL 呢？

如果您想知道它如何与 https 一起工作，您可以在 simpleanalytics 仪表板中设置您选择的子域，它会神奇地使用 SSL 证书为您实现安全的数据通信。使用来自不同 registars 的域名(Godaddy，Google，Amazon Route 53，Zeit，Netlify 通过他们的域名服务器),我什么也不用做。

### 单页应用和盖茨比呢？

对于 SPA，它只是工作，详细信息在他们的文档中有一页 [doc](https://docs.simpleanalytics.com/trigger-custom-page-views) 。

对于[盖茨比](https://gatsbyjs.org)，我只需要使用 SSR api
将脚本添加到“盖茨比之路”页面中