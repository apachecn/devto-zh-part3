# Harp 项目组织

> 原文：<https://dev.to/megazear7/harp-project-organization-3cd>

我已经在静态站点生成器 [Harp](http://harpjs.com/) 上写了一系列博文。我想通过谈论 [Harp](http://harpjs.com/) 中的项目组织来结束这一系列的博客文章。在这篇博客之后，我会再写一篇关于这个工具的总结性想法。如果 Harp 对你来说是全新的，首先阅读[开始使用 Harp](https://www.alexlockhart.me/2018/12/getting-started-with-harpjs.html) 可能会有所帮助。否则，请继续阅读，我将解释每个项目需要涵盖的一些基础知识，然后在最后，我将回顾我为自己开发的一些概念，这些概念极大地帮助了我组织我的项目。

## 站点内容

在我之前的博客文章中，[在 Github 页面上托管 Harp](https://www.alexlockhart.me/2018/12/hosting-harp-on-github-pages.html)，我们决定在项目的根目录下创建两个目录:

*   我的项目/
    *   src/
    *   文档/

正如那篇博文中所解释的，这允许我们将 src 代码编译到 docs 文件夹中，可以在任何 web 服务器上使用。这使得我们很容易在 Github 页面上托管，但在其他 web 服务器上托管 docs 目录也是一个相当类似的过程。

#### 元数据

在 src 文件夹内，我们将添加顶级网页，并开始与子目录的网站层次结构。这些目录中的每一个都应该包含一个 _data.json 文件，该文件可用于创建上下文特性，如 Harp 博客文章中的 [Breadcrumbs 组件所述。](https://www.alexlockhart.me/2019/01/breadcrumbs-component-in-harp.html)

## 资产

除此之外，我们还想创建一个“/my-project/src/assets”目录，其中可以包含共享的 JavaScript、CSS 和图像。在这个目录中，我有一个 main.js 文件和一个 main.css 文件。

#### 图像

对于图像，我建议创建指定图像尺寸的文件夹。在我的 Harp 网站示例中，我有以下两个包含我的图像资产的目录:

*   /src/资产/图像/4_3
*   /src/asseimg/16_9

这样，当我在其他地方引用图像时，我知道它的尺寸，并且可以通过更改路径中的部分来指定我想要使用的图像版本。

## 结构

到目前为止，这相当简单。然而现在我想解释一些我在使用 [Harp](http://harpjs.com/) 时为自己开发的概念。首先创建一个名为“/my-project/src/_structure”的目录。下划线告诉 harp 不要编译这个目录的内容。在这个目录中，我添加了符合以下标准的 [EJS](https://ejs.co/) 分部:

*   该结构目录中的部分应该使用对上下文数据源的直接引用，例如`public._data.title`和`current.path`。
*   结构目录*中的 Partials 不应该*只依赖于从 partials 参数传入的数据。

这些部分可以用来在你的站点上提供上下文特征，并且对于提供诸如页眉、页脚、导航和面包屑之类的东西来说是非常强大的。然而，它们依赖于 _data.json 文件中提供的元数据结构，并且它们的行为取决于它们的放置位置。

## 组件

组件的想法是我针对如何使用 [Harp](http://harpjs.com/) 开发的另一个概念。让我们再次从创建一个名为“/my-project/src/_components”的目录开始。下划线再次表示这些文件不会被渲染。在这个目录中，我们可以添加符合下列标准的 [EJS](https://ejs.co/) 分部。这基本上是与上一节相反的标准。

*   组件目录中的 Partials 应该只依赖于从 partials 参数传入的数据。
*   该组件目录*中的部分不应*直接引用任何上下文数据源，如`public._data.title`和`current.path`。

这些部分，我称之为组件，被设计成可以在站点的任何地方使用，并且总是具有相同的行为。它们不依赖于 _data.json 中提供的元数据。但是，它们与上一节中的结构 partials 相反，因为它们需要通过 partials 参数提供任何数据。

## 元素

最后一个[竖琴](http://harpjs.com/)项目组织概念是以元素的形式出现的。这是我给“/my-project/src/elements”目录中的文件起的名字。注意，这里没有下划线，所以这些文件将被呈现。我在这个目录中添加了 JavaScript 文件，每个文件都实现了一个 web 组件。我从 main.js 文件的 ES6 导入中将这些 javascript 文件包含到页面中。

这让我可以在我的 EJS 模板和片段的 html 中使用这些 web 组件。我可以通过数据属性将元数据传入这些 web 组件。这为实现更复杂的 UI 行为和功能提供了一个入口点。

就是这样！感谢您的阅读，敬请关注我在 [Harp](http://harpjs.com/) 上的最后一篇文章，在这篇文章中，我将对这个静态站点生成器做一个回顾，并提供一些最终的想法。

查看我的博客,了解更多我对技术和其他各种话题的思考。