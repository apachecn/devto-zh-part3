# 与盖茨比一起创建多作者博客

> 原文：<https://dev.to/wgao19/building-a-multi-author-blog-with-gatsby-3607>

封面图片:[古董打字机](https://unsplash.com/photos/0gkw_9fy0eQ)作者[帕特里克·福尔](https://unsplash.com/@patrickian4)

# 与盖茨比一起打造多作者博客

注意这是一篇非常简单的文章，我列出了创建多作者博客的所有步骤。你可能不想读这个，因为它可能会很无聊。这不是我在创意方面考虑很多的那种帖子。

如果你不想读这个帖子，但仍然想要这个功能，你可以看看我在这个帖子之后创建的[这个启动器](https://github.com/wgao19/gatsby-starter-multiple-author)，Netlify 上的[演示。](https://gatsby-starter-multiple-author.netlify.com/)

[![](img/a3d7310888ceacb409e3545995c04718.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eprh-347--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a8jrpywlgqsr2vs4feg0.png)

* * *

所以最近我想创建一个多作者的博客。除了标记每个帖子的作者之外，我还希望有一个地方显示作者的详细信息，并能够在该作者的页面上查询每个作者的帖子。

起初，在我看来这是一个相当普遍的要求。“相当普遍”意味着我希望已经有一个启动器或插件可以做到这一点。一些谷歌搜索并没有提示预期的结果。相反，我看到人们在盖茨比的 GitHub repo 中提问和请求功能。这不是一个好兆头🙈我记得 Gatsby 在`gatsby-config`中接受了一个`mapping`，这允许我们添加到作者细节的映射。所以在此基础上增加几页应该是直接的，不是吗？

为了验证我天真的想法是否可行，我决定亲自尝试一下，看看效果如何。

剧透:这个功能是可能的，但也不是没有问题。我想这是有趣的部分。

## 实现

在本帖中，我们将讨论以下主题:

*   地图`author`从前面的事到 YAML 指定的一个作者的细节
*   创建所有作者页面
*   创建作者详细信息页面

### 映射帖子前面的`author`字段

所以我们想在作者 id 和作者详细信息之间创建一个映射。Gatsby 支持在节点类型之间创建映射。我们可以在`gatsby-config.js`中指定一个名为`mapping`的可选字段，这里的[表示](https://www.gatsbyjs.org/docs/gatsby-config/#mapping-node-types)。它接受在单个文件或多个文件中定义的映射，并在文件名上有一个映射方案。它还支持多种文件类型，如 YAML，JSON，markdown 等。

我们将遵循一个常见的惯例，用 YAML 指定多作者信息。

为了解析 YAML 文件，在你的包中添加 [`gatsby-transformer-yaml`](https://www.gatsbyjs.org/packages/gatsby-transformer-yaml/?=transformer-yaml) 。

```
$ yarn add gatsby-transformer-yaml 
```

而在插件

```
// gatsby-config.js
module.exports = {
  plugins: [`gatsby-transformer-yaml`],
} 
```

请注意，我们稍后创建的文件需要位于由文件源插件获取的目录中。如果您打算创建一个单独的目录，比如说`mappings`，您可能需要:

```
// gatsby-config.js
module.exports = {
  plugins: [
    `gatsby-transformer-yaml`,
    {
      resolve: 'gatsby-source-filesystem',
      options: {
        name: 'mappings',
        path: `${__dirname}/mappings/`,
      },
    },
  ],
} 
```

然后，为了告诉 Gatsby 将作者映射到作者细节，在您的配置中将`mapping`指定为一个字段，比如:

```
// gatsby-config.js
module.exports = {
  mapping: {
    'MarkdownRemark.frontmatter.author': `AuthorYaml`,
  },
  plugins: [`gatsby-transformer-yaml`],
} 
```

接下来，创建包含作者详细信息的`author.yaml`文件。请注意，该文件需要由`gatsby-source-filesystem`提取。考虑把它放在你的博客目录的根目录下。对于我正在使用的`gatsby-advanced-starter`，我把它放在目录`/content`下。

```
# content/author.yaml

- id: Curious Cat
  bio: Very curious about this world and blogging whenever learning something new
  twitter: 'nonexistencecuriouscat'

- id: Wei Gao
  bio: First curious cat at Gatsby Curious Community. Blogs at dev.wgao19.cc.
  twitter: 'wgao19' 
```

设置了上面的映射后，Gatsby 现在会将您在我们的帖子的`frontmatter`中指定的`author`字段视为作者的 id。它会将它映射到`author.yaml`文件中该作者的实际细节。

现在，`frontmatter`中的`author`字段不再是字符串，而是我们的作者映射中指定的形状。要获取文章中的作者信息，需要时更新 GraphQL 查询。再次使用我正在使用的`gatsby-advanced-starter`，这将归结为:

```
// templates/post.jsx

export const pageQuery = graphql`
  query BlogPostBySlug($slug: String!) {
    markdownRemark(fields: { slug: { eq: $slug } }) {
      html
      timeToRead
      excerpt
      frontmatter {
        title
        cover
        date
        category
        tags
-       author +       author {
+         id
+         bio
+         twitter
+       }
      }
      fields {
        nextTitle
        nextSlug
        prevTitle
        prevSlug
        slug
        date
      }
    }
  }
`; 
```

### 创建作者列表页面

我们需要创建作者页面。在`gatsby-node.js`中，让我们放入 authors 页面的模板，并创建页面。在页面创建过程中，我们不需要传递任何东西，我们可以稍后查询页面中所有作者的信息。

```
// gatsby-node.js

exports.createPages = ({ graphql, actions }) => {
  const { createPage } = actions;

  return new Promise((resolve, reject) => {
    const postPage = path.resolve("src/templates/post.jsx");
    const tagPage = path.resolve("src/templates/tag.jsx");
    const categoryPage = path.resolve("src/templates/category.jsx");
+   const authorsPage = path.resolve("src/templates/Authors/index.jsx");
    resolve(
      graphql(
        `
          {
            allMarkdownRemark {
              edges {
                node {
                  frontmatter {
                    tags
                    category
                  }
                  fields {
                    slug
                  }
                }
              }
            }
          }
        `
      ).then(result => {
        // ... other page creations

+       createPage({
+         path: `/authors/`,
+         component: authorsPage
+       }); 
      })
    );
  });
} 
```

在这个页面中，我们可以使用`allAuthorYaml` :
查询所有作者

```
// src/templates/Authors/index.jsx

import React from 'react'

export default ({
  data: {
    allAuthorYaml: { edges: authorNodes },
  },
}) => (
  <div>
    {authorNodes.map(({ node: author }, index) => (
      <div key={`author-${author.id}`}>{author.id}</div>
    ))}
  </div>
)

export const pageQuery = graphql`
  query AuthorsQuery {
    allAuthorYaml {
      edges {
        node {
          id
          bio
          twitter
        }
      }
    }
  }
` 
```

### 创建作者页面

作者的页面显示作者的详细信息，并列出该作者的所有文章。所以我们的页面查询会包括两部分，一部分查询作者信息，另一部分查询帖子。

让我先概述一下我们需要做的几件事:

*   在`onCreateNode`期间为每个帖子添加一个字段`authorId`，在`gatsby-node.js` * *内，参见下面的注释，为什么我们需要这个*
*   为每个作者创建页面，也在`createPages`期间的`gatsby-node.js`实现。我们在上下文中传递作者的 id，供以后查询当前作者的帖子时使用。
*   在作者页面中，查询:
    *   作者的详细信息
    *   来自该作者的所有帖子

*有一个小问题，[目前我们无法过滤带有映射模式](https://github.com/gatsbyjs/gatsby/issues/7251#issuecomment-429651084)的帖子。所以看起来像
的普通滤镜

```
allMarkdownRemark(
  filter: {
    fields: { author: { id: { eq: $authorId } } }
  }) {
    edges {}
} 
```

不会起作用。

我肯定周围有作品。我最终想到的是为每篇文章创建一个字段`authorId`，并用创建的字段进行过滤。我对 GraphQL 不是很了解，所以如果有标准的或者更好的方法，请告诉我。

*创建`authorId`字段来发布节点*T3】

```
// gatsby-node.js

exports.onCreateNode = ({ node, actions, getNode }) => {
  // ...

  if (Object.prototype.hasOwnProperty.call(node.frontmatter, 'author')) {
    createNodeField({
      node,
      name: 'authorId',
      value: node.frontmatter.author,
    })
  }
} 
```

*查询`createPages`中的`authorId`查询*

一旦我们创建了这个字段，它就会出现在我们查询的`field`模式中。我们还可以使用这个字段来输入作者页面的上下文，稍后将使用这些页面来查询作者的帖子:

```
// gatsby-node.js

// the query for createPages:
graphql(
  `
    {
      allMarkdownRemark {
        edges {
          node {
            frontmatter {
              tags
              category
            }
            fields {
              slug
+             authorId
            }
          }
        }
      }
    }
  ` 
```

*创建作者页面*

现在我们可以创建作者的页面

```
// gatsby-node.js

// resolves from the query from 👆
const authorSet = new Set();
result.data.allMarkdownRemark.edges.forEach(edge => {
  if (edge.node.fields.authorId) {
    authorSet.add(edge.node.fields.authorId);
  }
}

// create author's pages inside export.createPages:
const authorList = Array.from(authorSet);
authorList.forEach(author => {
  createPage({
    path: `/author/${_.kebabCase(author)}/`,
    component: authorPage,
    context: {
      authorId: author
    }
  });
}); 
```

*组件实现*

最后，我们可以实现前端组件:

*   查询作者详细信息以及该作者过滤的帖子
*   呈现数据

```
// templates/Author/index.jsx
import React from 'react'

export default ({
  data: {
    authorYaml: { id, bio, twitter },
    allMarkdownRemark: { edges: postNodes },
  },
}) => (
  <div>
    <div>
      <h2>{id}</h2>
      <a href={`https://twitter.com/${twitter}/`} target="_blank">
        {`@${twitter}`}
      </a>
      <p>
        <em>{bio}</em>
      </p>
    </div>
    <hr />
    <p>{`Posted by ${id}: `}</p>
    {postNodes.map(({ node: post }, idx) => (
      <div key={post.id}>
        <a href={post.fields.slug}>{post.frontmatter.title}</a>
      </div>
    ))}
  </div>
)

export const pageQuery = graphql`
  query PostsByAuthorId($authorId: String!) {
    allMarkdownRemark(filter: { fields: { authorId: { eq: $authorId } } }) {
      edges {
        node {
          id
          frontmatter {
            title
            author {
              id
            }
          }
          fields {
            authorId
            slug
          }
        }
      }
    }
    authorYaml(id: { eq: $authorId }) {
      id
      bio
      twitter
    }
  }
` 
```

如果你愿意，你可以在这个 [CodeSandbox](https://codesandbox.io/embed/github/wgao19/gatsby-curious-community/tree/multi-author-blog/?fontsize=14) 查看这个特性。

[https://codesandbox.io/embed/32624281w6](https://codesandbox.io/embed/32624281w6)

## 到下次🤞

最后，我们可以通过在`gatsby-config.js`中指定`mapping`来映射节点类型。该 API 旨在适应涉及将某个节点类型映射到另一个节点类型的一般用例。并且在它为映射接受的文件类型以及如何定义映射方面是灵活的。

但是，查询过滤还不支持映射节点类型😭通过为`authorId`复制一个字段来解决这个问题，我们稍后会使用这个字段进行过滤。

再一次，看看 Netlify 上的这个[盖茨比-首发-多作者](https://github.com/wgao19/gatsby-starter-multiple-author)、[演示😁。](https://gatsby-starter-multiple-author.netlify.com/)

[![](img/a3d7310888ceacb409e3545995c04718.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eprh-347--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a8jrpywlgqsr2vs4feg0.png)

## 参考文献

*   文件缺少安装步骤， [#13134](https://github.com/gatsbyjs/gatsby/issues/13134)
*   添加`mapping`文件， [#4007](https://github.com/gatsbyjs/gatsby/issues/4007)
*   当每个帖子有多个作者时，如何查询一个作者的所有帖子， [#7251](https://github.com/gatsbyjs/gatsby/issues/7251)
*   [关于映射的 Gatsby 配置文档](https://www.gatsbyjs.org/docs/gatsby-config/#mapping-node-types)
*   [盖茨比节点 API](https://www.gatsbyjs.org/docs/actions/#createPage) 上`createPage`
*   [T2`gatsby-transformer-yaml`](https://www.gatsbyjs.org/packages/gatsby-transformer-yaml/?=transformer-yaml)
*   盖茨比好奇社区 [CodeSandbox 仅针对多作者特性](https://codesandbox.io/embed/github/wgao19/gatsby-curious-community/tree/multi-author-blog/?fontsize=14)、 [GitHub](https://github.com/wgao19/gatsby-curious-community)
*   [在 Jekyll 中处理多个作者](https://www.codingpedia.org/ama/how-to-handle-multiple-authors-in-jekyll/)
*   [Pandoc 标题栏多作者支持](https://pandoc.org/MANUAL.html#extension-pandoc_title_block)
*   [YAML](https://en.wikipedia.org/wiki/YAML)