# 静态网站渲染征服一切

> 原文：<https://dev.to/nikoheikkila/static-site-rendering-conquer-all-218i>

最近，我开始将我的博客从一个旧的但并不脆弱的 WordPress 系统迁移到 [*盖茨比*](https://gatsbyjs.org) 。迁移进行得很顺利，避免了由于 DNS 配置错误而导致的短暂停机。现在，结果就在你眼前。不过，让我们稍后再讨论变化。

## 为什么从 WordPress 迁移

我是 WordPress 的长期拥护者，因为使用它发布网站非常容易。自从一种被称为*静态站点发电机*的现象在几年前开始浮出水面，并且*在前面，我开始考虑迁移。2019 年伊始，我决定全力以赴。我厌倦了查看 WordPress 的更新，因为有一个诱人的替代选择。*

 *我曾经和 Jekyll 一起建立过几个网站，尽管我在那个环境中从来没有感觉太舒服。这很可能是因为构建过程非常繁重，而且我通常不用 Ruby 开发其他东西。还有其他强有力的竞争者，如*[*雨果*](https://gohugo.io)[*拼图*](https://jigsaw.tighten.co) 等等。出于这样或那样的原因，他们没有把我争取过来。*

 *另一方面，盖茨比是用 JavaScript 构建的，由 *React* 提供支持。React，作为[街区](https://dev.to/search?q=gatsby)上的酷小子，与来自 Vue.js 的一些令人钦佩的竞争，提供了一种以*不要重复自己*(干)心态构建可重用 HTML 元素的好方法。这些被称为组件，你可以利用它们来构建你的网站。结合 Gatsby 提供的大量插件和优秀文档，我们在 2019 年成为最佳静态网站生成器的竞赛中显然是赢家。

下面的示例片段显示了一个自定义组件渲染器，如果我选择发布一个不是用英语写的帖子，它会输出一个到 Google 翻译页面的链接。不要害怕 JSX 语法。习惯了之后还是挺舒服的。

```
render() {
    const { lang, url } = this.props
    return (
        <section className="language-info">
            <p>
                This post was written in {this.getLanguage(lang)}.
                You can access the machine translated version{'  '}
                <OutboundLink
                    href={url}
                    target="_blank"
                    rel="noopener noreferrer"
                >
                    here
                </OutboundLink>.
            </p>
        </section>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，看看组件如何包含其他组件，如`<OutboundLink>`。这使得用户界面的组合变得轻而易举。

与传统的动态 web 应用程序相比，用 Gatsby 构建您的博客有许多显著的优势:

*   你的博客变成了一个单页应用程序(SPA ),这意味着尽管成百上千的帖子分散在不同的 URL 上，但网站感觉像一个统一的页面。
*   所有内容都在您的存储库中。您再也不用担心数据丢失，除非您喝醉了，决定删除您的远程存储库及其所有本地克隆——在这种情况下，您的互联网许可证将被无限期吊销。
*   Markdown 是我每天用来记录工作的最纯粹、最漂亮的写作形式。重要的是，你不必去弄乱 WordPress HTML 块或者新的 Gutenberg 编辑器。
*   构建流程缩小并捆绑您的资产，以实现最小占用空间。
*   部署可以通过持续集成管道或者像我一样通过 [ZEIT Now](https://zeit.co/dashboard) 无服务器服务(没有双关语)来实现自动化。
*   你的博客变得*快如闪电*，因为内容是普通的老式 HTML，带有一些 CSS 和 Javascript 资产，最好通过 CDN 网络提供。

将所有内容存储在版本控制中不可避免的代价是，您必须编辑原始文本文件，并将其推送到远程存储库，以备不时之需。这是非常可以接受的，因为我和许多其他开发人员最习惯在代码编辑器中编辑文本。

对于其他内容创建者，需要一个发布前端。这是 Gatsby 的另一个亮点:有大量的源代码插件可以从包注册表中获取。这意味着你可以，例如，使用 [Netlify CMS](https://www.gatsbyjs.org/docs/sourcing-from-netlify-cms) 或者甚至从一个无头 [WordPress 安装](https://www.gatsbyjs.org/packages/gatsby-source-wordpress/?=wordpress)中提取内容。

## 发生了什么变化

我保留了旧网站的风格，但在一些地方做了小小的调整。Gatsby 模板通常附带一种所谓的 *CSS-in-JS* 方法，通过向 React 组件传递一个`style`属性(对象)来声明您的样式。作为一个将表示和逻辑分开的爱好者，我更喜欢用 Webpack 来编译 SCSS 的风格。

最值得注意的定制是传说中的**黑暗模式**。去点击这个帖子上面有太阳和月亮图标的切换按钮，然后离开这个网站，再回来。你将再次受到欢迎，一个更好看的深色主题将会保存到你的浏览器的本地存储器中。这个功能的荣誉归于 [**丹·阿布拉莫夫的**博客](https://overreacted.io/)，我从那里移植了这个功能。

另一个新增功能是帖子页脚，提供了 Twitter 讨论和 GitHub 编辑的链接。由于这个博客现在是完全开源的，每篇文章都可以被读者编辑。只需单击一个链接，派生出存储库，并提交一个拉请求。除了帖子之外，我们也欢迎其他方面的投稿。我必须承认我不是最熟练的前端开发人员，因此任何关于 UI 或 UX 的修复都非常感谢。

至于后端系统，这个存储库的每次签入都将启动一个 Travis CI 管道，运行 JavaScript 代码的`eslint` linter 和一组使用`pa11y`的可访问性测试。也有可能为你的 React 组件编写单元测试，但是它包含了太多的[嘲讽](https://medium.com/javascript-scene/mocking-is-a-code-smell-944a70c90a6a)我很快就开始感觉不舒服了。管道在 macOS 和 Linux 平台上并行运行，这对一些人来说可能是过度使用资源，但至少我可以相信我的代码在我的 Windows 10 WSL box 和 Macbook 上都可以工作。

## 结论

我将继续提交小的更新和张贴新的内容到这个博客。如果你喜欢的话，一定要将这个网站加入书签，并启动 [GitHub 库](https://github.com/nikoheikkila/nikoheikkila.fi)。**