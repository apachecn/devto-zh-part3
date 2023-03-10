# 构建我的个人站点——第 2 部分:用入门站点设置 Gatsby

> 原文：<https://dev.to/ekafyi/building-my-personal-site--part-2-setting-up-gatsby-with-a-starter-site-1ob1>

年初的时候，我还没有网站。所以我把“建立一个个人网站”作为我 2018 年的目标之一。到 2018 年 12 月的最后一周...我还是没有网站。请跟随我创建一个简单的登录页面，这样我就可以检查今年的至少一个目标！🤦🏽‍♀️✅

前情提要:

[![ekafyi image](img/c93ebdb7f76a26e4e235feb2075f96ee.png)](/ekafyi) [## 构建我的个人网站——第 1 部分:规划和设计

### eka Dec 20 ' 187 分钟读数

#showdev #design #webdev #rant](/ekafyi/building-my-personal-site--part-1-planning-and-designing-4fm)

现在，我将通过把[Gatsby-starter-morning-dew](https://github.com/maxpou/gatsby-starter-morning-dew)starter 网站修改成[我自己的网站](https://github.com/ekaoddlass/ekaaa/tree/4d43b33d57f9c2c9c3d5c4b01e3e0befb5a4d254)，来建立我和 Gatsby 的网站。

## 选择盖茨比

Gatsby 是一个非常流行的静态(ish)*站点生成器，运行在 [React](https://reactjs.org) 上。我一直在一个正在进行的项目中使用它，就像每个开发人员和他们的叔叔(和婶婶，以及堂兄妹)一样，我觉得它令人印象深刻。

盖茨比的一些优点是:

*   超级快🚀( <abbr title="Server Side Rendering">SSR</abbr> ，代码拆分，预取，镜像优化等)
*   恒星文档
*   伟大的社区和生态系统
*   出色的开发人员体验(复杂而强大的*和*旨在使用经过深思熟虑的配置和功能来解决常见用例的繁重工作，从而减轻开发人员的工作量)
*   灵活使用任何数据源(本地文件、 <abbr title="Application Programming Interface">API</abbr> 、其他 <abbr title="Content Management System">CMS</abbr> 或数据库)

Gatsby 编译成静态资产，但是在需要的地方使用常规 React 站点的“动态”功能。

抛开炒作不谈，我为什么要和盖茨比建个人网站？

*   谁能对超快的网站说不？
*   我可以用降价文件作为数据源。
    *   这是理想的，因为我可以在没有 CMS、数据库或外部 API 调用的情况下更新我的站点。每当我想发表一篇新文章时，我可以打开一个文本编辑器，输入、保存、构建和部署。(我使用 VSCode，所以我可以在一个窗口中完成所有这些操作！)
*   我理解 React 和 Gatsby 的基础，但我想更好地理解它们是如何工作的。
*   Gatsby 提供了与各种现代前端工具/库的集成，这是我今后想要学习的。

对于这个站点，我不会从头开始安装 Gatsby，而是从一个通常称为“入门站点”的样板文件开始安装。

## 选择并安装 Gatsby starter 站点

<figure>

[![Automated search recommending 'depression starter pack' when I typed 'gatsby starter' in the URL bar](img/2c7415298cd47a86eb8059c02acb18f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LAJRu9W_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sjniw4j8azanmv7f7zze.png)

<figcaption>What the heck, search recommendation algorithm?</figcaption>

</figure>

盖茨比生态系统的一个很酷的事情是有大量的初始站点。初学者网站节省时间，非常适合学习特定工具的最佳实践。您可以通过技术、依赖项和关键字来过滤启动器。

💡*边注:相反的方法，[从零开始建立一个网站](https://www.gatsbyjs.org/tutorial/part-one/)并自己添加依赖项，这是熟悉盖茨比基础的理想方法。*

<figure>

[![Gatsby Starters listing](img/4d10ff3159227df66b1466da36683acb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZRdFZ6Af--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b0auwm72o3fr9om2ore6.png)

<figcaption>Gatsby Official Starter Library</figcaption>

</figure>

使用入门网站也是获取常用前端工具的好方法。您可以检查`package.json`文件中的`scripts`对象。有有用的命令用于林挺、格式化、清理、测试，甚至[检查你的英语](https://github.com/btford/write-good)！如果我自己单独学习这些工具，这将比我简单地检查`package.json`文件并根据需要运行、复制、修改花费更多的时间和毅力。

<figure>

```
"scripts": {
  "develop": "gatsby develop",
  "serve": "gatsby serve",
  "build": "gatsby build",
  "build:pp": "gatsby build --prefix-paths",
  "build:gh": "npm run clean && npm run build:pp && gh-pages -d public",
  "clean": "rm -rf .cache && rm -rf public",
  "lint": "npm run lint:js && npm run lint:css && npm run lint:md",
  "lint:js": "eslint --ext .js,.jsx --ignore-pattern public --ignore-pattern static .",
  "lint:css": "stylelint --fix 'src/**/*.css'",
  "lint:md": "remark content/posts/",
  "write-good": "write-good $(glob 'content/posts/**/*.md')",
  "format:js": "prettier '**/*.{js,jsx}' --write",
  "release": "standard-version -a"
} 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Example of scripts from gatsby-v2-starter-casper’s package.json file</figcaption>

</figure>

另一方面，大量的初始站点[可能会让](https://www.gsb.stanford.edu/insights/research-too-many-choices-can-derail-success)不知所措。为了避免被[的过度选择](https://en.wikipedia.org/wiki/Overchoice)所麻痹，我将搜索标准缩小到以下特征:

*   为*博客*版块发布标签
*   带头盔的 SEO 元数据模板
*   清单和离线插件
*   棉绒和格式器(更漂亮)

在犹豫了将近两天之后，我缩小了搜索范围:

*   [盖茨比-高级-首发](https://www.gatsbyjs.org/starters/Vagr9K/gatsby-advanced-starter/)由 Vagr9K
*   盖茨比中心
*   格雷格·洛宾斯基的《盖茨比-首发-英雄-博客》
*   贾斯汀·福门廷的《盖茨比-v2-教程-入门》
*   马克森斯·普托德的《盖茨比-首发-晨露》

这些启动器有我想要的功能，所以我只安装了全部五个(是的，我知道…😆).只有四个会跑，我和他们玩了一会儿。我最终选择了**Gatsby-starter-morning-dew**,因为它的架构最接近我的需求，并且它有额外的功能，如生成社交媒体预览图像和将所有网站元数据存储在一个 JSON 文件中的`siteConfig`对象。整洁！

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ maxpou ](https://github.com/maxpou) / [盖茨比-首发-晨露](https://github.com/maxpou/gatsby-starter-morning-dew)

### 🚀盖茨比主题/建立快速博客/网站的开始

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Build Status](img/cca5a75060662e627e9fd68afb8c3a0f.png)](https://travis-ci.com/maxpou/gatsby-starter-morning-dew)[![Netlify Status](img/4133124e3de1ac2b7120d53abbe0ac3e.png)](https://app.netlify.com/sites/gatsby-starter-morning-dew/deploys)[![code style: prettier](img/e7e7a68e574b24697e1222655cdde8b6.png)](https://github.com/prettier/prettier)[![Maintainability](img/2253db65d71be734ec4d0d704f57b9ba.png)](https://codeclimate.com/github/maxpou/gatsby-starter-morning-dew/maintainability)[![semantic-release](img/a692f1dbafdf53023b8a11ad2a76d9c8.png)](https://github.com/semantic-release/semantic-release)

# 盖茨比-先发制人-晨露

<g-emoji class="g-emoji" alias="rocket" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f680.png">🚀</g-emoji>盖茨比启蒙者晨露是构建闪电般快速网站的盖茨比启蒙者！
T3】💫你也可以用《盖茨比-首发-晨露》作为盖茨比主题

[演示网站。](https://maxpou.github.io/gatsby-starter-morning-dew/)

[![screenshot](img/9218c35f3bfb8376dacddb3c5be292bb.png)](https://camo.githubusercontent.com/7cb3cccd73ada47040297c2efdea33517e0f5357693f29518ffe89686fdf6bac/68747470733a2f2f692e696d6775722e636f6d2f6e5074685351792e706e67)

## 特征

*   [<g-emoji class="g-emoji" alias="purple_heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f49c.png">💜</g-emoji>盖茨比 v3](https://www.gatsbyjs.org/) / React 16
*   <g-emoji class="g-emoji" alias="mag" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f50d.png">🔍</g-emoji> SEO 优化
*   <g-emoji class="g-emoji" alias="love_letter" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f48c.png">💌</g-emoji>在 Markdown 或 [MDX 中写帖子/页面](https://mdxjs.com/)
    *   <g-emoji class="g-emoji" alias="art" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f3a8.png">🎨</g-emoji>代码语法高亮显示
    *   <g-emoji class="g-emoji" alias="books" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4da.png">📚</g-emoji>多边支持/i18n(博客文章)
*   <g-emoji class="g-emoji" alias="iphone" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4f1.png">📱</g-emoji>移动友好型(响应式设计)
*   <g-emoji class="g-emoji" alias="last_quarter_moon_with_face" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f31c.png">🌜</g-emoji>黑暗模式
*   ✨准备好了吗
    *   <g-emoji class="g-emoji" alias="airplane" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2708.png">✈️</g-emoji> 离线支持
    *   <g-emoji class="g-emoji" alias="page_with_curl" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4c3.png">📃</g-emoji>明示支持
*   [<g-emoji class="g-emoji" alias="wrench" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f527.png">🔧</g-emoji>完全可配置(见 data/siteConfig.js)](https://raw.githubusercontent.com/maxpou/gatsby-starter-morning-dew/master/./data/siteConfig.js)
*   <g-emoji class="g-emoji" alias="speech_balloon" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4ac.png">💬</g-emoji> Disqus 支持
*   <g-emoji class="g-emoji" alias="nail_care" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f485.png">💅</g-emoji> css-in-js(带[样式组件 v5](https://www.styled-components.com)
*   <g-emoji class="g-emoji" alias="bookmark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f516.png">🔖</g-emoji>带标签的群组帖子
*   <g-emoji class="g-emoji" alias="bird" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f426.png">🐦</g-emoji>发布预览图像生成(推特，脸书...)
*   <g-emoji class="g-emoji" alias="gem" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f48e.png">💎</g-emoji>开发者工具
    *   埃斯林特
    *   较美丽
*   <g-emoji class="g-emoji" alias="construction_worker" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f477.png">👷</g-emoji> Travis CI 支持

[![Lighthouse scores (locally :bowtie:)](img/37f934ac82e2fabd9bd6b6e951fee0ab.png)](https://camo.githubusercontent.com/84903a1f6b678941d38edccc7e04a458c5e7eb04716a5a82f34a6529d09d1993/68747470733a2f2f6c69676874686f7573652e6e6f772e73682f3f706572663d313030267077613d31303026613131793d3130302662703d3130302673656f3d313030)

## 安装(启动器)

*   使用 Gatsby-cli:

```
npm install --global gatsby-cli
gatsby new gatsby-blog https://github.com/maxpou/gatsby-starter-morning-dew
```

Enter fullscreen mode Exit fullscreen mode

*   没有盖茨比-cli

```
git clone my-site git@github.com:maxpou/gatsby-starter-morning-dew.git
cd my-site
npm install
```

Enter fullscreen mode Exit fullscreen mode

然后运行`npm run develop`进行本地尝试。

## 装置(主题)

还可以用`gatsby-starter-morning-dew`作为[盖茨比主题](https://www.gatsbyjs.org/blog/2018-11-11-introducing-gatsby-themes/)！

1.  添加主题

```
npm install
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/maxpou/gatsby-starter-morning-dew)

由于我已经安装了 Gatsby <abbr title="Command Line Interface">CLI</abbr> ，我只需在 CLI 中运行以下命令:

```
gatsby new ekaaa https://github.com/maxpou/gatsby-starter-morning-dew # creates a directory called `ekaaa` and install the starter site there

cd ekaaa # change directory

yarn install # install dependencies

gatsby develop # test if everything works 
```

Enter fullscreen mode Exit fullscreen mode

该应用程序编译成功，我可以在浏览器中的 [http://localhost:8000](http://localhost:8000) 查看它。耶，我有一个盖茨比网络应用程序！

接下来，我将**检查应用架构**，这样我就知道什么该放在哪里，并且**修改文件**以满足我的需求。现在，我只是要修改应用程序/网站架构——我将在下一篇文章中讨论 UI 组件。

💡*提示:你**可以**建立并运行自己的网站，只需更改初始网站的详细信息(标题、作者姓名等)，将你的内容添加到 Markdown 文件中，更改样式(颜色、排版)，并进行部署——无需进一步操作。然而，我个人发现对一个入门网站做进一步的修改是提高我技能的一种有趣的方式，即使我还没有完全理解所有的东西。如果这是你的第一次，我推荐从 [React](https://reactjs.org/docs) 和 [Gatsby](https://www.gatsbyjs.org/docs/) 官方文档开始。*

## 检查启动站点架构

这是我选择的起始站点的文件和文件夹结构。我省略了我现在不需要的其他配置文件。

```
├── content # Markdown and images content
│   ├── pages
│   └── posts 
├── data # Site metadata and custom configuration
├── scripts # Custom script (generate preview image)
├── src
│   ├── assets # Maybe non-content images?
│   ├── components
│   ├── pages # 404 and custom pages
│   └── templates # Dynamically generated page templates (eg. Blog Post, Tag List)
├── static # Site images (logo, favicon, etc) and robots.txt
├── gatsby-config.js # App & plugins configuration
└── gatsby-node.js # Generate pages dynamically (post list, posts, default pages, post list by tags) 
```

Enter fullscreen mode Exit fullscreen mode

*   *博客文章*是通过在`content/posts`目录下创建一个 Markdown 文件生成的。它使用了`src/templates/blog-post.js`中的帖子模板。
    *   例如，用`slug: hello-world`添加`hello-world.md`会自动在[http://localhost:8000/hello-world](http://localhost:8000/hello-world)创建一个帖子，并将其添加到*帖子列表页面*。
*   *默认页面*是通过在`content/pages`中创建一个 Markdown 文件生成的。它使用了`src/templates/page.js`中的默认页面模板。
    *   比如用`slug: contact`创建`contact.md`会自动在[http://localhost:8000/contact](http://localhost:8000/contact)创建一个页面。
    *   这种类型有两个现成的页面，*关于*和*安装*。*帖子列表页面*自动生成，包含所有*博文*摘录。它有内置分页功能。
*   *标签页*是为*博文*首页的每个`tags`条目自动生成的。
    *   如果您有一个名为“javascript”的标签，您可以在[http://localhost:8000/tags/JavaScript](http://localhost:8000/tags/javascript)访问该标签
*   上面所有的页面都是使用`gatsby-node.js`中 Gatsby 的`onCreateNode`和`createPages` API 动态生成和填充的。
    *   这个启动器通过[Gatsby-transformer-remark](https://www.gatsbyjs.org/packages/gatsby-transformer-remark)和 [gatsby-transformer-sharp](https://www.gatsbyjs.org/packages/gatsby-transformer-sharp) 插件使用 Markdown 和图像数据源，但是你可以添加任何你需要的额外数据源。
*   还可以通过在`src/pages`中创建一个 React 组件文件，使用自定义模板和数据生成页面。
    *   例如，创建导出默认组件`ContactPage`的`contact.js`也会在[http://localhost:8000/contact](http://localhost:8000/contact)创建一个页面，但是呈现的是这个组件而不是默认的页面模板。
    *   目前这个目录只包含`404.js`，它是为 404 错误页面自动渲染的
*   站点范围的设置(包括导航菜单)可以在`data/siteConfig.js`中配置。
*   所有其他组件(布局、SEO、UI 组件)都在`src/components`目录中。

💡*旁注:如果你不熟悉 [Markdown text format](https://guides.github.com/features/mastering-markdown/) 和 [front matter](https://caddyserver.com/docs/markdown) ，请阅读更多相关内容。*

## 修改启动站点

#### 1)博客(帖子列表)页面

我正在将*“帖子列表”*页面路径从`/` (root)更改为`/blog`，这样我就可以在之后定制一个登陆页面。我只需在`gatsby-node.js`中编辑路径。

```
// gatsby-node.js
Array.from({ length: nbPages }).forEach((_, i) => {
  createPage({
    path: i === 0 ? `/blog` : `/blog/page/${i + 1}`,
    component: ListPostsTemplate,
    context: {
      limit: postsPerPage,
      skip: i * postsPerPage,
      currentPage: i + 1,
      nbPages: nbPages,
    },
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记更改*分页*组件中的“下一页/上一页”链接。

```
// src/components/Pagination.js
const previousUrl = currentPage === 2 ? '/blog' : `/blog/page/${currentPage - 1}`
return (
  // ...
    <NextBtn to={`/blog/page/${currentPage + 1}`}>Older posts ›</NextBtn>
  // ...
) 
```

Enter fullscreen mode Exit fullscreen mode

**结果:**视图仍然是 starter 站点的默认视图，但是路由更改为`/blog`、`/blog/page/2`等。

<figure>

[![Blog Page](img/7bf459ddd4f4f59b672016f47d313250.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AxGjCc_X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/32ghu0rrwgq6mpzoc8y7.png)

<figcaption>Blog Page — ekaaa.me/blog</figcaption>

</figure>

#### 2)首页(登陆)页面

这是我今年网站第一次迭代的唯一目标部分。未来五天)。我正在制作一个**定制登陆页面**，其中包含:

*   介绍和正文
*   2 篇最新帖子
*   5 个最新项目
*   1 个随机引用

**步骤 0:** 打开 localhost:8000，得到 404 错误页面(因为我已经把帖子列表路由改成了`/blog`)。

**第一步:**在`src/pages/`目录下制作一个名为`index.js`的自定义页面模板。我从现有的页面模板`404.js`复制内容，并稍加修改。打开 localhost:8000，现在新模板已经加载完毕。

**第二步:**在`content/pages/`目录下制作一个文件`landing.md`。该文件包含特定于登录页面的内容。

```
--------
slug: landing
type: page
description: Enter landing page intro text here
quotes: [
  {
    'quoteBody': 'The quick brown fox jumped over the lazy dog.',
    'quoteSource': 'Anonymous'
 },
-------- 
Enter landing page body text here 
```

Enter fullscreen mode Exit fullscreen mode

**第三步:**启动器带有“文章”和“页面”内容，但是我们还没有“项目”内容类型。在`content`中创建一个名为`projects`的新目录，并用 Markdown 文件填充它们。我实际上还没有任何项目内容，所以我只是做了虚拟文件。在前面加上`type: project`。

```
mkdir content/projects touch content/projects/project-one.md content/projects/project-two.md 
```

Enter fullscreen mode Exit fullscreen mode

Gatsby 使用 [gatsby-source-filesystem](https://www.gatsbyjs.org/packages/gatsby-source-filesystem/#gatsby-source-filesystem) 插件从 Markdown 文件中获取数据，所以确保将新的源目录添加到`gatsby-config.js`中的插件数组中。

```
// gatsby-config.js
module.exports = {
  plugins: [
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        path: `${__dirname}/content/projects`,
        name: 'projects',
      },
    }
  ],
} 
```

Enter fullscreen mode Exit fullscreen mode

💡要了解如何使用 Markdown 文件作为数据源，请查看本教程。

**第四步:**用`graphql`将第二步到第三步的数据拉至`index.js`模板。 [GraphQL](https://graphql.org) 是 Gatsby 内置的开源 API 查询语言。以下是我的代码，为了简洁起见被截断了。

```
// src/pages/index.js
export const pageQuery = graphql`
  query {
    posts: allMarkdownRemark(
      sort: { fields: [frontmatter___date], order: DESC } 
      filter: { frontmatter: { type: { eq: null } } } 
      limit: 3 
    ) {
      edges {
        node {
          excerpt
          frontmatter {
            title
            # etc
          }
        }
      }
    }
    projects: allMarkdownRemark(
      sort: { fields: [frontmatter___date], order: DESC }
      filter: { frontmatter: { type: { eq: "project" } } } 
      limit: 3 
    ) {
      edges {
        node {
          excerpt
          frontmatter {
            title
            # etc
          }
        }
      }
    }
    landing: allMarkdownRemark(
      filter: { frontmatter: { slug: { eq: "landing" } } } 
      limit: 1
    ) {
      edges {
        node {
          excerpt
          html
          frontmatter {
            title
            # etc
          }
        }
      }
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

基本上它说:

*   获得 3 个最新条目，其中`type`为空(我将“post”作为默认类型)
*   获得 3 个最新条目，其中`type`等于`project`
*   获得 1 个特定条目，其中`slug`等于`landing`

可以看到 GraphQL 如何比 REST API 调用更灵活；这些不同类型的数据可以用一个查询来调用！

在组件中可以使用`posts`、`projects`、`landing`对象。你可以像任何常规的 React 数据道具一样使用它，例如:

```
// src/pages/index.js
class LandingPage extends Component {
  render() {
    // data from graphql query
    const posts = this.props.data.posts.edges 
    const projects = this.props.data.projects.edges
    const landing = this.props.data.landing.edges[0].node
    const quotes = landing.frontmatter.quotes
    const quote = quotes[0]

    return (
      <div>
        <h3>{landing.frontmatter.description}</h3>
        <BodyText dangerouslySetInnerHTML={{ __html: landing.html }} />
        <section>
          <h3>Recent Projects</h3>
          <RelatedPosts posts={projects} />
        </section>
        <section>
          <h3>Recent Posts</h3>
          <RelatedPosts posts={posts} />
        </section>
        <div>
          <blockquote>{quote.quoteBody}</blockquote>
          <cite>{quote.quoteSource}</cite>
        </div>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个高度简化的片段，只是为了给出它如何工作的概念。可以在我的 Github 中查看[完整代码。](https://github.com/ekaoddlass/ekaaa/blob/4d43b33d57f9c2c9c3d5c4b01e3e0befb5a4d254/src/pages/index.js)

Gatsby 为 GraphQL 提供了一个整洁的图形界面，名为**graph QL**，可以在[http://localhost:8000/_ _ _ graph QL](http://localhost:8000/___graphql)访问。您可以输入查询，查看哪些字段可用，以及查询调用返回哪些数据。

<figure>

[![GraphiQL screen](img/26c1ac6aada4eb9fa1799b7e1a22e39e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ooEsqXK_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hgl92jtzrz0esk067mky.png)

<figcaption>GraphiQL, in-browser IDE to explore your site's data and schema</figcaption>

</figure>

我做的最后一件事是将`import { graphql } from 'gatsby'`添加到我的 LandingPage 组件的顶部，因为我得到了一个警告，全局`graphql`标记已被否决。

**结果:**

<figure>

[![Landing Page](img/6d57d89a1c6924ec461bbf0785528bfc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6V7gbY3F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xtnfb4wc6hyzu79xtv02.png)

<figcaption>Landing page – http://ekaaa.me</figcaption>

</figure>

💡要了解如何使用 GraphQL 查询数据，请查看本教程。

#### 3)博文页面

受[一些](https://keithjgrant.com) [个人](http://adactio.com) [网站](http://andy-bell.design/)我喜欢的启发，我的帖子将分为**三种**不同的**帖子格式**:

*   **文章** —像这样严肃的长文
*   **链接** —指向外部网页的链接
*   **注释** —在社交媒体状态中快速随意更新(这应该是默认的，即如果未指定帖子格式，帖子应被视为“注释”)

我想对博客文章路径使用文章格式，例如:

*   ekaaa.me/articles/serious-tech-article
*   ekaaa.me/links/link-to-interesting-article-john-doe
*   ekaaa.me/notes/back-from-vacation

**第 0 步:**将`post_format: articles`添加到一个帖子的正面进行测试。

**步骤 1:** 在两个地方将`post_format`添加到`gatsby-node.js`——在`graphql`对象中，以及在`createPage`方法中。这确保了在包含文章格式的新路径上生成博客文章页面。

```
// gatsby-node.js

// generate blog posts
posts
  .forEach((post, index, posts) => {
    // ...
    const post_format = post.node.frontmatter.post_format || 'notes'; // if post_format is not filled, define it as "notes"
    createPage({
      path: post_format + '/' + post.node.frontmatter.slug, // instead of ekaaa.me/post-title, now we have eg. ekaaa.me/notes/post-title
      component: BlogPostTemplate,
      // ...
    })
  }) 
```

Enter fullscreen mode Exit fullscreen mode

**步骤 2:** 将`post_format`添加到所有显示博文的模板和组件中。这确保*所有链接都指向新路线*。点击每个链接查看代码。

*   [components/posts list . js](https://github.com/ekaoddlass/ekaaa/blob/4d43b33d57f9c2c9c3d5c4b01e3e0befb5a4d254/src/components/PostsList.js)—给发送到`PostsListItem.js`的道具添加 post_format
*   [components/postslistitem . js](https://github.com/ekaoddlass/ekaaa/blob/4d43b33d57f9c2c9c3d5c4b01e3e0befb5a4d254/src/components/PostsListItem.js)—添加 post_format 链接`to`属性
*   [components/related posts . js](https://github.com/ekaoddlass/ekaaa/blob/4d43b33d57f9c2c9c3d5c4b01e3e0befb5a4d254/src/components/RelatedPosts.js)—相同
*   [pages/index.js](https://github.com/ekaoddlass/ekaaa/blob/4d43b33d57f9c2c9c3d5c4b01e3e0befb5a4d254/src/pages/index.js) —相同
*   [templates/blog-list-template . js](https://github.com/ekaoddlass/ekaaa/blob/4d43b33d57f9c2c9c3d5c4b01e3e0befb5a4d254/src/templates/blog-list-template.js)—为发送到`PostsList.js`的`posts`对象的 graphql 查询添加 post_format
*   [templates/tag.js](https://github.com/ekaoddlass/ekaaa/blob/4d43b33d57f9c2c9c3d5c4b01e3e0befb5a4d254/src/templates/tag.js) —相同

<figure>

**结果:**
[![Blog Post Page](img/ea05a76331f92dad788cd88526a03b73.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wTuZ6_ln--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3ith466jww2rz2twmi14.png)

<figcaption>Blog Post page — ekaaa.me/articles/fix-missing-npm-os-high-sierra</figcaption>

</figure>

## 现在就这些！

我还在完成这个网站的中途，但我现在就要结束了。总而言之，通过修改一个入门站点来创建一个 Gatsby 站点是快速入门和学习各种特性的好方法。[这是这篇文章的提交](https://github.com/ekaoddlass/ekaaa/tree/4d43b33d57f9c2c9c3d5c4b01e3e0befb5a4d254)。

我想做一些相关的修改，比如*“按格式列表”*(ekaaa.me/articles 等)*“年/月存档”*页面。然而，我现在没有精力也没有时间去做这件事，所以我会推迟到明年。😉

<figure>

[![GIF of a person falling asleep onto the desk](img/4eefba6923eba6c1a6e3ae01cd221ac9.png)](https://i.giphy.com/media/lJnAXeJO8tE7E37mxq/200w_d.gif)

<figcaption>Soooo tired</figcaption>

</figure>

明天我们将为 UI 组件开发和附加库设置 Storybook。再次，如果你读到这里，感谢你的阅读！

* * *

## 参考文献

*   [反应文件](https://reactjs.org/docs)
*   [盖茨比文件](https://www.gatsbyjs.org/docs)
*   [盖茨比入门库](https://www.gatsbyjs.org/starters/?v=2)
*   [图形 SQL 文件](https://www.graphql.com/tutorials)
*   [如何绘制 SQL-full stack 教程](https://www.howtographql.com/)