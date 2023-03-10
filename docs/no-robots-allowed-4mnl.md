# 不允许机器人进入

> 原文：<https://dev.to/turnersoftware/no-robots-allowed-4mnl>

你可能听说过网络爬虫/蜘蛛/机器人等，通常是在搜索引擎索引一个站点以出现在其搜索结果中的情况下。

搜索引擎和网站运营商之间的关系非常微妙。网站运营商希望当人们搜索相关短语时，他们的网站会有流量。搜索引擎希望对网站进行索引，这样就可以让人们找到最相关的内容。

然而，网站运营商不喜欢爬虫如此猛烈地攻击网站以至于网站被关闭，也不喜欢他们不想显示的页面出现在搜索结果中。

网站运营商有一个强大的工具:他们可以阻止爬虫扫描网站。我的意思是，如果他们的网站经常因为被大量抓取而关闭，或者抓取了他们不想被索引的页面，那是他们唯一的选择，对吗？

但是如果不是呢...

[![The Grinder TV Show Quote - Dean saying "But what if it wasn't?"](img/9e2defedc05bec18aadffaff932c3183.png)](https://i.giphy.com/media/3oEduY5pojz5nc1Vza/giphy.gif)

## 欢迎来到擂台，robots.txt

“robots.txt”文件的原始规范形成于 1994 年，旨在促进网站运营商和网络爬虫之间的某种程度的控制。

它可能是这样的:

```
# robots.txt for http://www.example.com/

User-agent: *
Disallow: /cyberworld/map/ # This is an infinite virtual URL space
Disallow: /tmp/ # these will soon disappear
Disallow: /foo.html 
```

Enter fullscreen mode Exit fullscreen mode

这是一个相当简单的格式，定义一个用户代理(或任何用户代理的`*`)来应用下面的规则，并添加禁止特定路径的规则。该文件还支持在`#`符号后添加注释。

该规范在后来几年得到了扩展，如在 [NoRobots RFC](http://www.robotstxt.org/norobots-rfc.txt) 中，包括了`Allow`规则和每个块多个用户代理。

虽然没有官方文档，但各种网络爬虫都支持通配符路径，使用`$`匹配路径的末尾，支持`Crawl-delay`并支持指定站点地图(通过`Sitemap`)。

例如，下面是 [dev.to 的 robots 文件](https://dev.to/robots.txt) :

```
# See http://www.robotstxt.org/robotstxt.html for documentation on how to use the robots.txt file
#
# To ban all spiders from the entire site uncomment the next two lines:
# User-agent: *
# Disallow: /

Sitemap: https://thepracticaldev.s3.amazonaws.com/sitemaps/sitemap.xml.gz 
```

Enter fullscreen mode Exit fullscreen mode

由于缺乏具体的禁止规则，这表明 web 爬网程序可以对他们找到的任何页面进行爬网。

那么接下来的问题:*有一个“robots.txt”文件是不是就能保证所有的网络爬虫都会有行为？*

## 否(抱歉)

[![The Grinder TV Show Quote - Dean Sr. hitting the table](img/52f3bd8c8f4c1a467bc4a839aeeb5088.png)](https://i.giphy.com/media/3o6fDxnx7hNX4PpCtW/giphy.gif)

虽然这是真的，但不能保证网络爬虫真的会遵守 robots 文件，这仍然是他们的最佳利益所在，否则他们可能会被阻止。

各大搜索引擎会遵守规则，因为它们需要得到相关的内容。它是人们在你需要注意的应用程序中编写的随机的其他网络爬虫。

我是那些写网络爬虫的人之一，并且想要适当地尊重我正在爬行的网站。虽然其他人已经编写了可以做到这一点的库，但我想要一个比我发现的可用解决方案更好的解决方案。

## 我的图书馆

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[Turner software](https://github.com/TurnerSoftware)/[robots exclusion tools](https://github.com/TurnerSoftware/RobotsExclusionTools)

### C#中的“robots.txt”解析和查询库

<article class="markdown-body entry-content container-lg" itemprop="text">

# 机器人排除工具

一个用 C#解析和查询库的“robots.txt ”,紧跟在 [NoRobots RFC](http://www.robotstxt.org/norobots-rfc.txt) 和其他关于【robotstxt.org】T2 的细节之后。

[![AppVeyor](img/bc2a17ab2d0b5bfddad80939208ff4bb.png)](https://ci.appveyor.com/project/Turnerj/robotsexclusiontools)[![Codecov](img/38af38e6040c53cdf3d16ae1cbc5db36.png)](https://codecov.io/gh/TurnerSoftware/RobotsExclusionTools)[![NuGet](img/9efbca288a76eecfe8edca3149213eb4.png)](https://www.nuget.org/packages/TurnerSoftware.RobotsExclusionTools)

## 特征

*   通过字符串、URI(异步)或流(异步)加载机器人
*   支持多个用户代理和“*”
*   支架`Allow`和`Disallow`
*   支持`Crawl-delay`条目
*   支持`Sitemap`条目
*   支持通配符路径(*)以及必须以声明结尾($)
*   内置“robots.txt”标记化系统(允许扩展以支持其他自定义字段)
*   内置的“robots.txt”验证器(允许验证标记化的文件)
*   来自`<meta name="robots" />`标签和`X-Robots-Tag`头的数据的专用解析器

## NoRobots RFC 兼容性

本库试图严格遵循 RFC 文档中定义的规则，包括:

*   当没有明确定义时，全局/任何用户代理(RFC 第 3.2.1 节)
*   字段名(如“用户代理”)受字符限制(第 3.3 节)
*   允许/不允许规则按发生顺序执行(第 3.2.2 节)
*   由 URI 加载应用基于对“robots.txt”的访问的默认规则…

</article>

[View on GitHub](https://github.com/TurnerSoftware/RobotsExclusionTools)

由于 NRobots 是 Robots 文件解析的“非官方和不受支持的分支”,我从头开始编写了自己的目标定位。NET 标准 2.0。它支持前面描述的所有规则，同时允许以后扩展灵活性。

我基于 Jack Vanlightly 的“简单记号赋予器”文章编写了一个定制记号赋予器，这是我的库的核心。我在它上面写了一个验证层来检查令牌模式，以确保它们符合 NoRobots RFC。

我可能有一点非发明综合症，但是我认为这个库对于任何需要在. NET 中解析 robots 文件的人来说是一个真正的进步。

在以后的文章中，我将介绍如何在我写的另外两个库中使用这个库。

## 更多信息

*   robotstxt.org:文件格式的最官方信息可以在这里找到
*   维基百科上的“机器人排除标准”:涵盖了更多的非标准指令，如附加通配符、爬行延迟和网站地图。