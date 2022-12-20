# 将 Gatsby 默认入门博客转换为使用 MDX

> 原文：<https://dev.to/spences10/convert-the-gatsby-default-starter-blog-to-use-mdx-4f5e>

###### 詹姆斯·惠勒在 Unsplash 上拍摄的照片

在本指南中，我们将介绍如何将 Gatsby 默认博客启动器转换为使用 MDX。

这些天所有酷小孩都在博客里用盖茨比和 MDX。如果你已经有了一个使用盖茨比的博客，但想转移到新的热点，那么这是给你的指南。

[https://www.youtube.com/embed/gck4RjaX5D4](https://www.youtube.com/embed/gck4RjaX5D4)

## 版本:

本指南正与以下依赖版本一起使用。

*   盖茨比:2.3.5
*   反应:16.8.6
*   反应范围:16.8.6
*   gatsby-mdx: 0.4.5
*   @mdx-js/mdx: 0.20.3
*   @mdx-js/tag: 0.20.3

您还可以查看[示例代码](https://codesandbox.io/s/lqp6p647q)。

* * *

我们需要一些链接，它们是:

*   [用于导入项目的 CodeSandbox 文档](https://codesandbox.io/docs/importing)

*   [CodeSandbox 导入向导](https://codesandbox.io/s/github)

*   [盖茨比入门博客](https://github.com/gatsbyjs/gatsby-starter-blog)

## 导入到 CodeSandbox

对于这个例子，我将使用 [Gatsby starter 博客](https://github.com/gatsbyjs/gatsby-starter-blog)并将其导入 CodeSandbox，查看文档，它说您可以通过链接 [CodeSandbox 导入向导](https://codesandbox.io/s/github)来完成此操作，将
链接粘贴到那里，CodeSandbox 将打开 GitHub 上的代码表示。

现在，我们可以开始从使用 Gatsby transformer 注释转移到 MDX。

让我们来看看我们将为这个例子改变什么。但是首先我们需要导入一些依赖项来让 MDX 在 Gatsby 项目中运行。

使用 CodeSandbox 中的“添加依赖项”按钮添加以下依赖项:

*   `gatsby-mdx`
*   `@mdx-js/mdx`
*   `@mdx-js/tag`

我们还需要为样式化组件添加依赖项，所以现在也可以添加它们:

*   `gatsby-plugin-styled-components`
*   `styled-components`
*   `babel-plugin-styled-components`

要更改的文件:

*   `gatsby-node.js`
*   `gatsby-config.js`
*   `index.js`
*   `blog-post.js`

## `gatsby-node.js`

首先我们需要改变`gatsby-node.js`这是所有页面和数据节点生成的地方。

使用 MDX 更改所有出现的 markdown 注释，这是 create pages 中的初始 GraphQL 查询，然后在结果中再次出现。

[![initial gatsby node changes](img/e03a616002885f6618bef989db38286c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ly7etaRp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yudavhb32mefgwcupjcs.png)

然后将`onCreateNode`中的`node.internal.type`从`MarkdownRemark`改为`Mdx`。

[![last gatsby node changes](img/e46dd5236905d025cbea247e3c2bf634.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BzM-1H----/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3bo7vlmoggtgixmnev7k.png)

## `gatsby-config.js`

这里我们将把`gatsby-transformer-remark`替换为`gatsby-mdx`

[![replace transformer remark with gatsby-mdx](img/0d88e3f2af7bafcb8f6145c41a24056e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YcQNwRgo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5qn26xhffc5mdwnvfcha.png)

## `index.js`

在这里，我们将改变`posts`变量以获得`Mdx`边缘。

[![replace all markdown edges](img/fd0ddb69244abb668ffa16cc9d607c40.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2203LdNi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wxttdoy68yzw8n0x981o.png)

`Mdx`边取自页面查询，它也被修改为使用`allMdx`代替`allMarkdownRemark`。

[![index page query](img/a17ea06b1011b2f48e00ff62b083f08f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GNsg0Lgg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iuasg7uvinhx8c7xg4vi.png)

## `blog-post.js`

现在，列表中最后一个让 MDX 工作的是博客文章模板，我们将需要从`gatsby-mdx`导入`MDXRenderer`，我们将很快用它替换`dangerouslySetInnerHTML`。

[![MDX renderer](img/7810b623b00300b81ac8163bb16c43b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6oTthxgD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1undbffzfr5bah42zm3m.png)

这里是我们使用它的地方，我们将来到`post.code.body`。

[![replace dangerously set html](img/2673451ae45b6f5e8ba4fa53dd08d5fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2KPQI6RI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k9ruf3ygazxxg0cg3psx.png)

同样在查询中，我们用`mdx`替换了`markdownRemark`，这次也从查询中去掉了`html`，并添加了`code`来代替我们在渲染方法中使用的`body`。

[![](img/78b4d77f8aa4d5c6670f2644c38aa6fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0NPp3d1B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e3lmzhdskhx4f717o8eh.png)

## 现在我们用的是 MDX！

现在我们可以创建一个`.mdx`帖子，让我们开始吧。

导入样式化组件依赖:

```
gatsby-plugin-styled-components
styled-components
babel-plugin-styled-components 
```

Enter fullscreen mode Exit fullscreen mode

然后在`gatsby-config.js` :
中进行配置

```
module.exports = {
  siteMetadata: {
    title: `Gatsby Starter Blog`,
    ...
    },
  },
  plugins: [
    `gatsby-plugin-styled-components`,
    {
      resolve: `gatsby-source-filesystem`,
      options: {
  ... 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用样式化组件，在`src/components`中创建一个新组件，我已经把我的组件命名为`butt.js`你喜欢什么就叫你的组件。

我们将在一个`.mdx`文档中使用这个组件，首先是组件:

```
import styled from 'styled-components'

export const Butt = styled.button`
  background-color: red;
  height: 40px;
  width: 80px;
` 
```

Enter fullscreen mode Exit fullscreen mode

辣，对！🌶

现在我们可以在一个`.mdx`文档中包含这个组件，所以让我们继续创建，在`content/blog`中创建一个新目录，我给我的目录起了一个富有想象力的名字`first-mdx-post`，在那里创建
一个`index.mdx`文件，并使用其他帖子中的 frontmatter 作为使用示例:

```
---
title: My First MDX Post!
date: '2019-04-07T23:46:37.121Z'
---

# make a site they said, it'll be fun they said

more content yo! 
```

Enter fullscreen mode Exit fullscreen mode

这将呈现一个`h1`和一个`p`，我们应该在我们的 CodeSandbox 预览中看到它的呈现。

现在我们可以导入我们制作精美的按钮:

```
---
title: My First MDX Post!
date: '2019-04-07T23:46:37.121Z'
---

import { Butt } from '../../../src/components/button'

# make a site they said, it'll be fun they said

more content yo!

<Butt>yoyoyo</Butt> 
```

Enter fullscreen mode Exit fullscreen mode

## 总结一下！

所以，就是这样，我们已经把 Gatsby starter 博客从使用 Markdown Remark 转换成使用 MDX。

我希望它对你有所帮助。

**感谢阅读**🙏

如果你喜欢这个，请看看我的其他内容。

在 Twitter 上关注我，或者在 GitHub 上关注 T2 问我任何问题。