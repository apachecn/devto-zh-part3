# 向 Gridsome 添加 Twitter 卡片

> 原文：<https://dev.to/therealdanvega/adding-twitter-cards-to-gridsome-hik>

在之前的一篇文章中，我向你展示了如何在你的博客中添加 Twitter 卡片。如果你还没有浏览那篇文章，我建议你在阅读这篇文章之前先浏览一下。

在这篇文章中，我将向你展示如何在你的 Gridsome 博客中添加 Twitter 卡片。这不仅适用于 Twitter 卡片，更是一个为你的博客文章添加元标签的通用示例。

## grid some 中的博文

我将通过这篇文章做出一个假设，您至少已经使用过 Gridsome。如果你正在寻找 Gridsome 的初学者指南，我已经有了，但是请在 Twitter 上大声告诉我，让我知道你对此感兴趣。

在我的`gridsome.config.js`中，我定义了@gridsome/source-filesystem 插件，这里的关键部分是类型名。这是 GraphQL 类型和模板名称。src/templates 中的. vue 文件必须与 typeName 相匹配才能拥有模板。

```
plugins: [{
      use: '@gridsome/source-filesystem',
      options: {
        path: 'blog/**/*.md',
        typeName: 'Post',
        route: '/blog/:year/:month/:day/:slug', 
```

如果我们查看帖子模板，您首先会看到的是每个博客帖子的标记。同样，这是一个单独的博客帖子，有一个单独的模板用于列出帖子的页面。

```
<Layout>
  <div class="article content">
    <h1 class="title is-2 article-title">{{ $page.post.title }}</h1>
    <small class="about">{{ formatCreatedOn }} • ☕️ {{ $page.post.timeToRead }} min read</small>
    <g-image v-if="$page.post.cover" :src="$page.post.cover" class="cover"/>
    <article v-html="$page.post.content" class="article"/>
    <convert-kit uid="44cc02ed05" script="https://f.convertkit.com/44cc02ed05/38739557e4.js"></convert-kit>
  </div>
  <bulma-tag :tags="$page.post.tags"/>
</Layout> 
```

这是这篇博文的全部内容，那么它是从哪里来的呢？如果您查看标记下方，有一个 GraphQL 页面查询，它根据路径提取这篇文章的信息。

```
<page-query>
query Post ($path: String!) {
  post: post (path: $path) {
    title
    content
    timeToRead
    cover
    slug
    date
    excerpt
    tags {
      id
      title
      path
    }
  }
}
</page-query> 
```

既然你们都知道了，让我们开始为每篇博客文章创建一张 Twitter 卡片。

## Gridsome Meta 标签

