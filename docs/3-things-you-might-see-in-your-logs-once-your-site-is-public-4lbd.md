# 一旦你的网站公开，你可能会在日志中看到 3 件事

> 原文：<https://dev.to/intricatecloud/3-things-you-might-see-in-your-logs-once-your-site-is-public-4lbd>

您已完成将网站部署到新域。您开始看到您的正常用户流量，但随后您也注意到您的访问日志中的有趣模式。这里有几个例子，当你的站点或 API 在生产中公开后，你可能会在你的访问日志中看到。

1.  来自世界各地的自动化漏洞扫描器
2.  童裤
3.  SQL 注入尝试

## 扫描仪

你知道通常会出现什么吗？扫描仪。[很多很多的开源扫描器](https://geekflare.com/open-source-web-security-scanner/)。IP 来自世界各地中国，乌克兰，俄罗斯。一周内，我们会随机看到扫描仪流量。扫描仪流量是什么样的？

有几个迹象可以告诉你:

*   在大约 30 分钟的持续时间内，您的错误率略有上升，然后突然消失。4xx 和 5xx 的组合。![elevated error rates](img/f0eecfb79548774b7cd76ca5a157c3d7.png "Elevated error rates")
*   它正在请求您的应用程序中不存在的路径。
*   一些扫描程序使用特定的用户代理标头，将自己标识为扫描程序

您可能会看到这样的请求...

```
x.x.x.x - - [07/Apr/2018:02:50:27 +0000] "GET /wp-json/oembed/1.0/embed?url=..%2F..%2F..%2F..%2F..%2F..%2F..%2FWindows%2FSystem32%2Fdrivers%2Fetc%2Fhosts%00&format=xml HTTP/1.0" 404 132 "http://www.zzzz.yyy/xxxx" "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_8; en-us) AppleWebKit/534.57.2 (KHTML, like Gecko) Version/5.1.6 Safari/534.57.2" 
```

Enter fullscreen mode Exit fullscreen mode

在中有一个编码的 URL: `../../../../../../../Windows/System32/drivers/etc/hosts&`。这种类型的请求看起来主要像是一种侦察尝试，看看它是否能够访问该目录中的文件。

如果你正在运行一个 wordpress 站点，随时准备好接收大量已知的对你站点的攻击。我们甚至没有运行一个 wordpress 网站，我们的访问日志中会有这种类型的东西。包括有一个 [revslider_show_image 漏洞，窃取 2014](https://blog.sucuri.net/2014/09/slider-revolution-plugin-critical-vulnerability-being-exploited.html) 的 wp-config 文件。

```
| count | uri                                                                           |
|-------|-------------------------------------------------------------------------------|
| 12    | /wp-admin/admin-ajax.php                                                      |
| 6     | /wp-admin/admin-ajax.php?action=revslider_show_image&img=../wp-config.php     |
| 6     | /wp-admin/tools.php?page=backup_manager&download_backup_file=../wp-config.php |
| 3     | /wp-admin/wp-login.php?action=register                                        |
| 2     | /help/wp/wp-admin/setup-config.php                                            |
| 1     | /help/new/wp-admin/setup-config.php                                           |
| 1     | /help/wp-admin/setup-config.php                                               |
| 1     | /wp-admin/js/password-strength-meter.min.js?ver=4.9.1                         |
| 1     | /wp-admin/js/password-strength-meter.min.js?ver=4.9.2                         | 
```

Enter fullscreen mode Exit fullscreen mode

根据您的应用程序的托管方式，您甚至可能在扫描期间看到延迟增加:
[![increased latency graph](img/c2bd5d96d7d2f2c8eec2136e26fe6e75.png "increased latency graph")](https://res.cloudinary.com/practicaldev/image/fetch/s--kczFbSRa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/intricatecloud-content/wp-content/uploads/2018/11/04065335/Screen-Shot-2018-11-04-at-1.53.20-AM.png)

## 爬虫

这些无伤大雅。只是谷歌和必应索引内容。典型的互联网就是互联网。这个特别的爬虫非常好，包括一个用户代理头，它把你发送到一个站点，这个站点确切地告诉你他们对收集的所有数据做了什么。看看吧-[http://www.exensa.com/crawl](http://www.exensa.com/crawl)

```
x.x.x.x [30/Mar/2018:04:39:15 +0000] "GET /robots.txt HTTP/1.1" 404 136 "-" "Barkrowler/0.7 (+http://www.exensa.com/crawl)" 
```

Enter fullscreen mode Exit fullscreen mode

### SQL 注入袭击

您可能会看到这些侦察攻击——您可以获取一个已知的公共 URL，并添加一个 URL 编码的 SELECT 语句，以查看响应中是否有任何有趣的内容。像这样:

```
x.x.x.x [11/Apr/2018:16:09:37 +0000] "GET /REDACTED&response_mode=%28SELECT%20%28CHR%28113%29%7C%7CCHR%28107%29%7C%7CCHR%28120%29%7C%7CCHR%28107%29%7C%7CCHR%28113%29%29%7C%7C%28SELECT%20%28CASE%20WHEN%20%285423%3D5423%29%20THEN%201%20ELSE%200%20END%29%29%3A%3Atext%7C%7C%28CHR%28113%29%7C%7CCHR%28122%29%7C%7CCHR%28112%29%7C%7CCHR%2898%29%7C%7CCHR%28113%29%29%29&response_type=code&scope=openid HTTP/1.1" 302 0 "-" "Mozilla/5.0 (Windows; U; Windows NT 6.0; en-US) AppleWebKit/532.0 (KHTML, like Gecko) Chrome/4.0.202.0 Safari/532.0" 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个 URL 编码的 SQL 注入字符串。如果你解码，你会得到这个

```
(SELECT (CHR(113)||CHR(107)||CHR(120)||CHR(107)||CHR(113))||(SELECT (CASE WHEN (5423=5423) THEN 1 ELSE 0 END))::text||(CHR(113)||CHR(122)||CHR(112)||CHR(98)||CHR(113))) 
```

Enter fullscreen mode Exit fullscreen mode

这些是注射测试。如果回应包含一些不寻常的东西，他们知道他们能做到。虽然不完全确定这里发生了什么。

## 抵御它

这里有一些你可以用来远离这些人的东西。

*   使用安全组(在 AWS 中)/ iptables 将入站流量限制到您需要的端口。如果只有和 80/443 是开放的，那么您最容易受到 web 应用程序攻击，而不是 SSH/FTP/DNS 之类的。这是一份关于安全最佳实践的 AWS 白皮书

*   定期检查你的日志，看看发生了什么。如果您看到 SQL 注入尝试，看看哪些调用返回了 200/500 -这意味着一些潜在的有用的东西已经给攻击者。

*   彻底拒绝任何与你的应用程序 URL 一点也不接近的东西——例如，当你的应用程序托管在 Linux 上时，你会想要阻止 Windows 路径，比如在你的访问日志中包含`System32`、`cmd.exe`、`C:\\`的路径。

*   在本地对您的应用程序进行扫描，领先黑客一步。先知道自己接触的是什么。看看这些[开源扫描仪](https://geekflare.com/open-source-web-security-scanner/)

*   如果你把你的静态网站放在 AWS S3 网站上，这些问题都会消失——如果你是第一次这么做，这是我写的一个指南

*   如果你在寻找 Wordpress 的替代品？参见本指南[用 gatsby.js 建立博客](https://www.intricatecloud.io/2018/06/part-1-build-test-deploy-and-monitor-a-static-website-building-a-blog-with-gatsby/)

如果你觉得这很有帮助，你可以把它❤️化，然后在 dev.to 上关注我😄