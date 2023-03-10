# 😼KittenRouter:开源🔍弹性搜索监控、记录和🚦webtraffic 的故障切换(使用☁️ Cloudflare 工作人员)

> 原文：<https://dev.to/uilicious/elasticsearch-monitoring-logging-failover-of-webtraffic-using-opensource-kittenrouter-with-cloudflare-workers-4bf3>

<figure>

[![KittenRouter logo](img/61573fd09cee48ddf33ddb04992aa0cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4mY8XlVL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1j3qpkan01o7c2n9ugg1.png)

<figcaption>Special thanks to Nai Jie for the logo (he also did InboxKitten logo)</figcaption>

</figure>

# 向 InboxKitten 介绍 KittenRouter -无服务器表兄弟

因为我们在 API 中使用了 Cloudflare 无服务器组件。为什么不把它扩展到 UI 资产呢？

*   在 cloudflare 上记录每个 web 请求(这是[企业独有的功能](https://support.cloudflare.com/hc/en-us/articles/216672448-Cloudflare-Logs-formerly-ELS-))，帮助我们跟踪服务器故障。
*   服务器出错时自动进行故障转移，以维护整体站点可用性(高可用性！).
*   全部作为可重用的类模块

按照[“做一件事并把它做好”](https://en.wikipedia.org/wiki/Unix_philosophy)的理念保持项目的小规模，伪代码可以总结如下。

```
when receiving a request {
   for( each backend configuration ) {
      if (invalid backend configuration - wrong domain, etc) {
         continue - to next backend configuration iteration
      }
      perform cachable request to the backend
      log request to ElasticSearch backend - or alternative log provider
      if( 
         request result is valid || 
         is last configuration || 
         default backend configuration
      ) {
         return result - Ends request processing
      }
      continue - to next backend configuration iteration
   }      
}

# PS: The above is conceptually the synchronous flow. In the actual
# implementation, logging should occur asynchronously without blocking 
```

这允许我们在后端配置冗余，首先是 commons.host 服务器，然后是 firebase 作为“备份”。

* * *

# 背景语境(我们为什么做这个？)

[![picocreator image](img/9d01ba033264443700b6c4fc664da969.png)](/picocreator) [## 我们为什么迁移开源😼inbox kitten(7700 万个无服务器请求)来自🔥☁️Cloudflare 工人和🐑CommonsHost

### Eugene Cheah Mar 27 ' 196 分钟阅读

#showdev #whywe #serverless #javascript](/uilicious/why-we-migrated-serverless-inboxkitten-from-firebase-to-cloudflare-workers-and-commonshost-gd3)

当我们将 UI 前端从 Firebase 迁移到 [Commons 时。Host(一个开源和公共 CDN/静态主机提供商)](https://commons.host/)，我们面临的一个问题是随机远程位置的随机 404。比如俄罗斯——可能是因为那里缺少服务器。

<figure>

[![Commonshost Global Map](img/70b3793b2280a77a63a4379d8270c594.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T5vs1czY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mpy4xba5ennrfjt3phh9.png)

<figcaption>Commons.host, server locations at point of writing</figcaption>

</figure>

因为我们是不断发展的🇸🇬共享主机项目的支持者，我们决定将他们一起袖手旁观，而不是抛弃他们的替代品。

我们需要一种更好的方法来调试这些问题，同时提高服务的整体可靠性。

> 任何老大哥当局注意，我们只跟踪静态资产加载与 inboxkitten.com 的 IP 地址屏蔽。API 上不做任何日志记录。所以，不要麻烦问我这样的数据。

* * *

# Elasticsearch 监控:Commons 主机

最棒的是，使用 KittenRouter 对我们的 commons 主机部署进行的日志记录，我们可以开始获得关于其状态的硬数据。

<figure>

[![HTTP code logging](img/bf4f6100327fc50dc46037b248d94fe7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x1TAMUef--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ui2rilz7vuv581xg1qm8.png)

<figcaption>78 errors out of 12,069 requests ~ 0.65% failure rate</figcaption>

</figure>

目前在这样的设置中，我们有 99.35%的流量是通过 commons.host 的免费公共服务器路由的。

对于其余 0.65%失败的请求，它们会自动退回到 firebase 基础架构的空闲层。

虽然还不到 9 个 9，但考虑到这些 commons.host 服务器和 cloudflare 服务器分散在世界各地。[在大多数 cdn 的 98%真实用户指标范围内](https://www.cdnperf.com/#!rum)

[![Commons host dashboard](img/ac85e41d2a20dba3ad2aeac36e24082a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BALDD7Aq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4f3n8js8c6mzd2vlhwgb.png)

此外，有了提供的头信息，我们可以跟踪每个服务器的错误率。借助 Cloudflare 上的 KittenRouter 设置，您可以为任何现有站点配置此类监控，而无需更改应用程序代码。

此后，我们向 commons.host 团队提供了对上述 elasticsearch 集群的访问，以便在真实用户负载下提取关于其基础设施的有用数据。

> 澄清说明，出于隐私原因，commons.host 不会在其系统上为其平台上托管的站点执行这种级别的日志记录。
> 
> 我们在 Uilicious 的目的是为他们提供服务器的有用的真实世界使用指标，并有效地使我们的网站 inboxkitten.com 免于这种隐私保护，以帮助他们改善服务。

* * *

# Elasticsearch 监控:InboxKitten

[![KittenRouter world map](img/163eec4072bb6089aa28d5e44d700716.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mzV3gR_y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x54ehgmxsfkkicfke089.png)

最后，我们能够轻松地使用 cloudflare 国家代码来帮助我们跟踪我们的小猫世界统治地图- live！。喵-哈哈哈！

> 格陵兰和马达加斯加——我天生就有一双猫眼盯着你！

* * *

# 爽！我能在哪里得到代码

有关部署和配置的详细信息...

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [尤利西斯](https://github.com/uilicious) / [基廷罗特](https://github.com/uilicious/KittenRouter)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 什么是 KittenRouter

KittenRouter 是用于 [Cloudflare Workers](https://www.cloudflare.com/products/cloudflare-workers/) 的路由脚本，它尝试连接到指定服务器的列表，并将请求重定向到在该时间点当前“活动”的任何服务器。当您的服务器可能停机或无法处理请求时，这是非常有用的，KittenRouter 可以自动尝试将请求重定向到下一个配置的 URL 进行处理。

同时，它可以被配置为将信息记录到您的 ElasticSearch 服务器上，用于分析目的。记录的一些信息是服务器的状态、请求的国家等。有关全部细节，请参见`index.js`文件。

# 如何使用 KittenRouter

最终，KittenRouter 将与 Cloudflare workers 一起使用。有两种方法可以在 Cloudflare worker 脚本上使用 KittenRouter，

1.  使用 NPM 模块
2.  手动添加 KittenRouter

### 1)

…</article>

[View on GitHub](https://github.com/uilicious/KittenRouter)

[或者 NPM](https://www.npmjs.com/package/kittenrouter)

* * *

# 太酷了，但为什么我还要再次用 Inboxkitten 一次性邮箱呢？

当前的一个关键用例，也是我们构建这个项目的原因，是执行电子邮件验证作为我们自动化测试脚本的一部分。比如下面的...

```
 // Lets goto inbox kitten
I.goTo("https://inboxkitten.com");
I.see("Open-Source Disposable Email");

// Go to a random inbox inbox 
I.fill("email", SAMPLE.id(22));
I.click("Get Mail Nyow!");

// Check that its empty
I.see("There for no messages for this kitten :(");

// Testing for regular email
// (sent using a jenkins perodic build)
I.goTo("https://inboxkitten.com");
I.see("Open-Source Disposable Email");
I.fill("email", "ik-reciever-f7s1g28");
I.click("Get Mail Nyow!");

// See an email we expect, nyow
I.see("Testing inboxkitten subject"); 
```

可共享的测试结果，例如



<figure>[![uilicious demo](img/3013c149fffa5ecaad468994047f3e94.png)](https://snippet.uilicious.com/test/public/7t74nVS828weKMtzGgJppF)</figure>

此外，它简单、酷、有趣。

* * *

# KittenRouter 的下一步是什么？

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 非常粗略的 KittenRouter【3】](https://github.com/uilicious/KittenRouter/issues/3)所需的改进路线图

[![PicoCreator avatar](img/7d9c85b16a139dfe22b8918d388ce270.png)](https://github.com/PicoCreator) **[PicoCreator](https://github.com/PicoCreator)** posted on [<time datetime="2019-05-10T08:20:20Z">May 10, 2019</time>](https://github.com/uilicious/KittenRouter/issues/3)

接下来是什么？为了 KittenRouter

*   更多可配置的后端选项
    *   请求域，路径，区域
    *   请求超时
    *   错误/传递 HTTP 代码控制
    *   自定义标题控件
    *   作为特定 IP 上的指定域执行请求
*   更多日志选项
    *   参数记录白名单/黑名单
        *   头球
        *   饼干
*   更多日志记录提供程序
    *   ？？？

[View on GitHub](https://github.com/uilicious/KittenRouter/issues/3)

让我们看看这个 InboxKitten 之旅接下来会走向何方...在那之前，我们会睡个午觉！

[![Cat sleeping](img/0ac0565b100727fe92157fc54d88a85d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RN8MuqYx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8fmx06l7bnvogfxptd1f.jpg)

* * *

# 快乐航运🖖🏼🚀