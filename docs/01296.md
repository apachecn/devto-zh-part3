# 带注释的 JSON-LD 结构化数据示例

> 原文：<https://dev.to/gaijinity/annotated-json-ld-structured-data-examples-46ka>

# 注释 JSON-LD 结构化数据示例

### 想看看 JSON-LD 结构化数据“在野外”的一些真实例子吗？这篇文章有，加上注释。

安德鲁·韦尔奇

[![Json ld structured data in the wild](img/5ed3f926407948c2678793503a88e53a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PuESJ9mY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/json-ld-structured-data-in-the-wild.jpg)

结构化数据允许你告诉搜索引擎关于你的网页上有什么的上下文信息，而不是让他们去猜测。它使用来自 [schema .org](https://schema.org/docs/full.html) 的标准词汇，通过 [JSON-LD](https://json-ld.org/) 来表达数据之间的文本和联系。

许多文章谈到结构化数据；本文将关注在“野外”使用结构化数据的真实例子。它在示例中使用了用于 [Craft CMS](https://craftcms.com/) 的[SEO mat IC](https://nystudio107.com/plugins/seomatic)plu in，但是本文提供了任何人都可以使用的工具。

在本文中，我不打算向您推销使用结构化数据的好处。我假设你已经在船上了，并希望看到一些现实世界中，宣布的例子。

有关 JSON-LD 结构化数据的更多信息，请查看 [JSON-LD、结构化数据和 Erot i ca](https://dev.to/gaijinity/json-ld-structured-data-and-erotica-51ph-temp-slug-1704763) 文章。

## 结构化数据的一般方法

虽然结构化数据有几种模式，但谷歌、苹果和该行业的其他主要参与者已经明确表示，JSON-LD 是前进的方向。JSON-LD 是我们在 JavaScript 中习惯使用的 JSON，LD 代表“链接数据”。

定义了大量结构化数据模式供选择；我们如何决定实施什么？我的方法是双重的:

*   使用“最佳”方法尽可能明确地定义模式类型
*   使用 Google 公开支持的模式类型

毕竟，我们将结构化数据放在我们的网站上，这样搜索引擎就可以理解它，但是我们也希望对此保持务实，并将精力集中在我们知道 Google 会使用的模式类型上。

<aside>Google only con­cerns itself with a sub­set of schema​.org types</aside>

查看[谷歌公开支持的模式类型](https://developers.google.com/search/docs/data-types/article)以及[探索谷歌搜索图库](https://developers.google.com/search/docs/guides/search-gallery)，以互动的方式了解模式类型如何不仅影响知识图，还影响搜索引擎结果页面(SERP)。

## 节点标识字段和 mainEntityOfPage

在我们进入具体的例子之前，我想先讲几个一般性的概念。

第一个是当提交 JSON-LD 时，我大量使用了[节点标识符](https://www.w3.org/TR/2014/REC-json-ld-20140116/#node-identifiers)(`"[@id](https://dev.to/id) "`属性)来引用特定的 ic 模式，而不太需要重复的数据。我们可以有一个`Organization`模式，然后从多个地方引用它，而不是每次都复制整个模式数据。

这里有一个典型的例子:

```
 <script type="application/ld+json">
{
    "@context": "http://schema.org",
    "@id": "our-organization",
    "@type": "Organization"
}

<script type="application/ld+json">
{
    "@context": "http://schema.org",
    "@type": "Article",
    "publisher": {
        "@id": "our-organization"
    }
}
</script> 
```

在这种情况下，我们使用`our-organization`作为节点标识符，但通常只是作为一个提示，我们可以使用 URL 或带有散列的 URL，这样就感觉到名称空间。这真的不重要，只要它在你的网页上是唯一的。

可以把[节点标识符](https://www.w3.org/TR/2014/REC-json-ld-20140116/#node-identifiers)看作是定义本地模式图中另一个节点的标签或别名的一种方式。您为一个 JSON-LD 模式分配一个本地惟一的`"[@id](https://dev.to/id) "`，然后您可以从其他 JSON-LD 模式中引用这个`"[@id](https://dev.to/id) "`。

更多关于节点标识符的信息可以在[中找到,@id 在 json-ld syn tax 中有什么用？](https://webmasters.stackexchange.com/questions/98569/what-is-the-use-of-id-in-json-ld-syntax)和[Schema . org JSON-LD refer ence](https://stackoverflow.com/questions/34761970/schema-org-json-ld-reference)文章。

第页的[维护属性也值得特别注意:](https://schema.org/docs/datamodel.html#mainEntityBackground)

<aside>Indi­cates a page (or oth­er Cre­ative­Work) for which this thing is the main enti­ty being described</aside>

因此，如果一个网页是一篇文章，就像你现在正在阅读的这篇文章，它将有[篇文章](https://schema.org/Article) JSON-LD，其`mainEntityOfPage`属性值是文章本身的 URL:

```
 <script type="application/ld+json">
{
    "@context": "http://schema.org",
    "@type": "Article",
    "mainEntityOfPage": "https://nystudio107.com/blog/implementing-custom-json-ld-structured-data"
}
</script> 
```

它在说“嘿，我是这个 URL 上的网页的主题！”

现在让我们来看一些 spe cif ic 的例子。

## 你给了 107。与文章共页

让我们从查看您正在阅读的页面的结构化数据开始。只需选择一些如何映射数据的设置，下面是你默认从 [SEO mat ic](https://nystudio107.com/plugins/seomatic) :
得到的结果

```
 <script type="application/ld+json">
{
    "@context": "http://schema.org",
    "@type": "Article",
    "author": {
        "@id": "https://nystudio107.com/#identity"
    },
    "copyrightHolder": {
        "@id": "https://nystudio107.com/#identity"
    },
    "copyrightYear": "2019",
    "creator": {
        "@id": "https://nystudio107.com/#creator"
    },
    "dateModified": "2019-05-28T17:33:57-04:00",
    "datePublished": "2019-05-27T08:45:00-04:00",
    "description": "Want to see a some real-world examples of JSON-LD structured data \"in the wild\"? This article has that, plus annotation.",
    "headline": "Implementing Custom JSON-LD Structured Data",
    "image": {
        "@type": "ImageObject",
        "url": "https://nystudio107.com/img/blog/_1200x630_crop_center-center_82_none/json-ld-structured-data-in-the-wild.jpg"
    },
    "inLanguage": "en",
    "mainEntityOfPage": "https://nystudio107.com/blog/implementing-custom-json-ld-structured-data",
    "name": "Implementing Custom JSON-LD Structured Data",
    "publisher": {
        "@id": "https://nystudio107.com/#creator"
    },
    "url": "https://nystudio107.com/blog/implementing-custom-json-ld-structured-data"
}
</script> 
```

这篇文章中的大部分内容都很容易理解；但是为什么我们一开始就选择了《T2》这篇文章呢？为什么不是[的博文 ing](https://schema.org/BlogPosting) ？

答案很简单。谷歌[将文章](https://developers.google.com/search/docs/data-types/article)列为明确支持的类型。另外，如果我们的页面有 [Google AMP](https://dev.to/gaijinity/google-amp-should-you-care-3din-temp-slug-933989) 版本(我们为[这篇文章](https://nystudio107.com/blog/annotated-json-ld-structured-data-examples/amp)做了这个版本)，你的文章需要这个`Article`JSON-id 才能出现在 AMP stories 的传送带上。

注意，我们在几个地方使用带有`"[@id](https://dev.to/id) "`的[节点标识符](https://www.w3.org/TR/2014/REC-json-ld-20140116/#node-identifiers)来引用其他 JSON-LD 结构化数据节点，而不是重复内容。

<aside>By doing this, we are Link­ing Data nodes togeth­er… the LD part of JSON-LD.</aside>

需要注意的另一个关键问题是页面属性的维护，这表明这个[文章](https://schema.org/Article) JSON-LD 结构化数据是所列 URL 上的 web 页面的主要主题。

这有助于告诉谷歌你认为页面上最重要的部分是什么，而不是让它去猜测。

这个[组织](https://schema.org/Organization)模式用于拥有网站的实体(注意`"[@id](https://dev.to/id) ": "[https://nystudio107.com/#identity](https://nystudio107.com/#identity)"`节点标识符):

```
 <script type="application/ld+json">
{
    "@context": "http://schema.org",
    "@id": "https://nystudio107.com/#identity",
    "@type": "Organization",
    "address": {
        "@type": "PostalAddress",
        "addressCountry": "US",
        "addressLocality": "Webster",
        "addressRegion": "NY",
        "postalCode": "14580"
    },
    "alternateName": "nys",
    "description": "We do technology-based consulting, branding, design, and development. Making the web better one site at a time, with a focus on performance, usability & SEO",
    "email": "info@nystudio107.com",
    "founder": "Andrew Welch, Polly Welch",
    "foundingDate": "2013-05-02",
    "foundingLocation": "Webster, NY",
    "image": {
        "@type": "ImageObject",
        "height": "2048",
        "url": "https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/site/nys_logo_seo.png",
        "width": "2048"
    },
    "logo": {
        "@type": "ImageObject",
        "height": "60",
        "url": "https://nystudio107.com/img/site/_600x60_fit_center-center_82_none/nys_logo_seo.png",
        "width": "600"
    },
    "name": "nystudio107",
    "sameAs": [
        "https://twitter.com/nystudio107",
        "https://www.facebook.com/newyorkstudio107",
        "https://plus.google.com/+nystudio107com",
        "https://www.youtube.com/channel/UCOZTZHQdC-unTERO7LRS6FA",
        "https://github.com/nystudio107"
    ],
    "url": "https://nystudio107.com/"
}
</script> 
```

它的存在主要是为了让其他 JSON-LD 结构化数据如`Article`可以链接到它，并说“这是作者”和“这是版权持有者”等等。

您可以进一步通过`employee`属性添加在该组织工作的人员的列表，或者通过`alumni`属性添加曾经在该组织工作的人员的列表。

这样，你就建立了事物之间的联系，帮助谷歌理解它们之间的联系。

这个[组织](https://schema.org/Organization)模式用于创建网站的实体(注意`"[@id](https://dev.to/id) ": "[https://nystudio107.com/#creator](https://nystudio107.com/#creator)"`节点标识符):

```
 <script type="application/ld+json">
{
    "@context": "http://schema.org",
    "@id": "https://nystudio107.com/#creator",
    "@type": "Organization",
    "address": {
        "@type": "PostalAddress",
        "addressCountry": "US",
        "addressLocality": "Webster",
        "addressRegion": "NY",
        "postalCode": "14580"
    },
    "alternateName": "nys",
    "description": "We do technology-based consulting, branding, design, and development. Making the web better one site at a time, with a focus on performance, usability & SEO",
    "email": "info@nystudio107.com",
    "founder": "Andrew Welch, Polly Welch",
    "foundingDate": "2013-05-02",
    "foundingLocation": "Webster, NY",
    "image": {
        "@type": "ImageObject",
        "height": "1042",
        "url": "https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/site/nys_logo_seo.png",
        "width": "1042"
    },
    "logo": {
        "@type": "ImageObject",
        "height": "60",
        "url": "https://nystudio107.com/img/site/_600x60_fit_center-center_82_none/nys_logo_seo.png",
        "width": "600"
    },
    "name": "nystudio107",
    "url": "https://nystudio107.com/"
}
</script> 
```

它的存在主要是为了让其他 JSON-LD 结构化数据(如`Article`)可以链接到它，并说“这是创建器”等等。

就像`identity` [组织](https://schema.org/Organization)一样，你可以进一步增加关于`employee`、`alumni`等的附加信息。

最后，我们在 ed [Bread crum b List](https://schema.org/BreadcrumbList) schema 中有了自动材质生成器，它指示页面在站点层次结构中的位置:

```
 <script type="application/ld+json">
{
    "@context": "http://schema.org",
    "@type": "BreadcrumbList",
    "description": "Breadcrumbs list",
    "itemListElement": [
        {
            "@type": "ListItem",
            "item": {
                "@id": "https://nystudio107.com/",
                "name": "Homepage"
            },
            "position": 1
        },
        {
            "@type": "ListItem",
            "item": {
                "@id": "https://nystudio107.com/blog",
                "name": "Blog Index"
            },
            "position": 2
        },
        {
            "@type": "ListItem",
            "item": {
                "@id": "hthttps://nystudio107.comt/blog/tips-for-using-seomatic-effectively",
                "name": "Advanced SEOmatic Tips"
            },
            "position": 3
        }
    ],
    "name": "Breadcrumbs"
}
</script> 
```

这是一个[支持的 Google type](https://developers.google.com/search/docs/data-types/breadcrumb) 帮助它理解你的网站结构，也可以在你的搜索引擎结果页面(SERP)上显示为实际的面包屑链接。

所以这是一个很好的起点，但是我们可以做得更好。让我们使用 [SEO mat ic](https://nystudio107.com/plugins/seomatic) Twig API 给我们的`mainEntityOfPage` JSON-LD:
添加一点点

```
 {#
 # Add Article schema for the this plugin as per:
 # https://schema.org/Article
 #
 # @param article the Entry for the blog article
 #}

{# Merge in the additional properties to the Article mainEntityOfPage #}
{% set mainEntity = seomatic.jsonLd.get('mainEntityOfPage') %}
{% do mainEntity.setAttributes({
    'articleSection': article.blogCategory[0].title,
    'genre': 'Technology',
    'headline': article.title,
    'speakable': {
        'type': 'SpeakableSpecification',
        'cssSelector': [
            '.blog-wrapper',
        ],
    },
}) %} 
```

这增加了一些属性，导致以下 JSON-LD 结构化数据:

```
 <script type="application/ld+json">
{
    "@context": "http://schema.org",
    "@type": "Article",
    "articleSection": "Insights",
    "author": {
        "@id": "https://nystudio107.com/#identity"
    },
    "copyrightHolder": {
        "@id": "https://nystudio107.com/#identity"
    },
    "copyrightYear": "2019",
    "creator": {
        "@id": "https://nystudio107.com/#creator"
    },
    "dateModified": "2019-05-28T17:33:57-04:00",
    "datePublished": "2019-05-27T08:45:00-04:00",
    "description": "Want to see a some real-world examples of JSON-LD structured data \"in the wild\"? This article has that, plus annotation.",
    "genre": "Technology",
    "headline": "Implementing Custom JSON-LD Structured Data",
    "image": {
        "@type": "ImageObject",
        "url": "https://nystudio107.com/img/blog/_1200x630_crop_center-center_82_none/json-ld-structured-data-in-the-wild.jpg"
    },
    "inLanguage": "en",
    "mainEntityOfPage": "https://nystudio107.com/blog/implementing-custom-json-ld-structured-data",
    "name": "Implementing Custom JSON-LD Structured Data",
    "publisher": {
        "@id": "https://nystudio107.com/#creator"
    },
    "speakable": {
        "@type": "SpeakableSpecification",
        "cssSelector": [
            ".blog-wrapper"
        ]
    },
    "url": "https://nystudio107.com/blog/implementing-custom-json-ld-structured-data"
}
</script> 
```

根据我们添加的属性，这里的每件事情都应该很简单。我们只是通过增加一些额外的信息来充实内容。

可能最酷的部分是`speakable`属性，它可以让你通过[朗读规范](https://schema.org/SpeakableSpecification)告诉屏幕阅读器哪些文本是可以朗读的。在我们的例子中，`.blog-wrapper` CSS 类选择器位于围绕我们的文章内容的`<div>`上。

这有助于提高屏幕阅读器的可用性，也为谷歌助手和智能设备所用。

其他可以做的事情:

*   不要只把组织列为作者，而要提供写这篇文章的实际作者的信息
*   给`identity` &创建者[组织](https://schema.org/Organization)添加`employee`和明矾 ni 属性关系
*   通过`interactionStatistic`添加[互动计数器](https://schema.org/InteractionCounter)，用于用户对文章、转发等的评论。
*   为具有特定步骤来完成任务的部分产品添加 [How To](https://schema.org/HowTo) 步骤

查看 [schema .org](https://schema.org/) 中关于`mainEntityOfPage`模式类型的规范说明(在本例中为 [Arti cle](https://schema.org/Article) ),看看添加什么属性是有意义的。然后使用谷歌结构化数据测试工具验证你的页面。

**Google SERP 预览** → [nystudio107 文章页面](https://www.google.com/search?prvw=AHHjJUN9NjCNWFoXOR9VzSbjWgydphn53Q&q=previewid%3A4efcc1f9-27a5-49c4-8bc6-b1fe589f5eb6&useragent=Mozilla%2F5.0+%28Linux%3B+Android+7.1.1%3B+Nexus+6+Build%2FN6F27M%29+AppleWebKit%2F537.36+%28KHTML%2C+like+Gecko%29+Chrome%2F66.0.3359.106+Mobile+Safari%2F537.36&filter=0&gws_rd=cr&newwindow=1&igu=1)

**Google 结构化数据测试工具**链接→ [nys tu dio107 .com 文章页面](https://search.google.com/structured-data/testing-tool#url=https%3A%2F%2Fnystudio107.com%2Fblog%2Fimplementing-custom-json-ld-structured-data)

## 纽约时报

接下来，让我们来看一下 JSON-LD 结构化数据，具体说明了 [SEO mat ic](https://nystudio107.com/plugins/seomatic) 插件。

我们将跳过前面提到的身份、创建者和面包屑，因为它们在这里也是一样的。

```
 <script type="application/ld+json">
{
    "@context": "http://schema.org",
    "@type": "WebApplication",
    "author": {
        "@id": "https://nystudio107.com/#identity"
    },
    "copyrightHolder": {
        "@id": "https://nystudio107.com/#identity"
    },
    "copyrightYear": "2017",
    "creator": {
        "@id": "https://nystudio107.com/#creator"
    },
    "dateModified": "2019-05-21T11:35:49-04:00",
    "datePublished": "2017-12-31T19:48:00-05:00",
    "description": "SEOmatic facilitates modern SEO best practices & implementation for Craft CMS 3\. It is a turnkey SEO system that is comprehensive, powerful, and flexible.",
    "headline": "SEOmatic Craft CMS Plugin",
    "image": {
        "@type": "ImageObject",
        "url": "https://nystudio107.com/img/plugins/seomatic/_1200x630_crop_center-center_82_none/plugin-logo.png"
    },
    "inLanguage": "en",
    "mainEntityOfPage": "https://nystudio107.com/plugins/seomatic",
    "name": "SEOmatic Craft CMS Plugin",
    "publisher": {
        "@id": "https://nystudio107.com/#creator"
    },
    "url": "https://nystudio107.com/plugins/seomatic"
}
</script> 
```

在这里，我们再次为我们的[web 应用程序](https://schema.org/WebApplication) JSON-LD 结构化数据获得一些合理的默认值，并且我们明确表示这是前面讨论过的`mainEntityOfPage`。

但是用一小段来自 SEO mat ic 的自定义代码，我们可以让它变得更有趣:

```
 {#
 # Add WebApplication schema for the this plugin as per:
 # https://schema.org/WebApplication
 #
 # @param appInfo the appInfo Entry for the plugin
 #}

{# Offer for the plugin #}
{% set offer = seomatic.jsonLd.create({
    'type': 'Offer',
    'id': '{seomatic.site.identity.genericUrl}#plugin-offer',
    'availability': 'http://schema.org/InStock',
    'price': appInfo.pluginPrice ??? '0.00',
    'priceCurrency': 'USD',
    'priceValidUntil': now | date_modify('+1 year') | atom,
    'url': appInfo.pluginStoreLink,
}) %}

{# Merge in the additional properties to the WebApplication mainEntityOfPage #}
{% set mainEntity = seomatic.jsonLd.get('mainEntityOfPage') %}
{% set screenshotAsset = appInfo.pluginSeoImage.one() %}
{% set logoAsset = appInfo.pluginIcon.one() %}
{% do mainEntity.setAttributes({
    'name': appInfo.title,
    'applicationCategory': 'Plugin',
    'browserRequirements': 'Requires Craft CMS 3',
    'downloadUrl': appInfo.pluginStoreLink,
    'installUrl': appInfo.pluginStoreLink,
    'image': {
        'type': 'ImageObject',
        'url': logoAsset.url,
        'width': logoAsset.getWidth(),
        'height': logoAsset.getHeight(),
    },
    'screenshot': {
        'type': 'ImageObject',
        'url': screenshotAsset.url,
        'width': screenshotAsset.getWidth(),
        'height': screenshotAsset.getHeight(),
    },
    'offers': offer,
    'operatingSystem': 'Web Browser',
}) %}

{# Product for the plugin #}
{% set productConfig = mainEntity.getAttributes() | merge({
    'key': 'Plugin-Product',
    'type': 'Product',
    'brand': {
        'id': '{seomatic.site.identity.genericUrl}#identity',
    },
    'category': 'Plugin',
    'isRelatedTo': {
        'type': 'Product',
        'name': 'Craft CMS',
        'description': 'Craft is a flexible, user-friendly CMS for creating custom digital experiences on the web and beyond.',
        'slogan': 'Craft is the CMS that makes the whole team happy.',
        'url': 'https://craftcms.com/',
        'offers': {
            'type': 'Offer',
            'availability': 'http://schema.org/InStock',
            'price': '299.00',
            'priceCurrency': 'USD',
            'priceValidUntil': now | date_modify('+1 year') | atom,
            'url': 'https://craftcms.com/pricing',
        },
    },
    'logo': {
        'type': 'ImageObject',
        'url': logoAsset.url,
        'width': logoAsset.getWidth(),
        'height': logoAsset.getHeight(),
    },
    'productID': appInfo.slug,
    'sku': appInfo.slug,
    'slogan': appInfo.pluginSlogan,
}) %}
{% set product = seomatic.jsonLd.create(productConfig) %} 
```

这导致更加充实的 [WebAp 应用](https://schema.org/WebApplication) JSON-LD 结构化数据，以及创建新的[产品](https://schema.org/Offer)和[产品](https://schema.org/Product) JSON-LD 结构化数据模式:

```
 <script type="application/ld+json">
{
    "@context": "http://schema.org",
    "@type": "WebApplication",
    "applicationCategory": "Plugin",
    "author": {
        "@id": "https://nystudio107.com/#identity"
    },
    "browserRequirements": "Requires Craft CMS 3",
    "copyrightHolder": {
        "@id": "https://nystudio107.com/#identity"
    },
    "copyrightYear": "2017",
    "creator": {
        "@id": "https://nystudio107.com/#creator"
    },
    "dateModified": "2019-05-21T11:35:49-04:00",
    "datePublished": "2017-12-31T19:48:00-05:00",
    "description": "SEOmatic facilitates modern SEO best practices & implementation for Craft CMS 3\. It is a turnkey SEO system that is comprehensive, powerful, and flexible.",
    "downloadUrl": "https://plugins.craftcms.com/seomatic",
    "headline": "SEOmatic Craft CMS Plugin",
    "image": {
        "@type": "ImageObject",
        "height": "250",
        "url": "https://nystudio107.com/img/plugins/seomatic/seomatic-icon.svg",
        "width": "250"
    },
    "inLanguage": "en",
    "installUrl": "https://plugins.craftcms.com/seomatic",
    "mainEntityOfPage": "https://nystudio107.com/plugins/seomatic",
    "name": "SEOmatic",
    "offers": {
        "@id": "https://nystudio107.com/#plugin-offer"
    },
    "operatingSystem": "Web Browser",
    "publisher": {
        "@id": "https://nystudio107.com/#creator"
    },
    "screenshot": {
        "@type": "ImageObject",
        "height": "500",
        "url": "https://nystudio107.com/img/plugins/seomatic/plugin-logo.png",
        "width": "450"
    },
    "url": "https://nystudio107.com/plugins/seomatic"
}
</script> 
```

在这里，我们再一次填充了一些对[web 应用](https://schema.org/WebApplication)有意义的属性(对于一个基于 web 的 CMS 插件来说，这是最正确的)。

我们通过`"[@id](https://dev.to/id) ": "[https://nystudio107.com/#plugin-offer](https://nystudio107.com/#plugin-offer)"` [节点标识符](https://www.w3.org/TR/2014/REC-json-ld-20140116/#node-identifiers) :
添加了`downloadUrl`、`installUrl`、`screenshot`等属性关系，以及更有趣的`offers`

```
 <script type="application/ld+json">
{
    "@context": "http://schema.org",
    "@id": "https://nystudio107.com/#plugin-offer",
    "@type": "Offer",
    "availability": "http://schema.org/InStock",
    "price": "99",
    "priceCurrency": "USD",
    "priceValidUntil": "2020-05-28T19:03:39-04:00",
    "url": "https://plugins.craftcms.com/seomatic"
}
</script> 
```

由于我们的一些插件可供出售，我们可以通过列出了`price`、`priceCurrency`、`availability`等的[报价](https://schema.org/Offer)模式来表明这一点。

**提示:**如果你的产品是免费的，你应该通过拥有以上所有的属性来表明，但是把`price`设置为`0`。

我们用一个[节点标识符](https://www.w3.org/TR/2014/REC-json-ld-20140116/#node-identifiers)来实现它，这样我们就可以从`WebApplication`和`Product` JSON-LD:
中引用它

```
 <script type="application/ld+json">
{
    "@context": "http://schema.org",
    "@type": "Product",
    "brand": {
        "@id": "https://nystudio107.com/#identity"
    },
    "category": "Plugin",
    "description": "SEOmatic facilitates modern SEO best practices & implementation for Craft CMS 3\. It is a turnkey SEO system that is comprehensive, powerful, and flexible.",
    "image": {
        "@type": "ImageObject",
        "height": "250",
        "url": "https://nystudio107.com/img/plugins/seomatic/seomatic-icon.svg",
        "width": "250"
    },
    "isRelatedTo": {
        "@type": "Product",
        "description": "Craft is a flexible, user-friendly CMS for creating custom digital experiences on the web and beyond.",
        "name": "Craft CMS",
        "offers": {
            "@type": "Offer",
            "availability": "http://schema.org/InStock",
            "price": "299.00",
            "priceCurrency": "USD",
            "priceValidUntil": "2020-11-20T09:07:28-05:00",
            "url": "https://craftcms.com/pricing"
        },
        "slogan": "Craft is the CMS that makes the whole team happy.",
        "url": "https://craftcms.com/"
    },
    "logo": {
        "@type": "ImageObject",
        "height": "250",
        "url": "https://nystudio107.com/img/plugins/seomatic/seomatic-icon.svg",
        "width": "250"
    },
    "mainEntityOfPage": "https://nystudio107.com/plugins/seomatic",
    "name": "SEOmatic",
    "offers": {
        "@id": "https://nystudio107.com/#plugin-offer"
    },
    "productID": "seomatic",
    "sku": "seomatic",
    "url": "https://nystudio107.com/plugins/seomatic"
}
</script> 
```

我们从我们的[web 应用程序](https://schema.org/WebApplication)中提取了所有的属性，并用它们创建了一个新的[产品](https://schema.org/Product) JSON-LD 模式(SEO mat ic 去掉了新模式类型中不存在的任何属性)。

然后我们给`Product`添加了一些特殊的 cif ic，比如`isRelatedTo`属性，将它与 [Craft CMS](https://craftcms.com/) 关联起来(因为我们的插件需要它)。

那么为什么要创建一个新的`Product`模式呢？因为这又是一种谷歌特别支持的类型。

这也可能导致你的`Product`出现在搜索引擎结果页面(SERP)上，带有缩略图截屏、评论、评分和其他特征，这些特征是[鼓励参与](https://developers.google.com/search/docs/guides/search-gallery)。

其他可以做的事情:

*   [产品](https://schema.org/Product)应该有一个`aggregateRating`
*   添加合法用户-提交 ted [评论](https://schema.org/Review)

**Google SERP 预览**→[nystudio 107 plug in Page](https://www.google.com/search?prvw=AHHjJUNxHw2bA-aau7gmXikpjRMhEgsiag&q=previewid%3A5f95e05e-a0c2-4077-8b49-605450456018&useragent=Mozilla%2F5.0+%28Linux%3B+Android+7.1.1%3B+Nexus+6+Build%2FN6F27M%29+AppleWebKit%2F537.36+%28KHTML%2C+like+Gecko%29+Chrome%2F66.0.3359.106+Mobile+Safari%2F537.36&filter=0&gws_rd=cr&newwindow=1&igu=1)

**Google Rich 结果测试**→[nystudio 107 plug in Page](https://search.google.com/test/rich-results?id=oZLT8J4GjJ_S7OLzILcqkg)

**Google 结构化数据测试工具**链接→[nys tu dio 107 . com plug in Page](https://search.google.com/structured-data/testing-tool#url=https%3A%2F%2Fnystudio107.com%2Fplugins%2Fseomatic)

## 开发模式。fm 关于页面

开发模式。fm About page 是一个很好的例子，说明了我们如何实现一个带有[问题](https://schema.org/Question) & [答案](https://schema.org/Answer)的 [FAQ 页面](https://schema.org/FAQPage) schema .org 类型，它可以显示在 Google 搜索引擎结果页面(SERP)上。查看[谷歌开发者搜索常见问题页面](https://developers.google.com/search/docs/data-types/faqpage)了解详情。

```
 {
  "@context": "http://schema.org",
  "@type": "FAQPage",
  "author": {
    "@id": "https://devmode.fm/#identity"
  },
  "copyrightHolder": {
    "@id": "https://devmode.fm/#identity"
  },
  "copyrightYear": "2017",
  "creator": {
    "@id": "https://devmode.fm/#creator"
  },
  "dateModified": "2019-08-19T11:19:14-04:00",
  "datePublished": "2017-12-11T13:44:33-05:00",
  "description": "devMode.fm is a bi-weekly podcast dedicated to the tools, techniques, and technologies used in modern web development. Each episode, we have a cadre of hosts discussing the latest hotness, pet peeves, and technologies we use every day. We all come from a Craft CMS background, but we'll be focusing on other cool frontend development technologies as well.",
  "headline": "About the podcast",
  "image": {
    "@type": "ImageObject",
    "url": "https://devmode.fm/assets/site/_1200x630_crop_center-center_82_none/devmode_light-itunes.jpg?mtime=1515536278"
  },
  "inLanguage": "en-us",
  "mainEntity": [
    {
      "@type": "Question",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "<p>devMode.fm is a bi-weekly podcast dedicated to the tools, techniques, and technologies used in modern web development. Each episode, we have a cadre of hosts discussing the latest hotness, pet peeves, and technologies we use every day. We all come from a Craft CMS background, but we'll be focusing on other cool frontend development technologies as well.</p>"
      },
      "name": "What is the devMode.fm podcast all about?"
    },
    {
      "@type": "Question",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "<p>It sure is! The source code for this website is MIT licensed, and can be found on Github: <a href=\"https://github.com/nystudio107/devmode\" target=\"_blank\" rel=\"noreferrer noopener\">nystudio107/devmode</a>.</p>"
      },
      "name": "Is the source code to the devMode.fm website available?"
    },
    {
      "@type": "Question",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "<p>The devMode.fm website is built with:</p><a href=\"https://craftcms.com/\" target=\"_blank\">Craft 3 CMS</a>, <a href=\"https://tailwindcss.com/\" target=\"_blank\">Tailwind CSS</a>, <a href=\"https://vuejs.org/\" target=\"_blank\">Vue.js</a>, <a href=\"https://webpack.js.org/\" target=\"_blank\">webpack</a>, <a href=\"https://www.nginx.com/\" target=\"_blank\">Nginx</a>, <a href=\"https://www.postgresql.org\" target=\"_blank\">PostgreSQL</a>, <a href=\"https://forge.laravel.com\" target=\"_blank\">Forge</a>, <a href=\"https://github.com/nystudio107/craft\" target=\"_blank\">nystudio107/craft</a>"
      },
      "name": "What technologies were used building the devMode.fm website?"
    }
  ],
  "mainEntityOfPage": "https://devmode.fm/about",
  "name": "About the podcast",
  "publisher": {
    "@id": "https://devmode.fm/#creator"
  },
  "url": "https://devmode.fm/about"
} 
```

这里我们已经在 CP 的 SEO mat ic set tings 中设置了页面的 **Main Enti ty 到 **FAQ 页面**，然后我们使用一个小树枝从一个矩阵块中读取 FAQ，这个矩阵块有字段`question`、`answer`和`links` :** 

```
 {#
 # Add FAQPage schema for the this page as per:
 # https://schema.org/FAQPage
 #
 # @param faqs the FAQs matrix blocks
 # @param showInfo the showInfo Global for the show
 #}

{% set faqsArray = [] %}
{% for faq in faqs.all() %}
    {% set faqLinks = '' %}
    {% for link in faq.links %}
        {%- set linkHtml -%}
            <a href="{{ link.linkUrl }}" target="_blank">{{ link.linkText }}</a>
        {%- endset -%}
        {% set faqLinks = faqLinks ~ linkHtml ~ (loop.last ? '' : ', ') %}
    {% endfor %}
    {% set faqsArray = faqsArray | merge([seomatic.jsonLd.create({
        'type': 'Question',
        'name': faq.question,
        'acceptedAnswer': {
            'type': 'Answer',
            'text': (faq.answer ~ faqLinks),
        },
    }, false)]) %}
{% endfor %}

{# Merge in the additional properties to the FAQpage mainEntityOfPage #}
{% set mainEntity = seomatic.jsonLd.get('mainEntityOfPage') %}
{% do mainEntity.setAttributes({
    'mainEntity': faqsArray,
}) %} 
```

在这种情况下，我们将`mainEntity`属性设置为一组[问题 JSON-LD](https://schema.org/Question) 类型，每个类型都有一个嵌入在`acceptedAnswer`字段中的[答案 JSON-LD](https://schema.org/Answer) 类型。

这可能会导致您的`FAQPage`问题&答案出现在搜索引擎结果页面(SERP)上。

**谷歌 SERP 预览图** → [开发模式。fm 关于页面](https://search.google.com/test/rich-results?view=search-preview&id=UTxlLUl7dhK31iEYkDedmQ)

**谷歌丰富结果测试** → [开发模式。fm 关于页面](https://search.google.com/test/rich-results?id=UTxlLUl7dhK31iEYkDedmQ)

**Google 结构化数据测试工具**链接→ [开发模式。fm 关于页面](https://search.google.com/structured-data/testing-tool#url=https%3A%2F%2Fdevmode.fm%2Fabout)

## 开发模式。fm 剧集页面

最后，让我们看看在 [dev 模式下剧集页面的 JSON-LD 结构化数据。调频](https://devMode.fm)，在这种情况下[网络包内&与肖恩拉金](https://devmode.fm/episodes/webpack-inside-out-with-sean-larkin)插曲。

我们将跳过身份，创建者和面包屑提到耳朵里。尽管此站点的值会有所不同，但填写的属性是相同的。

```
 <script type="application/ld+json">
{
    "@context": "http://schema.org",
    "@type": "RadioEpisode",
    "author": {
        "@id": "https://devmode.fm/#identity"
    },
    "copyrightHolder": {
        "@id": "https://devmode.fm/#identity"
    },
    "copyrightYear": "2019",
    "creator": {
        "@id": "https://devmode.fm/#creator"
    },
    "dateModified": "2019-05-27T09:10:22-04:00",
    "datePublished": "2019-05-27T09:05:00-04:00",
    "description": "In this episode, we talk to webpack core maintainer Sean Larkin about what webpack is, who it's intended for, and where it's going in the future!\n\nWe discuss the serendipitous history of how Sean came to be a webpack core maintainer, and how his job at Microsoft came about as a result of it.\n\nJoin by guest host Jake Dohm, we then go on to discuss a whole lot of gritty technical detail of how webpack works, go through the various terminology, talk about Web Assembly, and what the future holds for webpack 5 and beyond.\n\nSean also drops some truth-bombs about CSS being flawed, and browser makers conspiring to kill off webpack. Tune in for the good stuff!",
    "headline": "webpack inside & out with Sean Larkin",
    "image": {
        "@type": "ImageObject",
        "url": "https://devmode.fm/assets/site/_1200x630_crop_center-center_82_none/devmode_light-itunes.jpg"
    },
    "inLanguage": "en-us",
    "mainEntityOfPage": "https://devmode.fm/episodes/webpack-inside-out-with-sean-larkin",
    "name": "webpack inside & out with Sean Larkin",
    "publisher": {
        "@id": "https://devmode.fm/#creator"
    },
    "url": "https://devmode.fm/episodes/webpack-inside-out-with-sean-larkin"
}
</script> 
```

我们再次为我们的[电台插曲](https://schema.org/RadioEpisode)模式(还没有[豆荚演员](https://github.com/schemaorg/schemaorg/issues/373)模式)有了一个非常好的起点，那就是我们的`mainEntityOfPage`。但是在这种情况下，我们可以添加更多的信息来描述这个播客所属的系列，等等。

我们将再次使用 [SEO mat ic](https://nystudio107.com/plugins/seomatic) Twig API 来这样做:

```
 {#
 # Add audio schema for the this episode as per:
 # https://schema.org/RadioEpisode
 #
 # @param episode the episode Entry for the current page
 # @param fileInfo summary of the episode media file info as per:
 # https://github.com/nystudio107/craft-transcoder#getting-information-about-a-videoaudio-file
 # @param showInfo the showInfo Global for the show
 # @param audioUrl the URL to the audio for this episode, wrapped in the
 # PodTrac.com tracking redirect
 #}

{% from "_partials/macros.twig" import addPersonArray %}
{% from "_partials/macros.twig" import addMentionsArray %}

{# RadioSeries for the episode to belong to #}
{% set showImage = showInfo.showImage.one() %}
{% set radioSeries = seomatic.jsonLd.create({
    'type': 'RadioSeries',
    'id': '{seomatic.site.identity.genericUrl}#radio-series',
    'name': showInfo.showTitle,
    'description': showInfo.showDescription,
    'url': showInfo.showUrl,
    'mainEntityOfPage': siteUrl,
    'inLanguage': '{seomatic.meta.language}',
    'copyrightHolder': {
        'id': '{seomatic.site.identity.genericUrl}#identity',
    },
    'author': {
        'id': '{seomatic.site.identity.genericUrl}#identity',
    },
    'creator': {
        'id': '{seomatic.site.creator.genericUrl}#creator',
    },
    'image': {
        'type': 'ImageObject',
        'url': showImage.url,
        'width': showImage.width,
        'height': showImage.height,
    },
}) %}

{# Person array of the episode hosts #}
{% do addPersonArray(radioSeries, 'actor', craft.users.group("hosts").all()) %}
{# Person array of the episode directors #}
{% do addPersonArray(radioSeries, 'director', craft.users.group("owners").all()) %}

{# AudioObject for the episode #}
{% set audio = seomatic.jsonLd.create({
    'type': 'AudioObject',
    'bitrate': '64k',
    'contentSize': fileInfo.size,
    'contentUrl': audioUrl,
    'duration': fileInfo.duration,
    'embedUrl': siteUrl("/player-card/#{episode.slug}"),
    'encodingFormat': 'audio/mpeg',
    'partOfSeries': {
        'id': '{seomatic.site.identity.genericUrl}#radio-series',
    },
    'productionCompany': {
        'id': '{seomatic.site.creator.genericUrl}#creator',
    },
    'uploadDate': episode.postDate | rss,
}, false) %}

{# Merge in the additional properties to the RadioEpisode mainEntityOfPage #}
{% set mainEntity = seomatic.jsonLd.get('mainEntityOfPage') %}
{% do mainEntity.setAttributes({
    'audio': audio,
    'episodeNumber': episode.episodeNumber,
    'genre': showInfo.showGenre,
    'isAccessibleForFree': true,
    'productionCompany': {
        'id': '{seomatic.site.creator.genericUrl}#creator',
    },
}) %}

{# Person array of the episode hosts #}
{% do addPersonArray(mainEntity, 'actor', episode.episodeHosts.all()) %}
{# Person array of the episode directors #}
{% do addPersonArray(mainEntity, 'director', craft.users.group("owners").all()) %}
{# Thing array of the episode mentions #}
{% do addMentionsArray(mainEntity, 'mentions', episode.episodeReferenceLinks) %} 
```

这变得更加复杂，我们甚至包括了两个宏来帮助生成附加的 JSON-LD 结构化数据:

```
 {# Add a Person array from `users` to the `property` of `jsonLd` #}
{% macro addPersonArray(jsonLd, property, users) %}
    {% set usersArray = [] %}
    {% for user in users %}
        {% set usersArray = usersArray | merge([seomatic.jsonLd.create({
            'type': 'Person',
            'affiliation': user.profileCompany,
            'description': user.profileBio,
            'jobTitle': user.profileTitle,
            'familyName': user.lastName,
            'givenName': user.firstName,
            'name': user.fullName,
            'sameAs': [
                user.profileTwitterUrl,
                user.profileGithubUrl,
            ],
            'url': user.profileUrl,
        }, false)]) %}
    {% endfor %}
    {% do jsonLd.setAttributes({
        (property): usersArray
    }) %}
{% endmacro addPersonArray %}

{# Add a Thing array from `mentions` to the `property` of `jsonLd` #}
{% macro addMentionsArray(jsonLd, property, mentions) %}
    {% set mentionsArray = [] %}
    {% for mention in mentions %}
        {% set mentionsArray = mentionsArray | merge([seomatic.jsonLd.create({
            'type': 'Thing',
            'name': mention.linkName,
            'url': mention.linkUrl,
        }, false)]) %}
    {% endfor %}
    {% do jsonLd.setAttributes({
        (property): mentionsArray
    }) %}
{% endmacro addMentionsArray %} 
```

我认为理解上面代码的最简单的方法是查看输出的结果，所以我们就这么做吧，现在来看看我们的[radio episode](https://schema.org/RadioEpisode)`mainEntityOfPage`JSON-LD:

```
 <script type="application/ld+json">
{
    "@context": "http://schema.org",
    "@type": "RadioEpisode",
    "actor": [
        {
            "@type": "Person",
            "affiliation": "nystudio107",
            "description": "Andrew Welch has been in the tech business since he was 15 years old. After a stint running a software company for a couple of decades, he's now immersing himself in doing consulting to help businesses use technology effectively.",
            "familyName": "Welch",
            "givenName": "Andrew",
            "jobTitle": "Demolitions Expert",
            "name": "Andrew Welch",
            "sameAs": [
                "https://twitter.com/nystudio107",
                "https://github.com/nystudio107"
            ],
            "url": "https://nystudio107.com"
        },
        {
            "@type": "Person",
            "affiliation": "MDD",
            "description": "Jonathan is principal/CEO of MDD, a small web agency based in Atlanta, Georgia. He has been developing for the web since 2005 and Craft CMS since 2013\. He used to have free time and cool hobbies until his second child arrived on December 6, 2017\. He loves his work and his family.",
            "familyName": "Melville",
            "givenName": "Jonathan",
            "jobTitle": "Jonathan Melville",
            "name": "Jonathan Melville",
            "sameAs": [
                "https://twitter.com/jonmelville",
                "https://github.com/jonathanmelville"
            ],
            "url": "https://codemdd.io"
        }
    ],
    "audio": {
        "@context": "http://schema.org",
        "@type": "AudioObject",
        "bitrate": "64k",
        "contentSize": "72969149",
        "contentUrl": "https://devmode.fm/transcoder/devmode0041_64kbps_22050_1c.mp3",
        "duration": "4560.535510",
        "embedUrl": "https://devmode.fm/player-card/webpack-inside-out-with-sean-larkin",
        "encodingFormat": "audio/mpeg",
        "productionCompany": {
            "@id": "https://nystudio107.com/#creator"
        },
        "uploadDate": "Mon, 27 May 2019 09:05:00 -0400"
    },
    "author": {
        "@id": "https://devmode.fm/#identity"
    },
    "copyrightHolder": {
        "@id": "https://devmode.fm/#identity"
    },
    "copyrightYear": "2019",
    "creator": {
        "@id": "https://devmode.fm/#creator"
    },
    "dateModified": "2019-05-27T09:10:22-04:00",
    "datePublished": "2019-05-27T09:05:00-04:00",
    "description": "In this episode, we talk to webpack core maintainer Sean Larkin about what webpack is, who it's intended for, and where it's going in the future!\n\nWe discuss the serendipitous history of how Sean came to be a webpack core maintainer, and how his job at Microsoft came about as a result of it.\n\nJoin by guest host Jake Dohm, we then go on to discuss a whole lot of gritty technical detail of how webpack works, go through the various terminology, talk about Web Assembly, and what the future holds for webpack 5 and beyond.\n\nSean also drops some truth-bombs about CSS being flawed, and browser makers conspiring to kill off webpack. Tune in for the good stuff!",
    "director": [
        {
            "@type": "Person",
            "affiliation": "nystudio107",
            "description": "Andrew Welch has been in the tech business since he was 15 years old. After a stint running a software company for a couple of decades, he's now immersing himself in doing consulting to help businesses use technology effectively.",
            "familyName": "Welch",
            "givenName": "Andrew",
            "jobTitle": "Demolitions Expert",
            "name": "Andrew Welch",
            "sameAs": [
                "https://twitter.com/nystudio107",
                "https://github.com/nystudio107"
            ],
            "url": "https://nystudio107.com"
        },
        {
            "@type": "Person",
            "affiliation": "Mildly Geeky",
            "description": "Patrick Harrington has been obsessed with the web ever since picking up a copy of HTML for Dummies at age 13\. After working for larger Boston-area agencies, he started his own company in 2011 and specializes in Craft CMS.",
            "familyName": "Harrington",
            "givenName": "Patrick",
            "jobTitle": "President, Founder",
            "name": "Patrick Harrington",
            "sameAs": [
                "https://twitter.com/p_harrington83",
                "https://github.com/mildlygeeky"
            ],
            "url": "https://mildlygeeky.com"
        }
    ],
    "episodeNumber": 41,
    "genre": "Technology",
    "headline": "webpack inside & out with Sean Larkin",
    "image": {
        "@type": "ImageObject",
        "url": "https://devmode.fm/assets/site/_1200x630_crop_center-center_82_none/devmode_light-itunes.jpg"
    },
    "inLanguage": "en-us",
    "isAccessibleForFree": true,
    "mainEntityOfPage": "https://devmode.fm/episodes/webpack-inside-out-with-sean-larkin",
    "mentions": [
        {
            "@type": "Thing",
            "name": "webpack concepts",
            "url": "https://webpack.js.org/concepts/"
        },
        {
            "@type": "Thing",
            "name": "webpack in Wikipedie",
            "url": "https://en.wikipedia.org/wiki/Webpack"
        },
        {
            "@type": "Thing",
            "name": "Webpack 4 Fundamentals videos",
            "url": "https://frontendmasters.com/courses/webpack-fundamentals/"
        },
        {
            "@type": "Thing",
            "name": "Webpack — The Confusing Parts",
            "url": "https://medium.com/@rajaraodv/webpack-the-confusing-parts-58712f8fcad9"
        },
        {
            "@type": "Thing",
            "name": "An Annotated webpack 4 Config for Frontend Web Development",
            "url": "https://nystudio107.com/blog/an-annotated-webpack-4-config-for-frontend-web-development"
        },
        {
            "@type": "Thing",
            "name": "A tale of Webpack 4 and how to finally configure it in the right way.",
            "url": "https://hackernoon.com/a-tale-of-webpack-4-and-how-to-finally-configure-it-in-the-right-way-4e94c8e7e5c1"
        }
    ],
    "name": "webpack inside & out with Sean Larkin",
    "productionCompany": {
        "@id": "https://nystudio107.com/#creator"
    },
    "publisher": {
        "@id": "https://devmode.fm/#creator"
    },
    "url": "https://devmode.fm/episodes/webpack-inside-out-with-sean-larkin"
}
</script> 
```

我将跳过一些附加的属性，把重点放在更有趣的部分。

`addPersonArray()`宏用于添加节目中的`director`，也可重复用于添加在剧集中扮演我的`actor`的角色。添加这种关系信息真的有助于谷歌找出人们和他们在网络上的作品之间的联系。

然后，我们还在属性`audio`中添加了一个[音频对象](https://schema.org/AudioObject) JSON-LD 对象，它表示剧集音频本身，以及关于音频的其他各种关联元信息。

最后，我们在网页上显示的所有“显示链接”都通过`addMentionsArray()`宏添加到了`mentions`属性中，所以我们在节目中看到的每件事都被链接到了。

最后，我们添加了一个新的 [RadioSeries](https://schema.org/RadioSeries) JSON-LD 结构化数据模式:

```
 <script type="application/ld+json">
{
    "@context": "http://schema.org",
    "@id": "https://devmode.fm/#radio-series",
    "@type": "RadioSeries",
    "actor": [
        {
            "@type": "Person",
            "affiliation": "nystudio107",
            "description": "Andrew Welch has been in the tech business since he was 15 years old. After a stint running a software company for a couple of decades, he's now immersing himself in doing consulting to help businesses use technology effectively.",
            "familyName": "Welch",
            "givenName": "Andrew",
            "jobTitle": "Demolitions Expert",
            "name": "Andrew Welch",
            "sameAs": [
                "https://twitter.com/nystudio107",
                "https://github.com/nystudio107"
            ],
            "url": "https://nystudio107.com"
        },
        {
            "@type": "Person",
            "affiliation": "Hypatia Industries",
            "description": "Earl Johnston is a freelance web developer operating under the Hypatia Industries banner. He specializes in high fives and bourbon.",
            "familyName": "Johnston",
            "givenName": "Earl",
            "jobTitle": "Professor Doctor",
            "name": "Earl Johnston",
            "url": "http://www.hypatia-industries.com"
        },
        {
            "@type": "Person",
            "affiliation": "A Bright Color",
            "description": "Lauren is a developer with a background in design, hailing from Toledo, Ohio. Her focus: make understanding code scalability easier, to help create performant and accessible experiences. Outside of work: playing with her MIDI controller, finding the best shows, and cycling around the streets of Neukölln.",
            "familyName": "Dorman",
            "givenName": "Lauren",
            "jobTitle": "Development",
            "name": "Lauren Dorman",
            "sameAs": [
                "https://twitter.com/laurendorman?lang=en",
                "https://github.com/laurendorman"
            ],
            "url": "https://laurendorman.io/about/"
        },
        {
            "@type": "Person",
            "affiliation": "MDD",
            "description": "Jonathan is principal/CEO of MDD, a small web agency based in Atlanta, Georgia. He has been developing for the web since 2005 and Craft CMS since 2013\. He used to have free time and cool hobbies until his second child arrived on December 6, 2017\. He loves his work and his family.",
            "familyName": "Melville",
            "givenName": "Jonathan",
            "jobTitle": "Jonathan Melville",
            "name": "Jonathan Melville",
            "sameAs": [
                "https://twitter.com/jonmelville",
                "https://github.com/jonathanmelville"
            ],
            "url": "https://codemdd.io"
        },
        {
            "@type": "Person",
            "affiliation": "Newlevant",
            "description": "Marion has been writing software for over 40 years. She may not have seen it all, but she's seen many things, most of them more than once. Marion is the queen of twig macros, and the second most famous Newlevant.",
            "familyName": "Newlevant",
            "givenName": "Marion",
            "jobTitle": "Marion Newlevant",
            "name": "Marion Newlevant",
            "sameAs": [
                "https://twitter.com/marionnewlevant",
                "https://github.com/marionnewlevant/"
            ],
            "url": "http://marion.newlevant.com"
        },
        {
            "@type": "Person",
            "affiliation": "Working Concept",
            "description": "Matt's a web developer with a design background that's been building websites for the past decade. He loves learning, connecting things, and helping businesses solve multi-faceted, internet-shaped problems.",
            "familyName": "Stein",
            "givenName": "Matt",
            "jobTitle": "President/Designer/Developer",
            "name": "Matt Stein",
            "sameAs": [
                "https://twitter.com/mattrambles",
                "https://github.com/mattstein"
            ],
            "url": "https://workingconcept.com"
        },
        {
            "@type": "Person",
            "affiliation": "Build For Humans",
            "description": "Michael runs a small dev team in Texas. Code is his happy place. He wants to be a teacher when he grows up. When not writing code, he cooks with friends, travels all over the place, sings in the shower, and works out with the circus.",
            "familyName": "Rog",
            "givenName": "Michael",
            "jobTitle": "Michael Rog",
            "name": "Michael Rog",
            "sameAs": [
                "https://twitter.com/michaelrog",
                "https://github.com/TopShelfCraft"
            ],
            "url": "https://michaelrog.com"
        },
        {
            "@type": "Person",
            "affiliation": "Mildly Geeky",
            "description": "Patrick Harrington has been obsessed with the web ever since picking up a copy of HTML for Dummies at age 13\. After working for larger Boston-area agencies, he started his own company in 2011 and specializes in Craft CMS.",
            "familyName": "Harrington",
            "givenName": "Patrick",
            "jobTitle": "President, Founder",
            "name": "Patrick Harrington",
            "sameAs": [
                "https://twitter.com/p_harrington83",
                "https://github.com/mildlygeeky"
            ],
            "url": "https://mildlygeeky.com"
        }
    ],
    "author": {
        "@id": "https://devmode.fm/#identity"
    },
    "copyrightHolder": {
        "@id": "https://devmode.fm/#identity"
    },
    "creator": {
        "@id": "https://nystudio107.com/#creator"
    },
    "description": "devMode.fm is a bi-weekly podcast dedicated to the tools, techniques, and technologies used in modern web development. Each episode, we have a cadre of hosts discussing the latest hotness, pet peeves, and technologies we use every day. We all come from a Craft CMS background, but we'll be focusing on other cool frontend development technologies as well.",
    "director": [
        {
            "@type": "Person",
            "affiliation": "nystudio107",
            "description": "Andrew Welch has been in the tech business since he was 15 years old. After a stint running a software company for a couple of decades, he's now immersing himself in doing consulting to help businesses use technology effectively.",
            "familyName": "Welch",
            "givenName": "Andrew",
            "jobTitle": "Demolitions Expert",
            "name": "Andrew Welch",
            "sameAs": [
                "https://twitter.com/nystudio107",
                "https://github.com/nystudio107"
            ],
            "url": "https://nystudio107.com"
        },
        {
            "@type": "Person",
            "affiliation": "Mildly Geeky",
            "description": "Patrick Harrington has been obsessed with the web ever since picking up a copy of HTML for Dummies at age 13\. After working for larger Boston-area agencies, he started his own company in 2011 and specializes in Craft CMS.",
            "familyName": "Harrington",
            "givenName": "Patrick",
            "jobTitle": "President, Founder",
            "name": "Patrick Harrington",
            "sameAs": [
                "https://twitter.com/p_harrington83",
                "https://github.com/mildlygeeky"
            ],
            "url": "https://mildlygeeky.com"
        }
    ],
    "image": {
        "@type": "ImageObject",
        "height": 1800,
        "url": "http://devmode.test/assets/site/devmode_light-itunes.jpg",
        "width": 1800
    },
    "inLanguage": "en-us",
    "mainEntityOfPage": "http://devmode.test/",
    "name": "devMode.fm",
    "url": "https://devmode.fm/"
}
</script> 
```

这个 [RadioSeries](https://schema.org/RadioSeries) 模式根据剧集所属的系列给出了单个剧集的文本，并且还重用了`addPersonArray()`宏来添加所有曾经作为主持人参加过该节目的人，作为 [RadioSeries](https://schema.org/RadioSeries) 的一部分。

其他可以做的事情:

*   电话号码可以被包括在[组织](https://schema.org/Organization) & [每个子](https://schema.org/Person)模式中
*   将客人的信息添加到`actor`属性中(该数据目前不在 CMS 中)
*   可以添加一个[站点链接搜索框](https://developers.google.com/search/docs/data-types/sitelinks-searchbox)

**Google 结构化数据测试工具**链接→ [开发模式。fm 剧集页面](https://search.google.com/structured-data/testing-tool#url=https%3A%2F%2Fdevmode.fm%2Fepisodes%2Fwebpack-inside-out-with-sean-larkin)

## 鳍。

这些例子展示了 JSON-LD 结构化数据的几个实际应用，但即使在这里，还可以做得更多！

[![Thats all folks](img/7c86cc7c0eb47320a4ca2336502ae465.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iE2DJ0eq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/thats-all-folks.jpg)

我认为有必要先得到垂手可得的果实，并解决谷歌自己可能难以阻止我的问题。

希望这些关于 JSON-LD 结构化数据在野外实际应用的示例对您有所帮助。

通过实际选择“最适合”您的网站的模式和属性，创建您自己的定制 JSON-LD 结构化数据，对 Google 使用的模式给予一点额外的特别关注。

享受您的结构化数据！

## 进一步阅读

如果你想获得新文章的通知，请在 Twitter 上关注[纽约时报 107](https://twitter.com/nystudio107) 。

版权所有 2020 nystudio107。由 nystudio107 设计