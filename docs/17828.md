# 群体克隆作业

> 原文：<https://dev.to/crazymax/swarm-cronjob-3lp3>

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [疯狂麦克斯](https://github.com/crazy-max) / [蜂拥而至](https://github.com/crazy-max/swarm-cronjob)

### 📅在 Docker Swarm 上创建基于时间的工作

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/e29083e99db84ef572f7df852291563a.png)](https://github.com/crazy-max/swarm-cronjob)

[![GitHub release](img/ce9776b12a63a5a62f3c451b44045016.png)](https://github.com/crazy-max/swarm-cronjob/releases/latest)[![Total downloads](img/ac480d3330daeffeea6c24206b9a1509.png)](https://github.com/crazy-max/swarm-cronjob/releases/latest)[![Build Status](img/c190781c778941075102a6daac54192e.png)](https://github.com/crazy-max/swarm-cronjob/actions?workflow=build)[![Docker Stars](img/54c18adead8225537ca4b8624dfee7fe.png)](https://hub.docker.com/r/crazymax/swarm-cronjob/)[![Docker Pulls](img/92bb77823393e2ecd6ead28dd13edd86.png)](https://hub.docker.com/r/crazymax/swarm-cronjob/)
[![Go Report](img/563aa4abb3975f3752c6dea344c58264.png)](https://goreportcard.com/report/github.com/crazy-max/swarm-cronjob)[![Code Quality](img/c4a106868be9cacba1918e38a7b4eb92.png)](https://www.codacy.com/app/crazy-max/swarm-cronjob)[![Become a sponsor](img/8622aa159ba302f981f4f2f291369dd3.png)](https://github.com/sponsors/crazy-max)[![Donate Paypal](img/f3563d97c76d0f36756536747d40f632.png)](https://www.paypal.me/crazyws)

## 关于

**swarm-cronjob** 在 [Swarm](https://docs.docker.com/engine/swarm/) 上根据基于时间的时间表创建作业，以分布式方式提供专用服务，通过[标签](https://docs.docker.com/engine/reference/commandline/service_create/#set-metadata-on-a-service--l---label)和 Docker API 自动动态配置自身。

<g-emoji class="g-emoji" alias="bulb" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4a1.png">💡</g-emoji>想要获得新版本的通知吗？看看<g-emoji class="g-emoji" alias="bell" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f514.png">🔔</g-emoji> [Diun (Docker 镜像更新通知器)](https://github.com/crazy-max/diun)项目！

## 特征

*   持续更新其配置(无需重启)
*   通过 go 例程实现 Cron
*   如果服务当前正在运行，则允许跳过作业
*   可以为调度程序更改时区

## 证明文件

*   [开始使用](https://raw.githubusercontent.com/crazy-max/swarm-cronjob/master/doc/get-started.md)
*   安装
    *   [与码头工人](https://raw.githubusercontent.com/crazy-max/swarm-cronjob/master/doc/install/docker.md)
    *   [来自二进制](https://raw.githubusercontent.com/crazy-max/swarm-cronjob/master/doc/install/binary.md)
    *   [Linux 服务](https://raw.githubusercontent.com/crazy-max/swarm-cronjob/master/doc/install/linux-service.md)
*   [常见问题解答](https://raw.githubusercontent.com/crazy-max/swarm-cronjob/master/doc/faq.md)
*   [日志](https://raw.githubusercontent.com/crazy-max/swarm-cronjob/master/doc/logs.md)

## 我能帮什么忙？

欢迎各种投稿<g-emoji class="g-emoji" alias="raised_hands" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f64c.png">🙌</g-emoji>！最基本的表达支持的方式就是出演<g-emoji class="g-emoji" alias="star2" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f31f.png">🌟</g-emoji>项目，还是要提问题<g-emoji class="g-emoji" alias="speech_balloon" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4ac.png">💬</g-emoji>你也可以通过 [**成为 GitHub**](https://github.com/sponsors/crazy-max) <g-emoji class="g-emoji" alias="clap" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f44f.png">上的赞助商来支持这个项目👏</g-emoji>或通过 [Paypal 捐款](https://www.paypal.me/crazyws)来确保旅程无限延续！<g-emoji class="g-emoji" alias="rocket" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f680.png">🚀</g-emoji>

再次感谢您的支持…

</article>

[View on GitHub](https://github.com/crazy-max/swarm-cronjob)

📅swarm-cronjob 在 swarm 上根据时间表创建作业🐳通过标签和 Docker API 自动和动态地配置自身。以下是主要功能:

*   持续更新其配置(无需重启)
*   通过 go 例程实现 Cron
*   如果服务当前正在运行，则允许跳过作业
*   可以为调度程序更改时区

希望对你们有些帮助。欢迎您的评论！

* * *

通过 [**成为 GitHub**](https://github.com/sponsors/crazy-max) 的赞助商来支持这个项目👏或者通过 [Paypal 捐款](https://www.paypal.me/crazyws)来确保这一旅程无限延续下去！🚀

再次感谢您的支持，非常感谢！🙏