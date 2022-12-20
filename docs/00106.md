# 在 Gatsby 中使用 GraphQL 查询分离页面和博客文章

> 原文：<https://dev.to/p0oker/using-graphql-queries-to-separate-pages-and-blog-posts-in-gatsby-1ke2>

当我和盖茨比开始我的博客时，我的一个主要问题是从博客文章列表中过滤掉我的“关于我”或任何其他页面。如果您的静态页面不使用 Markdown 文件，这个任务就像为每个页面创建单独的 React 组件一样简单。然而，如果像我一样，你想对你的静态页面使用 Markdown，你会遇到在博客文章列表中看到你的页面的问题！

开始时，我用我的 JavaScript 技能过滤掉结果，但我对此并不满意。最近，我学习了一种更好的使用 GraphQL 的方法，这就是我在这篇文章中描述的。

# 利用锋物质

首先，我们需要一种机制来标记我们用于页面的 markdown 文件。front matter(markdown 文件顶部指定的字段)是将一些帖子标记为页面的好地方。我是这样做的:

```
---
title: "My  page  title"
date: 05/06/2019
page: true
--- 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，在这个 markdown 文件中，我将页面属性(可以是其他任何东西)标记为`true`。现在，在我的 index.js 中，我列出了我所有的博客文章，我有可能过滤掉它们的`page`属性为假的 markdown 文件。

# 从博客文章中排除页面

此时，我已经在 frontmatter 中准备好了我的页面字段，从博客文章结果中删除页面就像在 GraphQL 查询中增加一行一样简单:

```
// index.js (or wherever you are showing the list of your blog posts)
export const pageQuery = graphql`
  query {
    site {
      siteMetadata {
        title
      }
    }
    allMarkdownRemark(
      sort: { fields: [frontmatter___date], order: DESC }
      filter: { frontmatter: { page: {ne: true}}}
    ) {
      edges {
        node {
          excerpt
          fields {
            slug
          }
          frontmatter {
            date(formatString: "MMMM DD, YYYY")
            title
          }
        }
      }
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

注意，我在 allMarkdownRemark 查询中使用了`filter`参数。`ne`是保留关键字，代表“不相等”。如果您不确定过滤可用的选项，您可以随时使用 graqhiQL(可在 **localhost:8000/__graphql** 访问)来检查它们。

# 查询正确的页面

现在我们需要在/pages 目录中创建一个新组件，并正确查询页面。然而，对于每一个页面，我们可能想要创建一个新的组件，但是没有办法从 frontmatter 中找出哪个页面属于哪个组件。

为了根据 markdown 文件的名称简单地服务于组件中的特定页面，我们可以简单地使用 GraphQL 数据中的 slug 字段:

```
export const pageQuery = graphql`
query {
  site {
    siteMetadata {
      title
      author
    }
  }
  markdownRemark(
    fields: { slug: { eq: "/about-me/" }}
  ) {
    excerpt
    html
    frontmatter {
      title
      date
    }
  }
}
` 
```

Enter fullscreen mode Exit fullscreen mode

通过这个查询，您可以在执行这个查询的同一个组件上访问您在`this.props.data.markdownRemark`中的文章的所有数据！

## 结论

这种解决方案适用于具有不同设计的有限页面的用例。如果你需要动态的页面数量，你不希望为每个页面创建新的组件，你需要在你的`gatsby-node.js`文件的`createPage`函数中做一些改变，以不同的方式呈现页面组件。