# 盖茨比和 WordPress:创建索引页面

> 原文：<https://dev.to/booyaa/gatsby-and-wordpress-creating-an-index-page-46k6>

*Billy Huynh 在[Unsplash](https://unsplash.com/photos/saS98jKhVjA)T3 拍摄的照片*

## 点菜点菜！让我们创建一个索引来列出我们的帖子

让我们访问[http://localhost::8000/_ _ _ graph QL](http://localhost::8000/___graphql)来启动内置的 GraphQL explorer 并粘贴以下查询。

```
{  allWordpressPost(sort:  {fields:  [date],  order:DESC}  )  {  totalCount  edges  {  node  {  title  excerpt  slug  date(formatString:  "Do MMMM")  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这里要注意的是如何对文章集合进行排序。如果你想知道哪些字段是可用的，浏览器提供了你已经加载的任何插件的文档，所以在 WordPress Post 的情况下，我们可以点击`allWordpressPost`的悬停对话框来探索这个。

让我们更新我们的索引页面(`src/pages/index.js`)来列出我们的 WordPress 文章。

首先，我们导入`graphql`并且我们可以删除我们导入的`Image`组件。

```
import { Link, graphql } from 'gatsby' 
```

Enter fullscreen mode Exit fullscreen mode

然后我们更新 IndexPage 定义，我们使用`map`函数将每个数组项转换成包含每个帖子信息的 HTML 片段。

```
const IndexPage = ({data}) => (
  <Layout>
    <SEO title="Home" keywords={[`gatsby`, `application`, `react`]} />
    <h1>Welcome to the Gatsby demo</h1>
    <h3>There are {data.allWordpressPost.totalCount} posts</h3>

    {data.allWordpressPost.edges.map(({ node }) => (
      <div key={node.id}>
        <Link to={node.slug}>
          <h4><span dangerouslySetInnerHTML={{ __html: node.title }}/> - {node.date}</h4>
        </Link>
        <div dangerouslySetInnerHTML={{ __html: node.excerpt }} />
      </div>
    ))}

  </Layout>
) 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们添加了我们在本文开始时在 GraphQL 浏览器中测试过的 pageQuery。

```
export const pageQuery = graphql`
  query {
    allWordpressPost(sort: { fields: [date], order: DESC }) {
      totalCount
      edges {
        node {
          id
          title
          excerpt
          slug
          date(formatString: "Do MMMM")
        }
      }
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

## 检查点:索引页重新启动

如果我们访问`http://localhost:8000`,我们会看到一个与我们在这个博客文章系列开始时不同的页面

[![Index](img/26764471e20abb191d1ba0a0642b8b0c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pubOd58A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://booyaa.wtf/img/gxw-index.png)

如果你卡住了，你可以看看下面的 Git hash: [`2249ea842a18e4da39c6e3abcf8eeabd78a17116`](https://github.com/booyaa/wordsby/commit/2249ea842a18e4da39c6e3abcf8eeabd78a17116)

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*