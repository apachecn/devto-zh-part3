# 将帖子从 Wordpress 导出到 Gatsby！

> 原文：<https://dev.to/jess/exporting-posts-out-of-wordpress-and-into-gatsby-1cfb>

在过去的五年里，我的朋友埃里克一直致力于一个名为“为台湾服务”的项目。他在(非常有限的)假期里周游不同的大学，并就从文化和身份到历史和政治的话题发表演讲，这是一项非常热爱的工作。教育人们了解台湾对我来说意义重大，所以我喜欢并支持他所做的事情。最近，这个项目失去了动力，所以我主动提出做 Eric 的官方宣传板——一个可以让我提出想法的人。有时你只是需要一个耳朵或有人问你问题来保持正轨。我不确定这个项目本身是否从我的参与中获益良多，但对我个人来说，能更多地参与台湾的所有事情已经很满足了。

无论如何，到 web 开发部分。我想在假期尝试盖茨比和 Netlify CMS，并决定为台湾创建一个模拟外联网站将是一个有趣的项目。

我用了盖茨比网络启动程序，很快一切都运行起来了。我喜欢盖茨比的文档，无论是[快速入门指南](https://www.gatsbyjs.org/docs/quick-start)还是[教程](https://www.gatsbyjs.org/tutorial/)都写得很好，很容易理解。一旦启动，我认为导入所有现有的台湾博客文章是很重要的。不要问我为什么，我想我只是想要“看到”所有帖子的满足感，我这样做的全部动机是尝试一些新的东西并从中获得乐趣。这导致了如何将 wordpress 的帖子导出到 markdown 和 front matter。

就在那时，我发现了这个有用的工具...

## 舒马赫姆 / [雨果出口商](https://github.com/SchumacherFM/wordpress-to-hugo-exporter)

### Hugo 是用 golang 编写的静态站点生成器。Wordpress 是远程访问您的服务器的工具；——)❗️contributions 欢迎你！

<article class="markdown-body entry-content" itemprop="text">

# WordPress to Hugo Exporter

雨果一个用 GoLang 写的静态站点生成器: [https://gohugo.io](https://gohugo.io)

这个回购是基于[https://github.com/benbalter/wordpress-to-jekyll-exporter](https://github.com/benbalter/wordpress-to-jekyll-exporter)

## 雨果特写

一键式 WordPress 插件，可以将所有的文章、页面、分类、元数据和设置转换成 Markdown 和 YAML，然后放入 Hugo。

## 特征

*   转换 WordPress 中的所有帖子、页面和设置，以便在 Hugo 中使用
*   导出用户看到的内容，而不是数据库存储的内容(在导出之前通过`the_content`过滤器运行帖子内容，允许第三方插件修改输出)
*   将所有`post_content`转换为 Markdown Extra(使用 Markdownify)
*   将`wp_posts`表中的所有`post_meta`和字段转换为 YAML 前端内容，以供 Hugo 解析。
*   可选地将`comments`作为帖子的一部分导出。这个特性需要通过编辑 PHP 源代码来手动启用。参见 hugo-export.php 文件第 40 行。
*   导出私人帖子和草稿。它们也被标记为草稿，不会被发布…

</article>

[View on GitHub](https://github.com/SchumacherFM/wordpress-to-hugo-exporter)

根据自述文件，我需要在我的机器上安装一个 wordpress 的本地实例来安装插件。因为我以前从未安装过 wordpress，所以我决定走 docker 路线。

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png)[【wodby】](https://github.com/wodby)/[坞站 4wordpress](https://github.com/wodby/docker4wordpress)

### 基于 Docker 的 WordPress 堆栈

<article class="markdown-body entry-content" itemprop="text">

# 基于 Docker 的 WordPress 堆栈

[![Build Status](img/100cc7dee5c836fb42e14c53870ecc51.png)](https://travis-ci.org/wodby/docker4wordpress)

## 介绍

Docker4WordPress 是一组针对 WordPress 优化的 Docker 图片。使用这个库中的 docker-compose.yml 文件在 Linux、macOS 和 Windows 上为 WordPress 构建一个本地环境。

*   阅读[上的文档**如何使用**T3】](https://wodby.com/docs/stacks/wordpress/local#usage)
*   询问关于[时差](http://slack.wodby.com/)的问题
*   关注 [@wodbycloud](https://twitter.com/wodbycloud) 了解未来公告

## 堆

WordPress 堆栈由以下容器组成:

| 容器 | 版本 | 服务名称 | 图像 | 默认 |
| --- | --- | --- | --- | --- |
| [Nginx](https://wodby.com/docs/stacks/wordpress/containers#nginx) | 1.17, 1.16 | `nginx` | 【T0 水源地/nginnx】t1 | -好的 |
| [阿帕奇](https://wodby.com/docs/stacks/wordpress/containers#apache) | Two point four | `apache` | 【T0 水源地/Apache】t1 |
| [WordPress](https://wodby.com/docs/stacks/wordpress/containers#php) | five | `php` | [wodby/wordpress](https://github.com/wodby/wordpress) | -好的 |
| [PHP](https://wodby.com/docs/stacks/wordpress/containers#php) | 7.4, 7.3, 7.2 | `php` | [wodby/wordpress-php](https://github.com/wodby/wordpress-php) |
| [马里亚布](https://wodby.com/docs/stacks/wordpress/containers#mariadb) | 10.4, 10.3, 10.2, 10.1 | `mariadb` | [wodby/mariadb](https://github.com/wodby/mariadb) | -好的 |
| [PostgreSQL](https://wodby.com/docs/stacks/wordpress/containers#postgresql) | 12，11，10，9.x | `postgres` | [wodby/postgres](https://github.com/wodby/postgres) |
| [再说一遍](https://wodby.com/docs/stacks/wordpress/containers#redis) | 5, 4 | `redis` | wodby/redis |
| [Memcached](https://wodby.com/docs/stacks/wordpress/containers#memcached) | one | `memcached` | [wodby/memcached](https://github.com/wodby/memcached) |
| [清漆](https://wodby.com/docs/stacks/wordpress/containers#varnish) | 6.0, 4.1 | `varnish` | [清漆/清漆](https://github.com/wodby/varnish) |
| [Node.js](https://wodby.com/docs/stacks/wordpress/containers#nodejs) | 12, 10, 8 | `node` | [水球/节点](https://github.com/wodby/node) |
| [Solr](https://wodby.com/docs/stacks/solr) | 8, 7, 6, 5 | `solr` | [wodby/solr](https://github.com/wodby/solr) |
| [弹性搜索](https://wodby.com/docs/stacks/elasticsearch) | 7, 6 | `elasticsearch` | [wodby/elasticsearch](https://github.com/wodby/elasticsearch) |
| [Kibana](https://wodby.com/docs/stacks/elasticsearch) | 7, 6 | `kibana` | 【T0 水陆两用】T1 |
| [AthenaPDF](https://wodby.com/docs/stacks/wordpress/containers#athenapdf) | 2.10.0 | `athenapdf` | [arachnysdocker/athenapdf-service](https://hub.docker.com/r/arachnysdocker/athenapdf-service) |
| [邮件猪](https://wodby.com/docs/stacks/wordpress/containers#mailhog) | 最近的 | `mailhog` | [邮件猪/邮件猪](https://hub.docker.com/r/mailhog/mailhog) | -好的 |
| [OpenSMTPD](https://wodby.com/docs/stacks/wordpress/containers#opensmtpd) | Six | `opensmtpd` | [wodby/opensmtpd](https://github.com/wodby/opensmtpd) |

…</article>

[View on GitHub](https://github.com/wodby/docker4wordpress)

我花了一点时间来设置实例，因为我以前从未接触过 docker，但一旦它启动，将帖子导出到 markdown 就非常简单了。最棘手的部分是意识到我需要压缩回购中的文件，并将其作为插件上传到 wordpress 上。我可能应该在自述文件中提交一份 PR 来说明这一点——事后看来，这似乎很明显，但搞清楚这一点花费的时间比我想承认的要长得多。

假期占据了我剩余的休息时间，所以这就是我所完成的。不过没关系！我的目标是体验一下我日常不用的技术，而这正是我的目标。也许在我下一次休息的时候，我会试着更进一步，或者试着用一套完全不同的工具为台湾建立另一个模拟外联网站。