如果你[读过我之前的文章](https://www.danvega.dev/blog/2019/02/18/twitter-cards-meta-tags)，你就会知道创建一张 twitter 卡片并不难。我们需要做的就是添加一些最终看起来像这样的 meta 标签。

```
<head>
  <meta name="twitter:card" content="summary_large_image" />
  <meta name="twitter:description" content="How to create your first npm package and publish it."/>
  <meta name="twitter:title" content="Creating your first npm package" />
  <meta name="twitter:site" content="@therealdanvega" />
  <meta name="twitter:image" content="https://www.danvega.me/assets/static/npm_cover.bd64798.eced3da.png"/>
  <meta name="twitter:creator" content="@therealdanvega" />
</head> 
```

这里的挑战是，您希望每篇博客文章生成特定于该文章的标签。幸运的是，Gridsome 为我们提供了一种在组件中填充元数据的简单方法。您需要做的就是将`metaInfo`对象添加到组件脚本部分。

```
<script>
export default {
  name: 'About',
  metaInfo: {
    title: 'About us',
    meta: [
      { name: 'author', content: 'John Doe' }
    ],
    link: [
      { rel: 'stylesheet', href: '/css/index.css' },
    ]
    // etc...
  }
}
</script> 
```

如果你有兴趣了解它是如何工作的，Gridsome 使用了 [Vue Meta](https://github.com/nuxt/vue-meta) 。

在 metaInfo 对象中，您可以定义以下属性。

| 财产 | 描述 |
| --- | --- |
| 风格 | 添加样式标签 |
| 脚本 | 添加脚本标签 |
| 自指的 | 添加元标签 |
| 标题 | 更改标题文本 |
| 标题模板 | 动态标题文本 |
| 环 | 添加链接标签 |

## Gridsome Meta 标签

如果您注意到在`metaInfo`对象中您可以添加的属性之一是`meta`。这将允许我们使用以下格式创建一个元标签:

```
metaInfo: {
  return {
    title: this.$page.post.title,
    meta: [
      { name: 'META_NAME_HERE' content: 'META_CONTENT_HERE' }
    ],
  };
}, 
```

这样，您就拥有了创建元标签所需的一切。在下面的代码片段中，我创建了一个描述元标签和我的 Twitter 卡片需要的每个标签。对于每一个标签，我都用我们发布的帖子中的数据填充内容。我添加的脚本属性是 Twitter API，我们需要包含它以使卡片工作。

```
metaInfo() {
    return {
      title: this.$page.post.title,
      meta: [
        { name: "description", content: this.$page.post.excerpt },
        // twitter-card: https://cards-dev.twitter.com/validator
        { name: "twitter:card", content: "summary_large_image" },
        { name: "twitter:description", content: this.$page.post.excerpt },
        { name: "twitter:title", content: this.$page.post.title },
        { name: "twitter:site", content: "@therealdanvega" },
        { name: "twitter:image", content: this.getCoverImage },
        { name: "twitter:creator", content: "@therealdanvega" }
      ],
      script: [{ src: "https://platform.twitter.com/widgets.js", async: true }]
    };
  }, 
```

关于这些元标签的更多信息，你可以查看一下 Twitter 卡片文档。这将在你的头脑中产生类似这样的 html

```
 <head>
    How to add Twitter Card Meta Tags to your Blog - Dan Vega
    <meta data-vue-tag="true" charset="utf-8" />
    <meta data-vue-tag="true" name="generator" content="Gridsome v0.5.0" />
    <meta data-vue-tag="true" data-key="viewport" name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover" />
    <meta data-vue-tag="true" data-key="description" name="description" content="Person blog of Dan Vega" />
    <meta data-vue-tag="true" data-key="format-detection" name="format-detection" content="telephone=no" />
    <meta data-vue-tag="true" name="description" content="In this tutorial you will learn what a Twitter Card is along with step by step instructions how to add them to your blog and validate that they are working." />
    <meta data-vue-tag="true" name="twitter:card" content="summary_large_image" />
    <meta data-vue-tag="true" name="twitter:description" content="In this tutorial you will learn what a Twitter Card is along with step by step instructions how to add them to your blog and validate that they are working." />
    <meta data-vue-tag="true" name="twitter:title" content="How to add Twitter Card Meta Tags to your Blog" />
    <meta data-vue-tag="true" name="twitter:site" content="@therealdanvega" />
    <meta data-vue-tag="true" name="twitter:image" content="https://www.danvega.dev/assets/static/twitter-cards.bd64798.89214ec.png" />
    <meta data-vue-tag="true" name="twitter:creator" content="@therealdanvega" />
    <script data-vue-tag="true" src="https://platform.twitter.com/widgets.js" async="true"></script>
  </head> 
```

这是一张卡片可能的样子

[![Gridsome Twitter Card](img/1a501f41af91f23678285b53a0a0884b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--da8LlV1m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0r27deelbja73ppd3jhv.png)

## 结论

Twitter 卡片为在 Twitter 上分享你的文章增添了一种非常好的感觉。有图片的推文会比没有图片的推文获得更多的展示和点击，所以你还在等什么？如果你有兴趣看我的整个 Gridsome 博客的代码，你可以在这里查看。一如既往...

快乐编码

丹

*这篇文章最初是在 https://www.danvega.dev/blog 的[发表在我的博客上的。如果你觉得这篇文章有趣，请考虑](https://www.danvega.dev/blog)[订阅我的时事通讯](https://www.danvega.dev/signup/)或者在 [Twitter](http://twitter.com/therealdanvega) 上关注我。*