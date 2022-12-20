# 消除 Magento 中的重复内容

> 原文：<https://dev.to/daniel88ftw/getting-rid-of-duplicate-content-in-magento-46bl>

在我们开始去除重复内容之前，我们应该概述一下它的一般性质。

按照谷歌的说法，重复内容是:

*   相同的标题，
*   title tags,
*   两个或更多网页上的相似内容，
*   复制粘贴的元标签(例如图像的元描述和 alt 标签)。

关于副本，最重要的是要明白，你不能完全摆脱它们，所以在你的 Magento 商店里有一些是可以的。

如果你没有滥用重复内容，你就不必担心谷歌在搜索结果中惩罚你——它只会决定用户将看到两个相似页面中的哪一个，并使另一个不可见。

也就是说，让我们看看副本是如何出现在 Magento 1 和 2 中的，以及如何最小化它们的数量。包含代码示例的可操作提示。

## 为什么会出现重复以及如何打击

如果我们将副本分为用户生成的副本和机器生成的副本，这将非常方便。

当供应商不想或者根本不能为他们销售的每件商品写独特的描述时，用户生成的重复就会出现。

在这种情况下，激励用户留下对你产品的评论是个好主意，让他们为你在你的网站上填满原创内容。如果他们能在评论中附上收到商品的图片，那就太棒了。

更重要的是，新客户会看到评论，这样你会赢得他们的信任。客户信任度高的网站在谷歌搜索中排名更高，自然会有更多的用户被这类店铺所吸引。

现在让我们继续讨论机器生成的重复，看看如何避免它们。

#### 1。胃多门

你在国际上销售和运输商品是很棒的，你决定以地区为目标是很酷的。在这种情况下，请确保为每个地区提供独特的内容，尤其是那些讲同一种语言的地区。

此外，使用语言元数据来帮助谷歌表明相同的内容针对不同类型的用户。

#### 2。Magento 中的长产品描述和短产品描述

这个又好又简单。请确保您商店中每件商品的长描述和短描述是不同的。这一举措将提高 SEO 排名和客户参与度。

#### 3。索引方法

如果你不使用 301 重定向，你的 Magento 商店将在 www.name.com 和 name.com 同时可用。对于谷歌来说，这两个域名将是不同的网站已经不是什么秘密，所以你要让它知道 www.name.com 的[和 name.com 的](http://www.name.com)应该被视为一个网站。

转到谷歌搜索控制台，将两个网站都添加到其中。不要忘记协议这样重要的细节。添加带有 http://或 https://称谓的地址是绝对必要的。

一个建议:使用 name.com 作为首选地址，并使某些 301 重定向指向它。

那么，如何创建上面提到的 301 重定向？

对于阿帕奇来说，还是编辑比较好。htaccess 或 httpd.conf 文件。转到根目录，打开。htaccess，并在其他行的顶部添加一个新行(如果有的话),看起来像这样:

 `RewriteEngine on
RewriteCond %{HTTP_HOST} <sup>[www.yourstore.com](http://www.yourstore.com)</sup> [NC]
RewriteRule <sup>.*</sup>$ [http://yourstore.com/$1](http://yourstore.com/%241) [L,R=301,NC]` 

该功能适用于 Magento 1 和 2。

对于 [Nginx](http://nginx.org/en/docs/beginners_guide.html) 编辑 server.rewrites，使其看起来像这样:

 `if ($http_host ~* "<sup>?!www\</sup>).*$") {
return 301 [https://www.$http_host$request_uri](https://www.%24http_host%24request_uri);
}` 

该功能也适用于 Magento 1 和 2。

对于 Varnish，将此代码公开。重写以防止缓存问题。

我们再回到协议上，好吗？

您希望使用 rel=canonical 来区分页面的优先级，将 HTTP 和 HTTPS 页面组合在一起并消除重复。所以这里有一个在 Magento 中制作 rel=canonical 页面的快速而简单的指南。

对于 Magento 1，进入系统>配置>目录>搜索引擎优化。在“为类别使用规范链接元标记”和“为产品使用规范链接元标记”字段中，选择“是”。

对于 Magento 2 商店，进入商店>配置>目录>搜索引擎优化，并在同一字段选择“是”

#### 4。URL 中的尾随斜线

根据网络服务器的设置，你的 Magento 商店将默认为 name.com/link/或 name.com/link 的网址。

人们很难注意到这种差异，但在谷歌看来，这两个网址是不同的。

这里最有价值的建议是不要对核心 Magento 文件做任何修改。

这里我们应该再次使用 301 重定向。

为了阿帕奇公开赛。htaccess 文件，它可以在根 Magento 目录中找到，并确保它看起来像这样:

 `RewriteCond %{request_method} <sup>GET$</sup>
RewriteCond %{REQUEST_URI} !<sup>/downloader.*$</sup>
RewriteCond %{REQUEST_URI} <sup>.+</sup>/$
RewriteRule <sup>.+</sup>$ %1 [L,R=301]` 

Nginx 的解决方案是改变服务器规则。将这段代码添加到 nginx.conf:

 `if ($request_method != POST) {
set $rew "A";
}
if ($http_x_requested_with != XMLHttpRequest) {
set $rew "${rew}B";
}
if ($rew = "AB") {
rewrite <sup>/(.*)/$</sup> $scheme://$http_host/$1 permanent;
}` 

#### 5。同一产品属于两个或更多类别

很明显，这个问题导致了大量的重复。大多数 Magento 专家推荐的最简单的解决方案是一种产品只使用一个类别。如果出于某种原因，你不能避免对一个项目使用多个类别，看看这些说明。

Magento 1 用户应该确保目录比较和类别不允许出现在谷歌搜索索引中:

 `Disallow: /catalog/product_compare/
Disallow: /catalog/category/view/` 

Magento 2 用户想去内容>设计>全局设计配置，给你的 robots 添加新行. txt:

 `Disallow: /catalog/product_compare/
Disallow: /catalog/category/view/` 

#### 6。Robots.txt

在你的 Magento 商店上使用 robots.txt 实现给定的技巧以消除重复。

这几行将帮助你限制搜索结果(这并不太麻烦，但也会导致重复)，将它们用于 Magento 1。

 `Disallow: /catalogsearch/result/
Disallow: /sendfriend/` 

这些行将从搜索结果中删除过滤器和评论:

 `Disallow: /review/
Disallow: /*?category=*
Disallow: /*&category=*
Disallow: /*?price=*
Disallow: /*&price=*
Disallow: /*?mode=*
Disallow: /*&product_list_mode=*` 

要限制 Magento 2 商店的搜索结果，您应该防止它们被索引，最佳做法是转到内容>设计>全局设计配置。

这些行将帮助您修复搜索结果中的重复内容:

 `Disallow: /catalogsearch/
Disallow: /catalog/product_compare/
Disallow: /catalog/category/view/
Disallow: /catalog/product/view/` 

此外，Magento 的第二个版本会生成指向标签和评论的链接，这些链接会导致——我们知道答案——更多重复内容。

下面是您添加到 robots.txt 来修复它的内容:

 `Disallow: /tag/
Disallow: /review/
Disallow: /*?dir*
Disallow: /*?dir=desc
Disallow: /*?dir=asc
Disallow: /*?limit=all
Disallow: /*?mode*` 

### 长话短说

重复的内容是不可避免的，不要害怕在你的 Magento 商店中有相当数量的重复内容。通过这些简单的步骤，尽你最大的努力将它最小化，并享受高转化率。