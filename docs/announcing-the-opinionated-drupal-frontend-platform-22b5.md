# 宣布固执己见的 Drupal 前端平台

> 原文：<https://dev.to/compony_io/announcing-the-opinionated-drupal-frontend-platform-22b5>

默认情况下，Drupal 是一个基于组件的系统。对于前端开发人员来说，以组件的方式工作已经万事俱备，但是我们几乎从来没有这样做过。

Compony 是 Drupal 的组件特性和现代前端之间的桥梁。它灵活地做到这一点，并要求开发人员具有基于组件的思维。

或者就像《牛津词典》会以一种非常隐晦的方式说的那样:
**“由金属和颜色、颜色和皮毛、或者皮毛和金属交替组成的纹章学”**

# **问题**

我们前端的一切都遵循组件的逻辑。但是当我们将前端逻辑插入 Drupal 时，我们编写基于组件的代码的所有奇迹都消失了。这是由于 Drupal 内部的一些限制(以及我们作为前端 Drupal 开发人员的思维方式):

*   我们编写基于组件的 Sass，但是我们将所有的 CSS 编译到一个文件中。
*   我们编写特定于组件的 JS，但是我们在每个页面上加载每一行 JS。
*   我们有 Drupal 库 api，但是我们把所有的资产放在一个全局库中。
*   我们都在对相同的 Drupal 模块的输出进行主题化，但是我们无法在彼此之间共享我们的解决方案。
*   我们制造了漂亮的项目特定风格的组件，但是我们不能把它插入到另一个项目中。
*   我们都喜欢 Drupal，因为它是开源的协作，然而当我们谈到主题设置中的前端组件或工具时，协作似乎停止了。

# **公司解决方案**

 **该解决方案分为三个主要部分:

1.  公司主题

2.  公司咽了口唾沫

3.  公司平台

## 公司主题

Compony-theme 是一种主题结构，它将 Drupal-theme 转换成一种结构，在这种结构中，一切都成为一个组件。你可以从 Compony 平台下载主题。

接下来是一些 PHP 函数，它们使得你可以在主题中进行的所有主题化完全基于组件。没什么特别的，旁边是你必须遵循的某个文件夹结构。(文档:[文件夹结构](https://www.compony.io/docs/documentation/required-structure)

在名为 components 的文件夹中，您可以放置您的组件。(文档:[组件结构](https://www.compony.io/docs/documentation/concept))(组件示例:[文本下拉](https://www.compony.io/component/text-dropdown))

## 公司咽了口唾沫

创造一个最先进的吞咽装置会导致令人挠头的秃顶。

因此，我们为这个确切的复合主题创建了一个定制的开源 Gulp 设置。Gulp 的设置是自以为是的，但是可以根据你的项目和以多种方式运作的开发团队的需求进行定制。在同一个项目中，它可以同时处理多个主题，不同操作系统中的多个开发环境。

它甚至可以手动调整，知道在您工作时何时为您清除 Drupal 的缓存。(文件:[该组件吞咽](https://www.compony.io/docs/documentation/compony-gulp)

当从主页下载主题时，吞咽设置已经包含在主题中。

## 复合平台

Compony 平台使社区能够相互共享他们的前端组件。它还是我们联系、贡献、讨论和发展不断发展的前端标准的中心。

# **逐渐将所有这些集合在一起**

按照渐进增强的逻辑，可以只使用 Compony 的某些部分。

## 背叛者的装置

Drupal 中只使用 Compony 主题，不使用平台上的组件，也不使用 Gulp 设置。Drupal 不需要 Gulp 就能正确理解你的主题。

这意味着您也可以在您的服务器上使用这种方法。Gulp 只是为了前端层的开发而存在。

## 前端的设置

你的主题的前端既要有复合主题，也要有复合大口。因为吞咽能使那个人工作得更快更可靠。

## 社区驱动的设置

如果你想从社区创建的组件中受益，你可以使用这个平台从其他人那里浏览和下载组件。当使用其他组件时，您还可以通过修复 bug 或进行增强来对您已经使用过的组件做出贡献。

在平台上你可以创建组件(blog post:[Create a Compony component](https://www.compony.io/blog/create-compony-component))供他人使用。或者您可以收集组件，然后下载并拖放到您的主题中。(收藏示例:[裸照必备品](https://www.compony.io/collection/bare-essentials))

# 听起来很有趣？

你可以免费使用这个平台，但我们也有一些付费功能。
继续[注册你的账户](https://www.compony.io/user/register)**