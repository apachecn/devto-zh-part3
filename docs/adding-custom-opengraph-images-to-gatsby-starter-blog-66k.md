# 向 Gatsby Starter 博客添加自定义 OpenGraph 图像

> 原文：<https://dev.to/juliang/adding-custom-opengraph-images-to-gatsby-starter-blog-66k>

我有一个基于[盖茨比博客](https://www.gatsbyjs.org/starters/gatsbyjs/gatsby-starter-blog/)的博客，没有任何修改。

## 问题

我希望我的帖子在社交媒体上分享时有一个漂亮的形象。

对生成的 HTML 进行简单的检查就会发现，Gatsby 博客呈现了(除了其他内容之外)以下[元标签](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta) :

```
<meta property="description" ... />
<meta property="og:title" ... />
<meta property="og:description" ... />
<meta property="og:type " ... />
<meta property="twitter:title" ... />
<meta property="twitter:description" ... /> 
```

Enter fullscreen mode Exit fullscreen mode

它不呈现`og:image`、`image`和`twitter:image`，这些都是使用 [Open Graph](http://ogp.me/) 在推特、脸书和其他网站上正确分享文章所需要的。

为了查看我们的博客文章链接在社交媒体上会是什么样子，我们可以使用 [Twitter 卡验证器](https://cards-dev.twitter.com/validator)、[脸书共享调试器](https://developers.facebook.com/tools/debug/)，或者我们可以简单地使用[打开图检查](https://opengraphcheck.com/)来读取打开图元数据。

[![Twitter Card Validator](img/56c85b93ed38809a6eda08f2b343100d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TagkLUJs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lddorkuvt2lgu9xoivhg.png)

## 要求

我们希望博客的主页有一个默认的(或备用的)图像，并可以选择指定一个自定义的图像(即使它不在文章的正文中)。

## 解

首先让我们在一篇博文的顶部(frontmatter)添加一个`ogimage`属性。

```
--------
 title: Hello!
 date: '2018-12-01'
+ogimage: './opengraph-default.png' -------- 
```

Enter fullscreen mode Exit fullscreen mode

重启`gatsby deveop`后，我们现在可以将`ogimage`添加到**src/templates/blog-post . js**中的 GraphQL 查询中。

```
 markdownRemark(fields: { slug: { eq: $slug } }) {
   id
   excerpt(pruneLength: 160)
   html
   frontmatter {
     title
     date(formatString: "MMMM DD, YYYY")
+    ogimage { 
+      childImageSharp {
+        fixed {
+          src
+        }
+      }
+    }
   }
 } 
```

Enter fullscreen mode Exit fullscreen mode

下一步是获取博客文章中的`ogimage`属性的文件路径，然后将其传递给`SEO`组件。还在**src/templates/blog-post . js**:

```
 class BlogPostTemplate extends React.Component {
   render() {
     const post = this.props.data.markdownRemark
     const siteTitle = this.props.data.site.siteMetadata.title
     const { previous, next } = this.props.pageContext
+    const { ogimage } = post.frontmatter
+    const ogImagePath = ogimage && ogimage.childImageSharp.fixed.src 
    return (
      <Layout location={this.props.location} title={siteTitle}>
        <SEO
          title={post.frontmatter.title}
          description={post.excerpt}
+          image={ogImagePath}
        /> 
```

Enter fullscreen mode Exit fullscreen mode

`SEO`组件(在 **src/components/seo.js** 中)现在需要接受一个`image`道具。

```
- function SEO({ description, lang, meta, keywords, title }) { + function SEO({ description, lang, meta, keywords, title, image }) { 
```

Enter fullscreen mode Exit fullscreen mode

如果`image`属性未定义，`SEO`将使用默认值，我们需要将其作为静态图像导入:

```
+ import defaultOpenGraphImage from '../../content/assets/opengraph-default.png' 
```

Enter fullscreen mode Exit fullscreen mode

...然后我们用它作为后备值。

```
 <StaticQuery
   query={detailsQuery}
   render={data => {
     const metaDescription =
       description || data.site.siteMetadata.description
+    const ogImageUrl = 
+      data.site.siteMetadata.siteUrl +
+      ( image || defaultOpenGraphImage )
     return (
       <Helmet 
```

Enter fullscreen mode Exit fullscreen mode

上面的`data.site.siteMetadata.siteUrl`表达式是用网站的 URL 作为文件路径的前缀所需要的，但是在我们将`siteUrl`添加到位于 **seo.js** :
底部的 GraphQL 查询之前，它是未定义的

```
const detailsQuery = graphql`
  query DefaultSEOQuery {
    site {
      siteMetadata {
         title
         description
         author
+        siteUrl
      }
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在元数据:
中使用`ogImageUrl`

```
 {
    property: `og:title`,
    content: title,
  },
  {
    property: `og:description`,
    content: metaDescription,
  },
+ {
+   property: `og:image`,
+   content: ogImageUrl,
+ },
+ {
+   property: `twitter:image`,
+   content: ogImageUrl,
+ },
+ {
+   property: `image`,
+   content: ogImageUrl,
+ }, 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

我们向博客帖子的 **frontmatter** 添加了一个属性。然后我们修改了 **blog-post.js** 中的 GraphQL 查询来读取属性，并将其传递给 **seo.js** 中的 SEO 组件。SEO 使用导入的图像作为后备，然后使用帖子的图像或后备图像呈现必要的 meta 标签。

您可以看到一个拉请求，其中包含我对我的博客所做的所有更改:[https://github.com/JulianG/juliang-blog/pull/3/files](https://github.com/JulianG/juliang-blog/pull/3/files)