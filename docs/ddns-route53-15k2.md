# ddns-路由 53

> 原文：<https://dev.to/crazymax/ddns-route53-15k2>

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [疯狂-max ](https://github.com/crazy-max) / [ ddns-route53](https://github.com/crazy-max/ddns-route53)

### 基于时间的亚马逊路线 53 的🛣动态 DNS

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/bec8c778800557678e36f29d29bc66d7.png)](https://github.com/crazy-max/ddns-route53)

[![Documentation](img/01221af2806e71fb057d0bd9f66786f7.png)](https://crazy-max.github.io/ddns-route53/)[![GitHub release](img/64c184423d7b57dbd3ce29887b759251.png)](https://github.com/crazy-max/ddns-route53/releases/latest)[![Total downloads](img/a85f8a1df3e9b8c0bc5e5e3f98cff5ba.png)](https://github.com/crazy-max/ddns-route53/releases/latest)[![Build Status](img/ffc1a08960c6ee01e3fabcd23da9038a.png)](https://github.com/crazy-max/ddns-route53/actions?workflow=build)[![Docker Stars](img/de629aa9c4424708dad2016c50a45afc.png)](https://hub.docker.com/r/crazymax/ddns-route53/)[![Docker Pulls](img/9b6b743086c80515b6d99e0dcad4e163.png)](https://hub.docker.com/r/crazymax/ddns-route53/)
[![Go Report](img/a6673c2385d62ff06f0f61826480f0db.png)](https://goreportcard.com/report/github.com/crazy-max/ddns-route53)[![Code Quality](img/b78d5939be29c089d8cee4eeeeba1f32.png)](https://app.codacy.com/manual/crazy-max/ddns-route53)[![Codecov](img/8396cf374ca4255f0d3382d5aeaf2d31.png)](https://codecov.io/gh/crazy-max/ddns-route53)[![Become a sponsor](img/8622aa159ba302f981f4f2f291369dd3.png)](https://github.com/sponsors/crazy-max)[![Donate Paypal](img/f3563d97c76d0f36756536747d40f632.png)](https://www.paypal.me/crazyws)

## 关于

**ddns-route53** 是一个 CLI 应用程序，用 [Go](https://golang.org/) 编写，作为[单个可执行文件](https://github.com/crazy-max/ddns-route53/releases/latest)(和 [Docker 镜像](https://hub.docker.com/r/crazymax/ddns-route53/))交付，让你根据时间表用 [Amazon Route 53](https://aws.amazon.com/route53/) 运行自己的[动态 DNS](https://en.wikipedia.org/wiki/Dynamic_DNS) 服务。

## 证明文件

文件可以在[https://crazy-max.github.io/ddns-route53/](https://crazy-max.github.io/ddns-route53/)找到

## 我能帮什么忙？

欢迎各种投稿<g-emoji class="g-emoji" alias="raised_hands" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f64c.png">🙌</g-emoji>！最基本的表达支持的方式就是出演<g-emoji class="g-emoji" alias="star2" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f31f.png">🌟</g-emoji>项目，还是要提问题<g-emoji class="g-emoji" alias="speech_balloon" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4ac.png">💬</g-emoji>你也可以通过 [**成为 GitHub**](https://github.com/sponsors/crazy-max) <g-emoji class="g-emoji" alias="clap" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f44f.png">上的赞助商来支持这个项目👏</g-emoji>或通过 [Paypal 捐款](https://www.paypal.me/crazyws)来确保旅程无限延续！<g-emoji class="g-emoji" alias="rocket" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f680.png">🚀</g-emoji>

再次感谢您的支持，非常感谢！<g-emoji class="g-emoji" alias="pray" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f64f.png">🙏</g-emoji>

## 许可证

麻省理工学院。更多详情见`LICENSE`。

</article>

[View on GitHub](https://github.com/crazy-max/ddns-route53)

[**ddns-route53**](https://github.com/crazy-max/ddns-route53) 是一个用 [Go](https://golang.org/) 编写的 CLI 应用程序，可以让你根据时间表用 [Amazon Route 53](https://aws.amazon.com/route53/) 运行自己的[动态 DNS](https://en.wikipedia.org/wiki/Dynamic_DNS) 服务。

它可以处理 IPv4 和 IPv6 地址，并有一个内部 cron 实现，通过 go 例程用作服务。

配置是通过一个简单的 YAML 文件完成的，如下所示:

```
credentials:
  access_key_id: "<AWS_ACCESS_KEY_ID>"
  secret_access_key: "<AWS_SECRET_ACCESS_KEY>"

route53:
  hosted_zone_id: "<HOSTED_ZONE_ID>"
  records_set:
    - name: "ddns.example.com."
      type: "A"
      ttl: 300
    - name: "another.example2.com."
      type: "A"
      ttl: 300 
```

和一个官方的[码头工人形象](https://hub.docker.com/r/crazymax/ddns-route53)🐳也是可用的。

希望对你们有些帮助。

* * *

通过 [**成为 GitHub**](https://github.com/sponsors/crazy-max) 的赞助商来支持这个项目👏或者通过 [Paypal 捐款](https://www.paypal.me/crazyws)来确保这一旅程无限延续下去！🚀

再次感谢您的支持，非常感谢！🙏