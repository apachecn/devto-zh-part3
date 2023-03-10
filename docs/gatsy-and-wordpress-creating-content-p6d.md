# 盖茨比和 WordPress:创造内容

> 原文：<https://dev.to/booyaa/gatsy-and-wordpress-creating-content-p6d>

*Billy Huynh 在[Unsplash](https://unsplash.com/photos/saS98jKhVjA)T3 拍摄的照片*

## 从 WordPress 获取内容

现在我们已经验证了插件可以从我们的 WordPress 站点获取数据，让我们开始创建基于我们的文章和页面的静态内容！

让我们从一些简单的东西开始，将帖子拉进来，并在开发人员控制台中显示它们。这包括两个步骤:

*   创建一个 GraphQL 查询
*   根据正在运行的查询的结果采取行动(在这种情况下，我们将只显示查询的内容)

页面创建由 [createPages](https://www.gatsbyjs.org/docs/node-apis/#createPages) API 处理，承诺我们可以执行所需的步骤以编程方式创建页面。让我们编辑`gatsby-node.js`添加下面的代码。

```
exports.createPages = ({ graphql, actions }) => {
  const createWpPosts = new Promise((resolve, reject) => {
    const query = graphql(`
      {
        allWordpressPost {
          edges {
            node {
              id
              slug
            }
          }
        }
      }
    `)

    query.then(result => {
      console.log(JSON.stringify(result, null, 4))
      resolve()
    }) // query.then
  }) // createWpPosts

  return Promise.all([createWpPosts])
} // createPages 
```

Enter fullscreen mode Exit fullscreen mode

继续并重启`gatsby develop`，您应该在您的终端中看到以下内容:

```
success building schema — 0.920 s
⠁ {
    "data": {
        "allWordpressPost": {
            "edges": [
                {
                    "node": {
                        "id": "51ec1d4e-7b5f-54b8-b5ea-f36aea0c1d8f",
                        "slug": "a-long-form-post"
                    }
                }, 
```

Enter fullscreen mode Exit fullscreen mode

虽然这不是很令人兴奋，但它证实了我们已经能够从 WordPress 中提取我们的帖子。原来两个字段`id`和`slug`正是我们开始以编程方式创建页面所需要的。

## 基于 Wordpress 创建页面

让我们更新我们的代码，为了简洁起见，我们已经忽略了没有改变的代码块，如果你卡住了，在这个检查点的末尾有一个提交散列。

```
const path = require('path');

exports.createPages = ({ graphql, actions }) => {
    const { createPage } = actions

    const createPostPages = new Promise((resolve, reject) => {
      // omitted

      query.then(result => {
        if (result.errors) {
          console.error(results.errors)
          reject(result.error)
        }

        const postEdges = result.data.allWordpressPost.edges

        postEdges.forEach(edge => {
          createPage({
            path: `/${edge.node.slug}`,
            component: path.resolve(`./src/templates/post.js`),
            context: {
              id: edge.node.id,
            },
          })
        })

        resolve()
// omitted 
```

Enter fullscreen mode Exit fullscreen mode

在我们创建任何页面之前，让我们做一些错误处理，如果我们遇到任何错误，就提前返回。

接下来，我们将结果提取到一个 const 中，以便于阅读代码。

```
const postEdges = result.data.allWordpressPost.edges 
```

Enter fullscreen mode Exit fullscreen mode

您会记得这只是我们的 GraphQL 查询的简化。

```
{  allWordpressPost  {  edges  {  ...  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

然后我们调用 createPage API 迭代我们的结果，它需要三个参数:

*   WordPress 已经有了这些数据，所以与其自己计算，不如让我们重复使用。
*   `component`是 React 对标记单位的说法。设想一个控件工具箱，其中有各种可以添加到窗体中的组件。我们将在下一节讨论这一点。
*   允许我们将数据传递给组件，在这个例子中，我们将传递文章的 Id。

## 检查点:创建博文模板

需要接受的信息太多了，但我们就快成功了！

还记得上一节的`component`参数吗？

```
component: path.resolve(`./src/templates/post.js`), 
```

Enter fullscreen mode Exit fullscreen mode

让我们继续创建文件`post.js`。

注意:您需要在`src`中创建一个名为`templates`的新子目录。

`src/templates/post.js`

```
import React from 'react';
import Layout from '../components/layout';

export default () => {
    return (
        <Layout>
            <div>Hello blog post</div>
        </Layout>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们停下来重新开始盖茨比:`gatsby develop`

我们没有索引页面来查看我们新创建的帖子，但是 Gatsby 404 开发页面充当了方便的临时工具。

转到不存在的页面:`http://localhost:8000/xyz`

你现在应该可以看到你的 WordPress 博客文章和盖茨比知道的其他页面。

[![Post Template 1](img/b78204e1911d72c1207a6f3b4119f3a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OV9NwS0R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://booyaa.wtf/img/gxw-post-template-1.png)

你可能已经发现我们当前的帖子模板非常简单，所以点击我们的任何帖子都会显示相同的内容。要注意的重要事情是我们现在有了 WordPress 帖子的页面，并且每个帖子都使用了模板。

如果您遇到了问题，可以查看下面的 Git hash: `13a036ae2a8dea2ea0f7a910c35c2fe4789f9a50`

## 将模板变成有用的东西

正如您从上一节中了解到的，我们使用模板所做的一切就是生成 slugs。获取内容的方法是可用的，但是我们需要把它连接起来。同样，我们将只显示您需要添加/修改的位

```
import { graphql } from 'gatsby'

export default ({ data }) => {
  const post = data.wordpressPost
  return (
    <Layout>
      <div>
        <h1 dangerouslySetInnerHTML={{ __html: post.title  }} />
        <h3>
          date: {post.date} tags: {extractTags(post)}{'  '}
        </h3>
        <div dangerouslySetInnerHTML={{ __html: post.content  }} />
      </div>
    </Layout>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到函数的签名已经改变，允许数据传递(稍后将由 GraphQL 提供)。这里需要注意的关键是使用了 [dangerouslySetInnerHTML](https://reactjs.org/docs/dom-elements.html#dangerouslysetinnerhtml) 和 JSX [表情](https://reactjs.org/docs/introducing-jsx.html#embedding-expressions-in-jsx)(花括号——看起来有点像小胡子，不是吗？).

`dangerouslySetInnerHTML`a React 是 innerHTML 的替代品，为什么但是它危险吗？从代码中设置 HTML(或者在这种情况下从数据库中设置)是有风险的，因为很容易将自己暴露给 XSS。因此，通过使用这个助手，你承认，你正在做一些危险的事情，但在我们完全崩溃之前，让我们对我们正在解析的数据做一个快速评估。**我们的** WordPress 博客，内容**我们已经**写好了。所以除非我们有严重的自我厌恶，否则我们不太可能有危险。如果您确实需要安全地解析 HTML，有像 [html-react-parser](https://github.com/remarkablemark/html-react-parser) 这样的选项

如果我们感兴趣的字段我们的`post`对象没有任何标记，那么我们可以使用表达式来呈现它们`{post.date}`。

您可以用这些表达式粘贴任何有效的 JavaScript，而`extractTags`是一个有用的函数，可以使我们的帖子的标签阵列变平。下面是这个有用函数的定义(添加在我们的默认函数之后)。

```
const extractTags = post =>
  post.tags ? post.tags.map(x => x.name).join(', ') : 'none' 
```

Enter fullscreen mode Exit fullscreen mode

差不多了，只需要添加我们的 GraphQL 来支持这个模板。

```
export const pageQuery = graphql`
  query($id: String!) {
    wordpressPost(id: { eq: $id }) {
      title
      tags {
        name
      }
      date(formatString: "Do MMMM YYYY")
      content
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

当我们在`gatsby-node.js`(见下文)中创建静态页面时，[页面查询](https://www.gatsbyjs.org/docs/page-query/)获取我们在上下文中传递的 WordPress 文章 Id，并允许我们使用该 Id `wordpressPost(id: { eq: $id })`提取特定的 WordPress 文章，我们不需要提取任何东西，我们只需要标题、标签、发布日期和内容。注意:我们使用 GraphQL 来格式化日期。

```
createPage({
  path: `/${edge.node.slug}`,
  component: path.resolve(`./src/templates/post.js`),
  context: {
    id: edge.node.id,
  },
}) 
```

Enter fullscreen mode Exit fullscreen mode

演示:还记得我们的朋友`dangerouslySetInnerHTML`吗，你可以通过使用表达式来避免风险，但是正如你所看到的，如果有任何标记，它将被清除，也就是说，它将被转义。

## 检查点:让我们看看内容

我们最好重启盖茨比来看看我们的变化。

[![Post Template 2](img/bc6652ef42581649c323c35dd7fcbaa2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hVjAF_EX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://booyaa.wtf/img/gxw-post-template-2.png)

如果你卡住了，你可以看看下面的 Git hash: [`50156723a4b21f08baeece9a5f7cd3936e384ee8`](https://github.com/booyaa/wordsby/commit/50038f90ec70387cc1f5b4170bc49b162fc4ef7d)

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*