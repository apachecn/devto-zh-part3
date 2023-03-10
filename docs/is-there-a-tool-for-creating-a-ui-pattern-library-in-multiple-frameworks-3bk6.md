# 有没有在多个框架中创建 UI 模式库的工具？

> 原文：<https://dev.to/ekafyi/is-there-a-tool-for-creating-a-ui-pattern-library-in-multiple-frameworks-3bk6>

这个问题困扰了我一段时间…最终我抽出时间把它写了下来，哪怕只是为了把它从我的胸口赶走！

#### 摘要/TLDR

*   有没有一个**工具**更容易创建/建立一个**【模式库】**来文档化和开发多个框架 (HTML、WebComponent、React、Vue 等)中的 **UI 组件？**

#### 定义

我使用术语*“模式库”*来指代 UI 组件的集合，它是*设计系统*的子类，如下文所述。

*   [系统设计综合指南](https://www.invisionapp.com/inside-design/guide-to-design-systems/)
*   设计系统 vs .模式库 vs .风格指南——有什么区别？

为了简洁起见，我不严格地使用了术语*“框架”*，因此包括了 React(一个 UI 库)和 HTML。它指的是任何呈现 UI 组件的方式。

#### 灵感

[IBM 的 Carbon 设计系统](https://www.carbondesignsystem.com/resources/)有香草(HTML+SCSS+JS)、React、Angular、Vue 的 UI 组件！🤯

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [碳设计系统](https://github.com/carbon-design-system) / [碳](https://github.com/carbon-design-system/carbon)

### 由 IBM 建立的设计系统

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Carbon Design System](img/a8d2ef312a45f88dac931fd7df450c7f.png)](https://www.carbondesignsystem.com)

# 碳设计系统

> Carbon 是 IBM 构建的一个开源设计系统。该系统以 IBM 设计语言为基础，由工作代码、设计工具和资源、人机界面指南以及活跃的贡献者社区组成。

[![Carbon is released under the Apache-2.0 license](img/7725dbf31c2e46476ed904eba4db9757.png)](https://github.com/carbon-design-system/carbon/blob/master/LICENSE)[![Build Status](img/1cf79b4ba284f4893dfe9c7e729c0568.png)](https://circleci.com/gh/carbon-design-system/carbon)[![Maintained with Lerna](img/fd8983decc3fc5bab4a2d10c5def50a9.png)](https://lerna.js.org/)[![PRs welcome](img/6f43bb6ec23adbd1102c103e63a75b84.png)](https://github.com/carbon-design-system/carbon/blob/master/.github/CONTRIBUTING.md)[![Chat with us on Gitter](img/b5769fe3df12422901d1971afd2b65bb.png)](https://gitter.im/carbondesignsystem/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

## 入门指南

如果你刚刚开始，看看 [`carbon-components`](https://raw.githubusercontent.com/carbon-design-system/carbon/master/./packages/components) 。如果你正在寻找 React 组件，看看 [`carbon-components-react`](https://raw.githubusercontent.com/carbon-design-system/carbon/master/./packages/react) 。

我们还有针对以下技术的社区贡献组件:

*   [角度](https://github.com/IBM/carbon-components-angular)
*   视图

如果你想找一些特定的东西，这里有我们支持的软件包的完整列表！

| 包名 | 描述 |
| --- | --- |
| [T2`carbon-components`](https://raw.githubusercontent.com/carbon-design-system/carbon/master/./packages/components) | 组件样式和普通 JavaScript |
| [T2`carbon-components-react`](https://raw.githubusercontent.com/carbon-design-system/carbon/master/./packages/react) | 反应组分 |
| [T2`@carbon/elements`](https://raw.githubusercontent.com/carbon-design-system/carbon/master/./packages/elements) | IBM 设计语言元素，如颜色、类型、图标等等 |
| [T2`@carbon/colors`](https://raw.githubusercontent.com/carbon-design-system/carbon/master/./packages/colors) | 使用 IBM 设计语言颜色 |
| [T2`@carbon/grid`](https://raw.githubusercontent.com/carbon-design-system/carbon/master/./packages/grid) | 使用新的 16 列网格系统构建布局 |
| [T2`@carbon/icons`](https://raw.githubusercontent.com/carbon-design-system/carbon/master/./packages/icons) | 图标资产。我们还在以下方面提供支持:[反应](https://raw.githubusercontent.com/carbon-design-system/carbon/master/./packages/icons-react)、[角度](https://github.com/carbon-design-system/carbon-icons-angular)和 [Vue](https://raw.githubusercontent.com/carbon-design-system/carbon/master/./packages/icons-vue) |
| [T2`@carbon/pictograms`](https://raw.githubusercontent.com/carbon-design-system/carbon/master/./packages/pictograms) | 象形图资产。我们也提供支持 |

…</article>

[View on GitHub](https://github.com/carbon-design-system/carbon)

[Github 的引物](https://primer.style/)提供了[引物 CSS](https://primer.style/css) 和反应模式库[引物组件](https://primer.style/components)。它还提供了纯 HTML 中[组件的实现。](https://styleguide.github.com/primer/components/)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [引物](https://github.com/primer) / [组件](https://github.com/primer/components)

### 引物反应组分

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/d1ae7468fd9545d9d64e7903cb1cd0f0.png)](https://raw.githubusercontent.com/primer/components/master//static/assets/readme-components.png)

# 底漆成分

反应底漆设计系统的组件

[![](img/795fa871cbaff47f8b872169ff0289bd.png)](https://www.npmjs.com/package/@primer/components)[![](img/5d30101d3af7327d99ea5f887e7dd7fc.png)](https://github.com/primer/components/graphs/contributors)[![](img/90d8499334b81db5f3612bf826f6f490.png)](https://github.com/primer/components/commits/master)[![](img/06448a6c30529fa029751a272e97ea9d.png)T11】](https://github.com/primer/components/blob/master/LICENSE)

## 证明文件

我们的文档站点位于[primer.style/components](https://primer.style/components)。您将能够找到详细的入门文档、所有组件、我们的主题、我们的原则等等。

## 装置

使用您选择的包管理器在您的项目中安装[@ primer](https://dev.to/primer)/组件:

```
npm install @primer/components

// or

yarn add @primer/components 
```

## 贡献的

我们喜欢与 GitHub 内外的人们合作，欢迎投稿！

> <g-emoji class="g-emoji" alias="point_right" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f449.png">👉</g-emoji>参见[贡献文档](https://raw.githubusercontent.com/primer/components/master/CONTRIBUTING.md)了解更多关于代码风格、测试、覆盖和故障排除的信息。

</article>

[View on GitHub](https://github.com/primer/components)

最后， [Salesforce 的 Lightning 设计系统](https://lightningdesignsystem.com/)提供了 [HTML+CSS](https://www.lightningdesignsystem.com/components/avatar/) 和 [WebComponent](https://developer.salesforce.com/docs/component-library/bundle/lightning-avatar/documentation) 的用法。

#### 理&题

手边有一个 UI 模式库——结合像 [Storybook](https://storybook.js.org) 这样的工具——加速原型、设计、开发，甚至文档。然而，像 Storybook 和 Patternlab 这样的工具只允许你在一个框架或普通的 HTML 中工作。(故事书*支持许多框架，但你必须建立单独的项目。)*

 *为什么不坚持一个框架呢？你(即。您的组织或客户)可能有多种产品需要不同的堆栈，或者您可能想尝试新的堆栈。如果你是一个小的产品团队中唯一的前端开发人员或者自由职业者，你可能没有资源去开发多个独立的代码库来记录你的组件。

*   我们如何使在多个框架中开发变得更容易——从而减少对一个特定库的过度依赖？
*   HTML 是网络的基本构件，所以 UI 组件*必须*在普通 HTML 中可用。另一方面，一些开发人员可能会发现在 React 中构建原型更容易/更快。我们如何鼓励两个世界都得到最好的，而不把它变成一个“对抗”的事情？
*   我们如何跨框架简化组件开发和文档？我们如何确保跨框架以一致的方式开发 UI 组件？(元素、类名、属性和道具等的选择)

#### (模糊的)想法&进一步提问

**免责声明:**我是初学者，还没有完全掌握前端工装的复杂性；就我所知，我只是在考虑各种可能性。

*   设置一个包含多个包的[纱线工作区](https://yarnpkg.com/en/docs/workspaces)。一个包出口品牌/基础设计令牌。我们在一个“主要”组件包中编写组件(在 WebComponents 中，因为它最接近本地 web 平台？还是用香草 JS 因为很容易转换成其他格式？);使用 bundler/编译器/transpiler(？)导出为多种格式。
*   然后从单独的包中，导入那些可以进一步修改的组件。每个包都有相似的文件夹结构，每个框架都有故事书设置。(有没有某种“生成器”可以自动生成这个？)
*   使用 Sass 萨斯作为真理的单一来源。(在某种程度上，我已经在这么做了——我为 React Storybook 模式库*和 Laravel web 应用程序*使用了相同的 CSS。)
*   输出代码的设计工具(Sketch，Figma，Zeplin)在这里面扮演什么角色？他们应该是真理的来源吗？(即。应该开发它们来输出多种框架的代码吗？)
    *   从设计到代码的工具似乎得到了很多宣传，我猜它对于面向视觉的设计师和开发人员来说是理想的(不幸的是我不是)——但是这意味着你需要从这些工具中进行开发？🤔

* * *

我很想知道是否有人对这个话题有经验或见解。*