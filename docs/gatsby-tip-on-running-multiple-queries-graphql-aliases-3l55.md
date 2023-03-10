# Gatsby 关于运行多个查询的提示(GraphQL 别名)

> 原文：<https://dev.to/malroun1/gatsby-tip-on-running-multiple-queries-graphql-aliases-3l55>

假设您想要在一个页面中获取基于参数或条件的特定数据，而这些数据不能使用一个查询来运行，因为您不能使用不同的条件或参数来查询相同的字段。一种方法是使用 [GraphQL 别名](https://graphql.org/learn/queries/#aliases)，您可以使用它将返回的数据集重命名为您想要的任何名称。

## 举例

```
export const query = graphql`
  query {
    post: allMarkdownRemark(
      limit: 3
      sort: { order: DESC, fields: [frontmatter___date] }
      filter: { frontmatter: { type: { ne: "portfolio" } } }
    ) {
      edges {
        node {
          timeToRead
          frontmatter {
            title
            path
            date(formatString: "DD MMMM YYYY")
            summary
            images
            tags
            type
          }
        }
      }
    }
    portfolio: allMarkdownRemark(
      sort: { order: DESC, fields: [frontmatter___date] }
      filter: { frontmatter: { type: { eq: "portfolio" } } }
    ) {
      edges {
        node {
          frontmatter {
            title
            path
            images
            tags
            type
          }
        }
      }
    }
    siteMetaData: site {
      siteMetadata {
        title
      }
    }
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

看上面的例子，我们可以看到我做的查询将返回多个数据集，通过给它一个别名，这允许我用不同的参数和条件运行多个查询，以获得我需要的特定数据对象，正如你在屏幕截图中看到的。
[![graphql alias](img/beeac547926675a94001fad69f71aa62.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--HJyIYG-i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/./https://thepracticaldev.s3.amazonaws.com/i/ftzlcvbjfqixkd1c0ouo.jpg)