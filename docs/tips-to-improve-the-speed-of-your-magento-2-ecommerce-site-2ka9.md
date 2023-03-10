# 提高 Magento 2 电子商务网站速度的技巧

> 原文：<https://dev.to/vasunaman/tips-to-improve-the-speed-of-your-magento-2-ecommerce-site-2ka9>

如果你不是电子商务开发的新手，那么你已经对 Magento 了解很多了。Magento 是世界上最受欢迎的开源应用程序之一，它使商家能够以前所未有的速度打开他们的电子商务商店。

Magento 正在帮助超过 250，000 家商家在线销售他们的产品。

当我们想提高 Magento 网站的速度时，这是不够的。

默认情况下，Magento 附带了一些功能，可以让你提高 Magento 网站的速度，正如你所知，如果你的网站运行得快，你就会有更多的销售...

请找到一些常见的做法，可以帮助您的 Magento 网站变得比以往更快。

1.  **始终升级到最新版本:**

 **如果你是那些碰巧使用旧版本 Magento 的电子商务店主之一，建议你立即升级到最新版本，因为这样做会自动修复当前 Magento 网站中的错误和补丁。只是不要忘记在开始这个过程之前对商店进行适当的备份。

更新 Magento 可以让你运行你的 Magento 网站，充分发挥它的潜力，显然，它可以帮助你提高网站的速度。

1.  **缓存管理:**

 **启用缓存可以帮助你提高 Magento 2 的速度。如果您不确定缓存是关于什么的，让我来帮助您对缓存有一些基本的了解

web 缓存是一种信息技术，用于临时存储 web 文档，如网页、图像和其他类型的 Web 多媒体，以减少服务器延迟。web 缓存系统存储通过它的文档的副本

[![cache Management](img/42ce3e27a3cd66af0b68494995d172b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--27IFLR18--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.vyrazu.com/wp-content/uploads/2019/03/cache-1024x525.png)

1.  启用 GZip: 这是您在运行任何网站时都应该做的事情，因为 GZip 允许您将页面大小减少 60%到 70%。

这是最容易做到的事情之一，如果您在 apache 上运行您的网站，您可以通过将这些代码添加到。htaccess 文件。

 `# Compress HTML, CSS, JavaScript, Text, XML and fonts
AddOutputFilterByType DEFLATE application/javascript
AddOutputFilterByType DEFLATE application/rss+xml
AddOutputFilterByType DEFLATE application/vnd.ms-fontobject
AddOutputFilterByType DEFLATE application/x-font
AddOutputFilterByType DEFLATE application/x-font-opentype
AddOutputFilterByType DEFLATE application/x-font-otf
AddOutputFilterByType DEFLATE application/x-font-truetype
AddOutputFilterByType DEFLATE application/x-font-ttf
AddOutputFilterByType DEFLATE application/x-javascript
AddOutputFilterByType DEFLATE application/xhtml+xml
AddOutputFilterByType DEFLATE application/xml
AddOutputFilterByType DEFLATE font/opentype
AddOutputFilterByType DEFLATE font/otf
AddOutputFilterByType DEFLATE font/ttf
AddOutputFilterByType DEFLATE image/svg+xml
AddOutputFilterByType DEFLATE image/x-icon
AddOutputFilterByType DEFLATE text/css
AddOutputFilterByType DEFLATE text/html
AddOutputFilterByType DEFLATE text/javascript
AddOutputFilterByType DEFLATE text/plain
AddOutputFilterByType DEFLATE text/xml
# Remove browser bugs (only needed for really old browsers)
BrowserMatch <sup>Mozilla/4</sup> gzip-only-text/html
BrowserMatch <sup>Mozilla/4.0[678]</sup> no-gzip
BrowserMatch \bMSIE !no-gzip !gzip-only-text/html
Header append Vary User-Agent` 

1.  **合并或缩小 JavaScript 和 CSS 文件:**

合并或缩小，JavaScript 或 CSS 文件将这些静态文件的大小减少了 50%以上，它删除了所有的注释和空白，从而允许文件以单个字符串提供服务。这有助于浏览器轻松读取这些文件，也将减少每个 HTTP 请求所花费的时间。

Magento 2 带有这些设置，你所要做的就是启用它。

[![Minifiy CSS and JavaScript](img/7da332d0498c76e373b9ffc5f2b80c0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BryvrZRz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.vyrazu.com/wp-content/uploads/2019/03/minify_javascript.png)

结论:有很多方法可以提高 Magento 2 的速度，这也取决于你拥有的技术栈，所以如果你正在开发一个新的和闪亮的 Magento 2 网站，请确保你正在努力提高速度。众所周知，好的网站速度可以给你带来更多的转化率。

要了解更多，你可以遵循这些更多的提示 [Magento 2 速度提升](https://www.vyrazu.com/important-tips-to-speed-up-your-magento-website-and-give-customers-a-better-buying-experience/)

如果你想补充或建议什么，我会很感激。****