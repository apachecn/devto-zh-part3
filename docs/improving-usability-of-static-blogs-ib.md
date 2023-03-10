# 提高静态博客的可用性

> 原文：<https://dev.to/animesh/improving-usability-of-static-blogs-ib>

要提高静态博客的可用性，有三件主要的事情需要关注。虽然我写这篇文章时考虑到了 Lektor，但大多数技术对任何静态博客生成器都是通用的。

1.  表演
2.  搜索引擎优化
3.  易接近

## 表现

静态博客默认很快。我们可以通过实现一些优化技术来进一步改善页面加载时间。我使用 [Varvy](https://varvy.com) 来识别潜在的改进。这里列出了其他一些你可能会觉得有用的流行网站。

1.  [谷歌页面速度洞察](https://developers.google.com/speed/pagespeed/insights/)
2.  [GTMetrix](https://gtmetrix.com/)
3.  [网页测试](https://www.webpagetest.org/)
4.  [web.dev](https://web.dev)

我在博客中应用的常见建议包括

1.  缩小静态资源
2.  启用 gzip 压缩
3.  使用 HTTPS

### 缩小

缩小是压缩静态文件以减小文件下载大小的过程。例如，下面的 JavaScript 代码片段是 540 字节。

```
var myModule = (function() {
    'use strict';

    var _privateProperty = 'Hello World';

    function _privateMethod() {
        console.log(_privateProperty);
    }

    return {
        publicMethod: function() {
            _privateMethod();
        }
    };
}());

myModule.publicMethod();                    // outputs 'Hello World'   
console.log(myModule._privateProperty);     // is undefined protected by the module closure
myModule._privateMethod();                  // is TypeError protected by the module closure 
```

压缩时，它被收缩到 215 字节。

```
var myModule=function(){"use strict";function o(){console.log(e)}var e="Hello World";return{publicMethod:function(){o()}}}();myModule.publicMethod(),console.log(myModule._privateProperty),myModule._privateMethod(); 
```

这在下载大小或带宽成本上减少了 60%,不管你怎么称呼它。

我使用一个非官方的插件 [lektor-minify](https://github.com/pietroalbini/lektor-minify) 来做这件事。要使用这个插件，请用 lektor plugin 命令安装它。

T2`lektor plugins add lektor-minify`

要查看缩小的效果，请运行以下命令。

T2`lektor build -f minify:html,css,js`

打开输出文件夹并检查 HTML 文件。所有静态文件都被压缩。对于 css 和 js 文件，创建相关的`.min.css`和`.min.js`文件，对于 html 文件，html 文件的内容被缩小到同一个文件中。不会生成单独的`.min.html`文件。

我在`gitlab-ci.yml`中加入了上面的一行。它在构建的内容部署到服务器之前执行。

```
image: python:3.6
pages:
 script:
 - pip install lektor
 - lektor build --output-path public -f minify:html,css,js
 artifacts:
   paths:
   - public
 only:
 - master 
```

### GZIP 压缩

除了缩小文件之外，我们还可以多做一层压缩。gzip 压缩文件会将其压缩成浏览器可以在接收时解包的文件格式。

如果我们使用 web 服务器来服务我们的博客，那么我们可以告诉 web 服务器压缩我们的页面并返回给客户端。因为我使用 gitlab 页面进行部署，所以所有内容的 gzip 压缩都是在博客部署时完成的。

为此，我将下面的 shell 命令添加到我们的`gitlab-ci.yml`中。

```
gzip -k -6 -r public 
```

这是完整的例子。

```
image: python:3.6

pages:
  script:
  - pip install lektor
  - lektor build --output-path public -f minify:html,css,js
  - gzip -k -6 -r public
  artifacts:
    paths:
    - public
  only:
  - master 
```

这将在构建步骤完成后运行`gzip`命令。现在，当客户机请求 URL 为`/blogpost-url-123/`的页面时，服务器返回`/blogpost-url-123/index.html.gz`。浏览器理解这个文件，并在呈现它之前进行额外的解压缩步骤。

我们可以使用网站[checkgzipcompression.com](https://checkgzipcompression.com)检查压缩量。

[![Image of gzip compression checker website](img/99ea7cfbe01a7580b304d97fe721f621.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JWsjS-FA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Liwirph.png)

很明显，我的一篇大型博客文章节省了 70%。

虽然这些是我为提高性能而实施的主要措施，但我也根据网站优化工具提供的见解实施了一些其他措施。我经常使用的方法之一是使用压缩图像。

## SEO

SEO，代表搜索引擎优化，是一种提高你的网站或博客文章在各种搜索引擎如 Google，Bing 和 DuckDuckGo 上的可见性的优化。针对特定主题撰写的文章，如果针对搜索引擎进行了优化，将会吸引更多的人。

SEO 是一个庞大且不断变化的主题，无法在本文中全面涵盖。然而，让我们看看一些流行的技术，包括

*   添加元信息
*   结构化内容
*   使用 HTTPS

默认情况下，Lektor 不提供任何 SEO 选项。让我们尝试一下这三件事。

### 添加元信息

有多种方式来提供关于文章或网页的元数据或元信息。

#### 元标签

meta 标签有很多，但以下是最重要的，IMHO。

*   类似标题的标题标签，但用于爬虫
*   作者-指明内容的作者
*   版权-你网页上的版权内容
*   描述-总结网页的内容
*   类似关键字的标签，但用于爬虫

对于 Lektor，我在关联的模型上创建一个属性，然后在模板上使用它。例如，为了在文章上有一个描述的 meta 标签，首先我在`blog-post.ini`模型上添加了一个字段:

```
[fields.metadesc]
label="Meta Tag: Description"
type = string 
```

该`metadesc`字段可在模板中使用。所以我在相关模板的头部添加了以下标记。

```
<meta name="description" content="{{ this.metadesc }}" /> 
```

由于这是一个字段，它出现在管理。

[![Picture of lektor admin showing the meta description field](img/7f97d452e502266fdf49695137bd548e.png)](https://i.imgur.com/TkPuKa8.png) 
*电梯管理中的元描述字段*

这里是一个完整的例子，上面所有的元标签。

```
<meta name="title" content="Improving usability of static blogs | Animesh Bulusu" />
<meta name="author" content="Animesh Bulusu" />
<meta name="copyright" content="Animesh Bulusu" />
<meta name="description" content="Optimizing my static blog for SEO and performance" />
<meta name="keywords" content="seo, optimization, optimize, static blog, lektor, performance optimization, meta tags, metadata, 2019, accessibility, performance, animesh bulusu, animesh.blog, animesh bulusu blog" /> 
```

> 注意:Meta 标签关键字[不再被认为](https://yoast.com/meta-keywords/) [有用](https://datadrivenlabs.io/blog/meta-keyword-tag-google-2018/)。我在 2016 年和 2017 年之间添加这些功能时并没有意识到这一点。我把它留下了，以防有人想知道它。

#### 规范的网址

需要规范的 URL 来指示特定网页是原始来源。这有两个好处。

1.  当我们交叉发布我们的内容到其他内容聚合器，如开发。对于和 Medium，它会在文章末尾添加一个链接，大意是“这篇博文最初来自:[https://animesh.blog/improving-usability-of-static-blogs](https://animesh.blog/improving-usability-of-static-blogs)”
2.  当我们有一篇文章的多个副本时，我们可以将一篇文章指定为规范的。通过这种方式，搜索引擎知道具有规范 URL 的文章是原创的，并且不会惩罚该文章。

在标记中，我添加了以下标记。

```
<link rel="canonical" href="{{'.'|url(external=true)}}" /> 
```

这将生成当前页面的绝对 URL，并呈现如下。

```
<link rel="canonical" href="https://animesh.blog/improving-usability-of-static-blogs/" /> 
```

#### [T1】robots . txt](#robotstxt)

将该文件添加到您的目录的根目录，以指示您是否希望您的内容被索引。这个在我的 robots.txt 文件里。第一行告诉爬虫我的博客可以被索引。第二行告诉爬虫，我不允许爬行静态文件夹。

```
User-agent: *
Disallow: /static/* 
```

#### 网站地图

在 robots.txt 文件中，添加对 sitemap.xml 文件的引用，如下所示。

```
Sitemap: https://animesh.blog/sitemap.xml 
```

这个 sitemap.xml 文件可以在构建站点时生成。我使用 Lektor 文档中的这个站点地图。

```
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  {%- for page in [site.root] if page != this recursive %}
  <url><loc>{{ page|url(external=true) }}</loc></url>
  {{- loop(page.children) }}
  {%- endfor %}
</urlset> 
```

它递归地生成我博客上所有网页的链接，这对爬虫很有用。

```
<urlset>
  <url>
    <loc>https://animesh.blog/</loc>
  </url>
  <url>
    <loc>https://animesh.blog/improving-usability-of-static-blogs/</loc>
  </url>
</urlset> 
```

在你的博客主页上添加一个生成的站点地图的链接也是有帮助的。查看我的[网站地图](https://animesh.blog/sitemap.html)页面。

### 构建内容

#### 标题

使用 h1、h2 和 h3 这样的标题标签，但是记住每页只有一个 H1 标签。h1 标签应该包含与你的文章最相关的词。

#### 语义标签

使用如下语义标签来组织你的内容。

*   页眉
*   主要的
*   航行
*   页脚
*   文章

#### 友好的网址

这种技术的要点是拥有一个与文章相关的 URL。

假设你正在写一篇关于如何学习 react.js 的文章

像这样的网址

[https://mydomain.tld/how-to-learn-react/](https://mydomain.tld/how-to-learn-react/)

是比更好的 URL

[https://mydomain.tld/article?id=203](https://mydomain.tld/article?id=203)

### 利用 HTTPS

这是一个感觉像是性能而不是搜索引擎优化的领域。现实中是性能方面。然而，由于谷歌的努力，HTTPS 的使用现在已经变得很普遍。原来，搜索引擎已经[开始](https://webmasters.googleblog.com/2014/08/https-as-ranking-signal.html)排名网站而没有 HTTPS 低。浏览器厂商最近开始强调不使用 HTTPS 的网站“不安全”。由于这一点，在网站上拥有 HTTPS 已经成为一个搜索引擎优化的事情。

HTTPS 主要保证两件事，身份和数据完整性。

SSL 证书告诉浏览器它收到的信息确实来自发起方。HTTPS 连接可以防止黑客读取从服务器传输给用户的数据。HTTPS 连接还能防止劫机者篡改传输中的数据。这种攻击被称为 [MITM](https://en.wikipedia.org/wiki/Man-in-the-middle_attack#Example) 。HTTPS 加大了 MITM 袭击的难度。

有一类恶意活动是 ISP [向您的连接中注入](https://www.infoworld.com/article/2925839/net-neutrality/code-injection-new-low-isps.html)代码或广告。如果您与网站的连接未加密，这是可能的。HTTPS 阻止了这一点。

多亏了 Let's Encrypt，设置 SSL 证书至少两年来变得更容易了。

## 可达性

可访问性是一种开发实践，使残疾人浏览网页更容易。再次像 SEO 一样，可访问性是一个主题的海洋。然而，非常重要的是，许多组织将使他们的 web 产品具有可访问性作为一项法律要求。所以，依我看，这是值得考虑的。

我个人的观点是，这是一种道德义务，在建立一个新博客时应该考虑到这一点。很容易陷入认为你的博客不重要的陷阱。此外，添加所有额外的东西只是需要时间，不是吗？这确实需要时间，但这是你的博客。现在不需要急于实施所有正确的事情。当你找到时间的时候，继续添加东西。童子军规则在这里很适用。

以下是我已经设法实现的一些事情，尽管并不令我完全满意。所有这些都很容易实现。

*   skiplinks -这些是实际内容开始的指示器
*   图像和视频的替代文本——屏幕阅读器将使用这些信息来宣布媒体是关于什么的
*   页面标题——这在很多情况下都很有用
*   逻辑结构——网站的可导航性，当结构合理时，为使用屏幕阅读器的用户提供更好的体验。
*   页面语言
*   有意义的 tab 键顺序——为只使用键盘或使用屏幕阅读器的用户保持线性或逻辑导航
*   焦点指示-当通过键盘访问内容时，指示焦点在哪里
*   色彩对比——如果考虑到这一点，视力不好的用户会有更好的体验

我不会为这些个别项目推荐特定的学习资源，而是鼓励先阅读 [WCAG 清单](https://www.wuhcag.com/wcag-checklist/)和合规水平，然后再学习特定项目。

作为工作项目的一部分，我最近才意识到这个清单。只有到那时，我才能够形成一种内在的表达，这些看似微不足道的事情是如何给你的读者带来奇迹的。这帮助我在一年多后将这篇文章从草稿状态中拉出。

**结尾注释**

为了进一步提高静态博客的可用性，可以投入时间

1.  实施响应式网页设计
2.  转换为 PWA

对于大多数静态博客来说，PWA 可能有些矫枉过正。然而，如果你现在就在建博客，响应式网页设计是必不可少的。我已经用媒体查询和一些 flexbox 实现了响应式设计。

在 [gitlab](https://gitlab.com/aniemsh/aniemsh.gitlab.io) 上随意检查、查看源代码或查看完整源代码。