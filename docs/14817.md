# 在你自己的 Jekyll 网站上处理外部帖子

> 原文：<https://dev.to/huijing/handling-external-posts-on-your-own-jekyll-site-1ekm>

我在自己的博客上写了很多东西，偶尔也会给一些外部出版物投稿。但我确实喜欢把我所有的写作清单放在一个地方。我的网站是用 Jekyll 建立的，已经有一段时间了。

使用静态站点生成器确实需要一定程度的努力来正确设置相关模板，以确保您的站点以您期望的方式结束。Jekyll 使用[液体模板语言](https://shopify.github.io/liquid/)，你的内容可以用 Markdown 编写，也可以用 HTML 和 CSS 美化。

大多数出版物都希望投稿人在再版前给予文章一定时间的出版专有权，因为流量对这些出版物的赚钱能力至关重要。尊重这一要求是很重要的，尤其是如果出版社为你的文章付钱的话。

由于我缺乏责任心，我发现当我试图在我自己的网站上的文章列表中包含外部帖子时，我违反了这一要求😔。所以我想分享我采取的纠正错误的步骤，也许对想做类似事情的人会有帮助。

## TL；速度三角形定位法(dead reckoning)

*   在外部帖子的前面设置额外的自定义变量，这些变量将在后续要点中使用:

```
external_url: https://PATH_TO_THE_CANONICAL_VERSION
external_site: PUB_NAME 
```

Enter fullscreen mode Exit fullscreen mode

*   确保`<head>`中的规范链接指向原始源:

```
{% if page.external_url %}
<link rel="canonical" href="{{ page.external_url }}">
{% else %}
<link rel="canonical" href="{{ page.url | replace:'index.html','' | prepend: site.baseurl | prepend: site.url }}">
<link rel="alternate" type="application/atom+xml" title="{{ site.title }}" href="{{ "/feed.xml" | prepend: site.baseurl | prepend: site.url }}">
{% endif %} 
```

Enter fullscreen mode Exit fullscreen mode

*   用原始源 URL 替换内部帖子 URL 的所有链接。对于列表，您可以添加条件:

```
{% if post.external_url %}
<a href="{{ post.external_url }}">{{ post.title }}</a>
{% else %}
<a href="{{ post.url }}">{{ post.title }}</a>
{% endif %} 
```

Enter fullscreen mode Exit fullscreen mode

*   包括视觉提示，以指示到文章链接被重定向到原始来源
*   如果适用，从 RSS 源中删除外部帖子。以下示例要求要排除的帖子在前面有一个额外的自定义变量:

```
nofeed: true 
```

Enter fullscreen mode Exit fullscreen mode

也可以使用外部帖子特有的其他一些现有的自定义变量作为条件变量。将条件添加到`feed.xml`中，如下所示:

```
{% for post in site.posts limit:15 %}
  {% unless post.nofeed %}
  <item>
    {{ post.title | xml_escape }}
    <description>{{ post.content | xml_escape | truncatewords:120}}</description>
    <pubDate>{{ post.date | date_to_rfc822 }}</pubDate>
    <link>{{ site.url }}{{ post.url }}</link>
    <guid isPermaLink="true">{{ site.url }}{{ post.url }}</guid>
  </item>
  {% endunless %}
{% endfor %} 
```

Enter fullscreen mode Exit fullscreen mode

*   为了使事情更清楚，如果你在列表中包含文章内容作为删节摘要，请在文章最初发表的地方添加一个注释。
*   如果你根本不希望内部帖子的 URL 出现在搜索引擎上，可以考虑在外部帖子的`<head>`后添加一个`noindex`，但这有点矫枉过正，因为只要设置了规范的 URL，就不会影响原始内容的 SEO。我想。
*   使用一个随机的字符串作为源文件名，以减少人们手动输入内部 URL 的几率，这也是 overkill 套件的一部分。

## 哲基尔帖子

使用 Jeykll，每篇文章都是一个文本文件，然后被处理成一个适当的博客。或许有可能利用数据文件以某种方式将外部链接与内部帖子穿插在一起，但这对于我的小脑袋来说太难了，所以即使是外部帖子，我也会为每个帖子创建一个文件。

因为我已经有了这个文件，我只需要把我的草稿写到这个 markdown 文件中，就像我在我的网站上写其他帖子一样。当我的帖子在外部出版物上发布时，我也会在我的网站上“发布”链接。因为我们可以在文章链接需要出现的任何地方用外部链接替换内部链接。

这就是我搞砸的地方。

尽管我已经设置好，将文章正文的内部链接替换为已发表文章的外部 URL，但我还是没有更改《❌邮报》上的规范链接元标签，而是将它们包含在我的 RSS 提要❌.中

## 规范链接

我最近了解到，规范链接对搜索引擎优化非常重要。是的，你可能会认为我在这个行业多年，但并没有真正理解 SEO。开始吧，我的朋友们。规范 URL 指定了网页的首选版本，通常这应该是内容的原始来源。

当相似内容有多个版本时，搜索引擎不知道在搜索结果中显示哪一个。通过选择并指出哪个版本是规范的，这解决了重复内容的问题。

我们通过在所有其他非规范版本的`<head>`中设置一个`rel=canonical`来做到这一点，如下:

```
<link rel="canonical" href="https://PATH_TO_THE_CANONICAL_VERSION"> 
```

Enter fullscreen mode Exit fullscreen mode

有了这个额外的元标签，搜索引擎会将其他版本内容的链接计入标准版本。如果你决定要重新发布任何东西，记得在你的帖子上正确设置。

目前在 3.8.5 版本中，Jekyll 安装了一个基于 gem 的主题，名为 Minima。对于基于 gem 的主题，模板文件存在于主题的 gem 中，您无法直接看到它们。然而，这些默认的主题都可以被覆盖，或者你可以直接创建你自己的自定义主题。

要替换这些主题文件，请在项目中创建相关的文件和文件夹。Jekyll 文档给你一步一步的指导，我建议你浏览一下。

为了区分外部帖子和内部帖子，您必须向外部帖子添加一些[前置内容](https://jekyllrb.com/docs/front-matter/)，模板可以将它们用作标识符。在我的例子中，我为所有外部帖子添加了两个变量，`external_site`和`external_url`。

```
external_url: https://PATH_TO_THE_CANONICAL_VERSION
external_site: PUB_NAME 
```

Enter fullscreen mode Exit fullscreen mode

这两个变量将在很多地方使用，其中一个是默认模板，以确保规范链接设置正确:

```
{% if page.external_url %}
<link rel="canonical" href="{{ page.external_url }}">
{% else %}
<link rel="canonical" href="{{ page.url | replace:'index.html','' | prepend: site.baseurl | prepend: site.url }}">
<link rel="alternate" type="application/atom+xml" title="{{ site.title }}" href="{{ "/feed.xml" | prepend: site.baseurl | prepend: site.url }}">
{% endif %} 
```

Enter fullscreen mode Exit fullscreen mode

## 用外部 URL 替换内部 URL

有了在帖子前面定义的 2 个自定义变量，我们还可以用适当的外部 URL 替换内部帖子 URL 的任何实例。通常这发生在列表中，要么在你的主页上，要么在博客/写作页面上。

列出帖子的模板通常是这样的:

```
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

可以用一个条件来交换 URL:

```
<ul>
  {% for post in site.posts %}
    <li>
      {% if post.external_url %}
      <a href="{{ post.external_url }}">{{ post.title }}</a>
      {% else %}
      <a href="{{ post.url }}">{{ post.title }}</a>
      {% endif %}
    </li>
  {% endfor %}
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

## 外部链接的视觉提示

我们可以使用自定义变量`external_site`来添加一个相关的 CSS 类，用于设置外部链接的样式。

```
<a class="{{ post.external_site }} external-url" href="{{ post.external_url }}">{{ post.title }}</a> 
```

Enter fullscreen mode Exit fullscreen mode

这将生成以下标记:

```
<a class="PUB_NAME external-url" href="https://PATH_TO_THE_CANONICAL_VERSION">The title of your article</a> 
```

Enter fullscreen mode Exit fullscreen mode

我选择使用伪元素在我的链接前添加出版物的徽标，并在链接的末尾添加一个指示链接的图标。

```
.external-url::before,
.external-url::after {
  content: '';
  display: inline-block;
  height: 0.8em;
  width: 0.8em;
}

.external-url::before {
  margin-right: 0.25em;
  background-size: cover;
}

.external-url::after {
  background: url('/asseimg/icons/icon-link.svg') no-repeat;
  background-size: cover;
  margin-left: 0.25em;
}

.PUB_NAME::before {
  background: url('/asseimg/icons/PUB_LOGO.svg') no-repeat;
  background-size: cover;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你为许多不同的出版物写文章，那么你必须为每个出版物和它们相应的标志添加更多的类。

## 生成 RSS 提要

Jekyll 有一个用于生成 RSS 提要的官方插件，名为 [jekyll-feed](https://github.com/jekyll/jekyll-feed) ，要激活它，你必须将它添加到你的`_config_yml`文件中。如果你的站点托管在 GitHub 页面上，这个插件也可以工作，并在`/feed.xml`自动生成一个 Atom 提要。

要覆盖默认模板，您可以在项目的根目录下创建自己的`feed.xml`文件。关键是加一个条件排除某些帖子。我在我的外部帖子中添加了另一个名为`nofeed`的自定义前台事件变量。

```
nofeed: true 
```

Enter fullscreen mode Exit fullscreen mode

然后，在生成项目列表的部分`feed.xml`中使用它作为条件:

```
{% for post in site.posts limit:15 %}
  {% unless post.nofeed %}
  <item>
    {{ post.title | xml_escape }}
    <description>{{ post.content | xml_escape | truncatewords:120}}</description>
    <pubDate>{{ post.date | date_to_rfc822 }}</pubDate>
    <link>{{ site.url }}{{ post.url }}</link>
    <guid isPermaLink="true">{{ site.url }}{{ post.url }}</guid>
  </item>
  {% endunless %}
{% endfor %} 
```

Enter fullscreen mode Exit fullscreen mode

## 你可以做的其他事情

如果你在你的列表中包含了一小段被删节的内容作为摘要，最好在顶部添加一个注释，指出文章最初发表的地方。

如果你根本不希望内部帖子的 URL 出现在搜索引擎上，你也可以考虑给外部帖子的`<head>`添加一个`noindex`元标签，但这可能有点矫枉过正，因为只要设置了规范的 URL，这就不会影响原始内容的 SEO。我想。

另一个尝试是使用一个随机的字符串作为源文件名，以减少人们手动输入内部 URL 的几率，同样，在你做了所有其他事情之后，这可能是大材小用。

## 包装完毕

嗯，这总结了我在 Jekyll 上处理外部帖子的经验。也许能帮到别人。保持认真，我的朋友们。

## 相关阅读

*   rel=canonical:终极指南
*   哲基尔文档:主题
*   [Jekyll 的外部帖子](https://robots.thoughtbot.com/external-posts-in-jekyll)
*   从 Jekyll 的 RSS 中排除帖子
*   [莉斯·克莱恩关于哲基尔的笔记](https://learningnerd.com/2017/05/30/)