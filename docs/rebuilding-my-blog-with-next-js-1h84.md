# 用 Next.js 重建我的博客

> 原文：<https://dev.to/jolvera/rebuilding-my-blog-with-next-js-1h84>

> [也发表在 jolvera.dev](https://jolvera.dev/posts/rebuilding-my-blog-with-nextjs)

比起写内容，我更喜欢用新技术来入侵我的网站。这次我使用 Next.js 重新构建了它，如果你正在阅读这篇文章，我已经部署了第一个版本。

## 为什么 Next.js

随着 React 和 SSR 框架的兴起，例如 Gatsby 和 Next.js，我看到了许多用 Gatsby 构建的好看、快速的博客。[你可能已经看过丹的](https://overreacted.io/)。当然，我去试着建立自己的博客，但我不喜欢的是 Gatsby blog-starter 使用 GraphQL，这很棒，但我不认为我需要一个简单的博客。

因此，我有一个坏习惯，那就是想建立自己的版本，我开始研究如何用 Next.js 建立我的博客。

## 如何

在过去的几个月里，我通过提供一个例子和其他一些小的改动熟悉了 Next.js。我目前也在用它构建两个产品，所以我觉得我有信心可以构建一些像样的东西。

### 检查表

我从一个用 [create-next-app](https://github.com/segment-open-source-transfer/create-next-app) 创建的空项目开始，从那里我列出了我需要的特性。

1.  从一系列 [MDX](https://mdxjs.com/) 文件中创建一个文章对象
2.  添加语法突出显示
3.  添加 RSS 源
4.  添加分页

对于 Next.js 项目，所有这些对我来说都是新的，不知道如何实现它们。所以，我去调查其他人是怎么做的。

吉列尔莫·劳赫([@劳赫](https://twitter.com/rauchg))告诉我，马克斯·斯托伊伯的网站( [@mxstbr](https://twitter.com/mxstbr) )是他最喜欢的用 Next.js 建立的博客之一，所以我查看了他的 [GitHub repo](https://github.com/mxstbr/mxstbr.com) 。

Max 的仓库中已经完成了 80%的工作，我也很幸运地和他聊到了这件事。这项工作的大部分功劳归于他，他的实现非常聪明，对我帮助很大。

### 1。一篇博文

为了解决第一个问题，Max 使用了[babel-plugin-preval/macro](https://github.com/kentcdodds/babel-plugin-preval)。这个包允许您在客户端上下文中动态运行节点代码，并导出结果。

使用文件系统模块，我们从给定的文件夹(例如，`posts/`)中获取所有文件，提取元数据、内容、路径，并返回一个包含我们需要的所有信息的`Object`。[见 Max 的实现](https://github.com/mxstbr/mxstbr.com/blob/master/data/get-blog-posts.js)。

这样，我们可以将博客文章列表作为`import blogposts from "../../data/blog-posts";`导入，并在`blog.js`页面中使用。[见 Max 的实现](https://github.com/mxstbr/mxstbr.com/blob/master/pages/thoughts/index.js)和[矿](https://github.com/j0lv3r4/jolvera.dev/blob/master/pages/blog.js)。

举个简单的例子，我们可以这样做。

```
import blogposts from "../posts/index";

const Blog = () => (
  <ul>
    {blogposts
      .map((post, index) => (
        <li key={index}>
          <a href={post.path}>{post.title}</a>
          <time>{post.publishedAt}</time>

          {post.summary}
        </li>
    ))}
  </ul>
);

export default Blog; 
```

### 2。语法突出显示

第二个问题更棘手。基本语法突出显示与[retype-prism](https://github.com/mapbox/rehype-prism)一起工作，但是缺少一个关键特性。突出显示一行代码的能力，例如:

[![Snippet of code from Dan Abramov’s blog](img/bf6b5585d9775591ad53cb07ef985e87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--47srj0_2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jolvera.dev/static/overreacted-code-snippet.jpg)

这可能是这个过程中最困难的部分(对我来说)。为了实现这一点，我去了一个兔子洞学习关于 [unified.js](https://unified.js.org/) 和语法树如何工作。我必须了解 Gatsby 团队和贡献者如何实现他们自己的插件，以及如何将它们插入 MDX 插件接口。

我从这些软件包中获得了灵感:

*   [盖茨比-备注-普里姆斯](https://github.com/gatsbyjs/gatsby/tree/master/packages/gatsby-remark-prismjs)
*   [@ map box/re type-prism](https://github.com/mapbox/rehype-prism)
*   [折射镜](https://github.com/wooorm/refractor)

我不会详细介绍，但是我集成了这三个包中的代码，使语法高亮与行高亮特性一起工作。还有其他功能缺失，但这将做到这一点。[查看当前的实现](https://github.com/j0lv3r4/jolvera.dev/blob/master/mdx-prism/index.js)，我可能会向`rehype-prism`发出一个 pull 请求来添加这个功能或者发布一个单独的模块。

### 3。简单讯息聚合订阅

这很容易。我跟随 Max 使用了 JSON 提要规范并将博客文章`Object`重新格式化为 JSON RSS 提要。

我仍然没有弄清楚如何在构建时创建 JSON 文件，所以现在我在提交更改以生成提要和[将其作为静态文件](https://github.com/j0lv3r4/jolvera.dev/blob/master/now.json#L7)之前运行[节点脚本](https://github.com/j0lv3r4/jolvera.dev/blob/master/posts/rss-feed.js)。

### 4。页码

这也很容易。我使用[分页库](https://www.npmjs.com/package/pagination)和[博客文章`Object`的长度和其他选项作为输入](https://github.com/j0lv3r4/jolvera.dev/blob/master/pages/blog.js#L13-L18)。

## 部署

在这一切之后，我用 Next.js 将这个项目放到了一个足够好的工作博客中，此时我真的很兴奋能够部署它，并且在周六晚上我决定用 Now 2.0 发布它。

## 开发环境

使用 Next.js 和 Now 2.0 的一个好处是新的 [`now dev`](https://zeit.co/blog/now-dev) 命令。你可以确切地看到你在生产中会得到什么。它使用相同的`now.json`配置文件，几乎所有其他东西都以相同的方式工作。

所有的工作都感觉比应该的要容易。

## 结论

网站感觉真的很快。[灯塔审计](https://twitter.com/_jolvera/status/1127432136565383169?s=20)成果惊人。除了没有登录之外，添加内容的容易程度就像你在处理一个 CMS。

Next.js 提供的 SSR 和预取特性让这个网站感觉非常流畅、快速和灵敏。

到目前为止，我对使用 Next.js 和 Now 开发的体验非常满意，结果不言自明。我将向 Next.js 存储库提交一个 pull 请求，以添加 blog 作为示例，我希望有人会发现这项工作很有用，就像我发现 Max 的工作一样。