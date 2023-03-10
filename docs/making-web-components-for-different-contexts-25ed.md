# 为不同的上下文制作 Web 组件

> 原文：<https://dev.to/equinusocio/making-web-components-for-different-contexts-25ed>

本文不是关于如何构建 web 组件。凯勒·威廉姆斯最近已经就此写了一个全面的指南。让我们来谈谈如何与它们合作，制作它们时需要考虑什么，以及如何在您的项目中拥抱它们。

如果您是 web 组件的新手，Caleb 的指南是一个很好的读物，但是这里有更多的资源可以帮助您快速上手:

*   [Web 组件——正确的方式](https://equinsuocha.io/blog/web-components-the-right-way)
*   [Shadow DOM v1:自包含 Web 组件](https://developers.google.com/web/fundamentals/web-components/shadowdom)
*   [MDN 上的 Web 组件](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
*   [令人惊叹的网络组件](https://github.com/mateusortiz/webcomponents-the-right-way)
*   [打开 Web 组件建议](https://open-wc.org/)

由于 web 组件现在得到了广泛的支持(这要归功于幕后许多人的辛勤工作)，并且考虑到 Edge 即将对 chromium 平台进行的[转变](https://css-tricks.com/edge-goes-chromium-what-does-it-mean-for-front-end-developers/)，人们现在认为 web 组件是“原生的”,是一种平台兼容的方式来构建可重用的 UI 组件，以保持跨设计系统和 web 项目的一致性，同时使用 Shadow DOM 的能力来封装组件本身内部的样式和逻辑。

嗯，这个可以是真的，也可以是假的。但是首先，让我们熟悉一下**抽象层三角形**。

## 抽象层三角形

从技术上讲，我们应该把 web 组件看作是我们最喜欢的标记语言 HTML(是的！). [Web 组件 API](https://developer.mozilla.org/en-US/docs/Web/Web_Components) 允许我们创建 HTML 中不存在的自定义 HTML 元素(例如`<foo-bar>`)。

我们被告知 web 组件基本上是新的 HTML 元素，所以我们应该将它们视为 HTML 规范的一部分，因此，我们应该遵循它的范例、核心概念和应用。如果我们假设所有这些点，我们将会发现我们的组件将与 HTML、CSS 和 JavaScript 一起生活在 web 平台堆栈的最底层。

像 React、Vue、Angular、SvelteJS 这样的框架和库在它们的抽象层次上工作，就在其他生活在“中土世界”的工具之上，比如 StencilJs、Hybrids 和 Lit。在这些抽象层下，我们可以找到我们的基本 web 技术…和普通的 web 组件。我们可以用一个**ALT**(**A**b traction**L**Ayers**T**riangle)图来表示这个概念:

[![ALT](img/5a06edca0b258449f5aa28366d30ffec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L7_cxD-U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://css-tricks.com/wp-content/uploads/2019/04/s_5B36036EA3CCD2962258CADABD44B400D52827409ACCBE3D636781BACC2B8EDC_1554105948178_al.svg)

为什么这很重要？嗯，它帮助我们可视化存在于本地组件之上的各种层，并理解它们被使用的上下文，以便它们可以被构建用于预期的上下文。什么是语境？那就是我们要去的地方。

## 同样的技术，不同的语境

影子 DOM 是 Web 组件 API 中的一个关键因素。它允许我们将 JavaScript 和 CSS 捆绑在自定义元素中，以防止外部干扰和样式操作，除非我们明确允许这样做。开发人员确实可以遵循一些方法来允许外部 CSS 泄漏到影子根和组件中，包括自定义属性和`::part`和`::theme`伪元素，这是[莫尼卡·丁库莱斯库](https://twitter.com/notwaldorf)已经[很好地覆盖了](https://meowni.ca/posts/part-theme-explainer/)的东西。

还有另一件事需要考虑:我们工作的环境。在我个人构建 web 组件三年后，我可以识别两种上下文:私有上下文(像设计系统一样)和标准上下文(像普通的 HTML、CSS 和 JavaScript，没有定制样式)。

在设计组件之前，我们需要知道它们将如何被使用，因此确定上下文的类型是所有这一切的关键。最简单的方法是只针对一个上下文，但是使用一个小的 CSS 技巧。我们可以为这两者构建我们的组件。

在我们进入这个话题之前，让我们先来看看这两种语境之间的区别。

### 私人语境

一个**私有环境**是一个封闭的生态系统，它为组件提供了它们自己的风格，必须按原样使用。因此，如果我们正在构建一个来自特定样式指南或设计系统的组件库，每个组件都将反映自定义样式，因此没有必要在每次需要它们时都进行编码。

JavaScript 逻辑也是如此。例如，我们可以附加一个封闭的阴影根，防止其他人意外地用`querySelector`穿透阴影边界。因此，我们可以简单地选择和使用所有组件，避免风格不一致和 CSS 冲突等问题。作为作者，您还可以定义一组 CSS 自定义属性(或`::parts`)，用于为特定用例设计组件的样式，但这不是设计系统的重点。

下面是一个私有上下文中的 web 组件的例子。它的`shadow-root`和中包含了所有的样式和逻辑，可以简单地放到任何页面中。

[https://codepen.io/equinusocio/embed/WWbQdP?height=600&default-tab=html,result&embed-version=2](https://codepen.io/equinusocio/embed/WWbQdP?height=600&default-tab=html,result&embed-version=2)

> 这个例子和接下来的例子是为了演示，而不是为了生产使用，因为它们没有考虑关键情况，比如可访问性和其他优化。

私有上下文中的组件很少能在该上下文之外使用。例如，如果我们试图从一个设计系统中获取一个元素(它有自己的强制样式)，我们不能简单地将它添加到一个项目中，并期望定制它。你知道 Bootstrap 可以根据你的喜好进行主题化和定制化吗？这几乎是相反的。这些组件只存在于它们的上下文中。

### 标准上下文

一个**标准**上下文可能是最复杂的组件类型，不仅仅是因为环境的范围可以从成熟的框架(像 Vue 和 React)到普通的 HTML，还因为每个人都应该能够像使用任何其他元素一样使用那个组件。

例如，当公开添加一个组件时，比如说 npm，那些使用它的人会期望能够定制它，至少在某种程度上。

你知道有什么 HTML 元素有自己的表现风格吗？答案应该是否定的，因为元素必须用 CSS 进行显式样式化。对于在标准上下文中制作的 web 组件也是如此。单个 web 组件应该可以通过添加类和属性或其他方法来定制。

这是我们在封闭上下文示例中看到的相同的`<todo-list>`元素，但是是为标准上下文设计的。它按原样工作，在其影子根中没有任何表示样式。事实上，它只包含确保正常运行所需的逻辑和基线 CSS。否则，它完全可以像任何标准的 HTML 元素一样定制，比如 div。

[https://codepen.io/equinusocio/embed/vMOqBO?height=600&default-tab=result&embed-version=2](https://codepen.io/equinusocio/embed/vMOqBO?height=600&default-tab=result&embed-version=2)

我们在每个上下文中看到的两个例子都是由相同的组件构成的。不同之处在于，标准上下文中的组件可以通过外部 CSS 进行定制和选择。

## Web 组件和组成

好的，所以使用 web 组件实际上和使用普通的 HTML 是一样的，尽管正如我们所看到的，遵循给定内容的范例和原则是很重要的。我们需要注意的是 web 组件的组合。

正如[谷歌网络基础](https://developers.google.com/web/fundamentals/web-components/shadowdom#composition_slot)所解释的:

> 合成是 shadow DOM 最不为人所知的特性之一，但无疑是最重要的。
> 
> 在我们的 web 开发世界中，组合是我们构造应用程序的方式，以声明的方式脱离 HTML。不同的积木(`<div>` s，`<header>` s，`<form>` s，`<input>` s)组合在一起形成应用程序。其中一些标签甚至可以相互配合使用。构图就是像`<select>`、`<details>`、`<form>`、`<video>`这样的本土元素如此灵活的原因。这些标签中的每一个都接受某些 HTML 作为子标签，并对它们做一些特殊的事情。例如，`<select>`知道如何将`<option>`和`<optgroup>`渲染成下拉和多选小部件。元素将`<summary>`呈现为一个可扩展的箭头。甚至`<video>`也知道如何处理某些孩子:`<source>`元素不会被渲染，但它们会影响视频的行为。多么神奇！

当我们使用 HTML 时，通常会进行组合。由于 web 组件仅仅是带有 DOM 引用的 HTML 元素——而不是逻辑容器——我们应该依靠组合来构建我们的组件和任何子组件。如果您考虑一下`ul`和`select`，您会注意到您声明性地组合这些元素以获得最终输出，并且您有特定的属性用于主组件(例如`[readonly]`)或子组件(例如`[selected]`)。对于 web 组件也是如此，如果你正在构建一个定制列表，考虑构建主组件(`<custom-list>`)和子组件(`<custom-li>`)。[使用`[slot]`元素](https://css-tricks.com/extracting-text-from-content-using-html-slot-html-template-and-shadow-dom/)，您可以定义放置子元素的位置，以及没有子元素通过时显示的占位符内容。

## Web 组件和可访问性

另一个要考虑的是我们称之为可访问性的“小”主题。由于我们正在创建全新的 HTML 元素，我们需要考虑元素的可访问性，以便提供基本的语义角色、任何键盘导航以及用户的操作系统偏好，如[减少运动](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion)和[高对比度](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/-ms-high-contrast)设置。

我强烈建议参考以下资源来构建可访问和包容的组件，如何定义语义标记，以及如何实现基本的键盘导航。

*   [包含组件](https://inclusive-components.design/)
*   [在 web.dev 上对所有人开放](https://web.dev/accessible)
*   [WAI-ARIA 创作实践](https://www.w3.org/TR/wai-aria-practices-1.2/)
*   [网络目标 WCAG 清单](https://webaim.org/standards/wcag/checklist)

## 结论

Web 组件是 web 开发中的一项新兴技术，正因为如此，目前还没有任何明确定义的最佳实践来指导我们构建它们，以实现它们的预期用途或最大化用途。当你发现自己在和他们一起工作时，也许你能从这篇文章中学到的唯一一件事就是考虑他们是用于封闭环境还是标准环境，然后问问你自己 **WHI** :

*   谁会使用这个组件？
*   那个人应该有多大的灵活性来定制它？
*   这个组件是面向所有人还是面向特定受众？