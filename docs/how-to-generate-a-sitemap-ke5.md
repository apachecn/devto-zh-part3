# 如何生成网站地图

> 原文：<https://dev.to/roelofjanelsinga/how-to-generate-a-sitemap-ke5>

[!["How to generate a sitemap"](img/640c85e8a062fe3dce8122933b6cec9f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3I5qbPrR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j9j7djpkoi19txus0d43.jpeg)

# 如何生成网站地图

在之前的一篇文章 [SEO 和开发者的个人营销](https://dev.to/articles/seo-and-personal-marketing-for-developers)中，我提到你需要生成一个网站地图，以便将网站上所有重要的页面提交到谷歌搜索控制台。但是如何生成网站地图呢？它看起来像什么？这些是我将在这篇文章中回答的问题。

## 一个网站地图文件的例子

在我开始之前，我想给你看一个站点地图文件的例子。它真的很简单，很容易添加新的网址。

```
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
    <url>
        <loc>https://example.com/</loc>
        <lastmod>2019-01-01</lastmod>
        <changefreq>monthly</changefreq>
        <priority>1</priority>
    </url>
</urlset> 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，这就是创建一个站点地图所需要做的全部工作。如您所见，一个“urlset”元素包装了所有内容。然后是“url”元素。这个元素包含了关于单个 URL 的所有信息，比如**URL(在 loc 元素中找到)、最后修改日期(lastmod)、页面优先级(priority)和页面的更改频率(changefreq)** 。

## 为 URL 信息接受的值

*   **loc** :在你的网站上找到的任何网址
*   **优先级**:0 到 1 之间的数字，1 为最重要的页面，0 为最不重要的页面。
*   **lastmod** :任何“yyyy-mm-dd”格式的日期
*   **changefreq** :年、月、周、日等。

## 手动创建 XML 文件

通读前两节的信息后，你就可以开始创建自己的网站地图了。如果你没有大量的页面想要包含在你的站点地图中，你可以简单地手工设置。如果你有很多页面，这可能是一个很大的工作量，你可以使用自动化服务。如果您有机会用 PHP 编写一个脚本来自动完成这项工作，您可以进入下一节。

## 自动创建 XML 文件

如果你的网站使用 PHP，你可以使用我为这个特定用例创建的包。你可以在[packagest](https://packagist.org/packages/roelofjan-elsinga/sitemap-generator)上找到它，然后用 composer:
安装它

```
composer require roelofjan-elsinga/sitemap-generator 
```

Enter fullscreen mode Exit fullscreen mode

您可以将该包合并到任何可能用来创建站点地图的脚本中。添加完所有链接后，可以将生成的 XML 保存到一个 sitemap.xml 文件中，该文件可以通过浏览器访问。

## 你把 sitemap XML 文件放在哪里？

放置你生成的站点地图最容易的位置是在*" your website . com/sitemap . XML "*。这是一个非常容易预测的地方，你希望尽可能简单地索引你所有的 URL。在你把网站地图文件放在正确的位置后，通过进入*" your website . com/path/to/sitemap . XML "*，验证你是否可以从浏览器访问该文件。如果你看到你的网址是正确的，你就可以进入下一步了。

## 向谷歌搜索控制台提交网站地图

现在你已经有了一个站点地图，你已经为最后一步做好了准备。提交你的网站地图到谷歌搜索控制台。幸运的是，这一步非常简单。首先，确保你已经为你的网站设置了谷歌搜索控制台，你可以通过阅读[“SEO 和开发者个人营销”](https://dev.to/articles/seo-and-personal-marketing-for-developers)了解如何设置。当你进入搜索控制台时，点击左侧边栏中的“网站地图”。您可以在这里输入站点地图的 URL。我的名字是*" roelofjanelsinga . com/sitemap . XML "*。我的域名已经在表单中输入，所以我只需填写 sitemap.xml。就这样，谷歌现在可以找到你的所有页面，并将其编入搜索系统。

如果你对这篇文章有任何问题或补充，请在 Twitter 上告诉我！如果你发现了错误或者有更好的信息我可以补充，我很乐意帮助你或者修改这篇文章。