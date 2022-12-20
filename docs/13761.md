# 用 GatsbyJS & Netlify 写草稿

> 原文：<https://dev.to/chaseadamsio/writing-drafts-in-gatsbyjs--netlify-3chn>

有一些在 GatsbyJS 中保存草稿的解决方案，但是没有一个达到我想要的效果。我发现的那些需要`graphql`做繁重的工作来过滤草稿，这意味着要过滤掉很多草稿错误的东西。* *如果你不关心理由，你可以跳到[关于我如何用盖茨比](https://dev.to/gatsby-drafts/#how-i-write-drafts-in-gatsby)写草稿的代码。

## 正当理由

这些是引导我使用的解决方案的规格:

*   很容易推断哪些文章处于草稿状态。
*   不使用 Netlify CMS 的单个作者(也适用于多个作者，但我不需要)。
*   我的站点的产品构建/运行时应该完全不知道草稿。

### 找草稿很清楚

我强烈主张减少寻找“工作进行中”状态内容的摩擦。为草稿建立一个单独的目录有助于我做到这一点。

在 frontmatter 中使用`drafts`的一个缺点是，你通常不得不用一种工具在 frontmatter 中搜索，这种工具可以判断一个文件是草稿(grep、ripgrep 等)*还是*将状态保存在你的大脑中(这是对大脑的一种不好的使用)。

### 单个作者在 Dropbox 上托管内容

我是我的盖茨比网站上唯一的作者，并使用 Dropbox 来托管我的内容。我认为这个解决方案可以很容易地为使用 git 作为“后端”的多个作者工作(你可能使用 Netlify CMS 的方式)，但我没有用这个模式尝试过，所以里程可能会有所不同。

我还使用 Dropbox 来托管我的内容和构建，这是文件更改的结果。拥有一个独立的草稿文件夹让我可以在不触发不必要的 Dropbox/Netlify 钩子的情况下写内容。

### 安全生产

我不想在我的 GraphQL 中有一堆草稿过滤器。我想不出一个令人信服的理由让 GraphQL 过滤草稿，因为我知道我不希望草稿出现在产品中)。拥有一个单独的草稿文件夹减少了使用 GraphQL 进行同样的过滤所带来的复杂性(和潜在的错误)。

## 我如何在《盖茨比》中写草稿

现在到了有趣的部分:配置盖茨比！

我的 src 目录中有两个内容文件夹:

*   `content` -准备生产的内容
*   `drafts` -我仍在创作的内容

内容生命周期有 3 个“阶段”:

| 阶段 | `content`出席 | `drafts`出席 |
| --- | --- | --- |
| 地方发展 | ✅ (symlink) | ✅ (symlink) |
| 非生产网络 | ✅(在构建时被拉) | ✅(在构建时被拉) |
| 生产效率 | ✅(在构建时被拉) | -好的 |

目标是在任何非生产阶段都有一个`drafts`的`gatsby-source-filesystem`,甚至在生产阶段也不用担心它。

### Gatsby 配置设置

在我的`gatsby-config.js`中，我有一个 Gatsby 配置对象(而不是直接在`module.exports`上设置对象):

```
const cfg = {
  /* ...my default configuration */
};

module.exports = cfg; 
```

Enter fullscreen mode Exit fullscreen mode

然后对 Gatsby 构建我的站点的环境进行条件检查(基于 Netlify 的环境上下文):

```
if (process.env.CONTEXT !== "production") {
  const draftsCfg = {
    resolve: `gatsby-source-filesystem`,
    options: {
      name: `drafts`,
      path: `${__dirname}/src/drafts`
    }
  };
  cfg.plugins.push(draftsCfg);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果`process.env.CONTEXT`不是`production`，提供`drafts`作为文件系统源。

*   `draftsCfg`是一个`gatsby-source-filesystem`插件配置对象，其中内容的`sourceInstanceName`是`drafts`，内容的路径是项目根目录下的`src/drafts`目录。

接下来，我们将`draftsCfg`推入 Gatsby 在构建站点时将使用的`plugins`对象。

现在每当盖茨比在本地或者在非制作分支运行(因为 Netlify 的预览分支很牛逼！)并且生产环境中的构建完全不知道我们有一个`drafts`文件夹。

### 告诫

这看起来很大程度上依赖于使用 Netlify，但是每个称职的主机提供商都提供了设置环境变量的方法。Netlify 是开箱即用的，所以它非常方便，但如果你使用另一个主机提供商，它就不是一个节目停止器。

大❤️感谢盖茨比和 Netlify 让这个过程变得超级简单！