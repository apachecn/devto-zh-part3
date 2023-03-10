# 今天你可以应用的 5 个代码改进，使你的网站更容易访问

> 原文：<https://dev.to/nothing/5-code-improvements-you-can-apply-today-to-make-your-website-more-accessible-27h5>

[![](img/4f1c322989447f8f8e15ac04af9a5d00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Mkd4GwsF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ArpI2fzRm4bMO1gQi1bkAAg.png)

如果说 web 开发中有一个组件经常被当作一个选项(如果有的话)，那就是可访问性。公司希望从中获得商业价值，但看不到好处；开发人员要么从未听说过 web 的可访问性，要么认为由于时间不够，他们不能在自己的公司中应用可访问性。

根据我的经验，网站和 web 应用程序的可访问性是 80%编写符合标准的 HTML 标记，并考虑到渐进增强和 20%的内容改进。

**这 80%应该是每个前端开发人员都可以做到的**与 HTML，CSS 和 JavaScript 的基础知识。这是关于语义，使用正确的 HTML 元素，应用正确的键盘焦点顺序等等。这项工作的很大一部分可以用像 [aXe](https://github.com/dequelabs/axe-core) 这样的工具自动测试(与谷歌灯塔可访问性测试的引擎相同)。

**最后的 20%**——这也是我称之为内容改进的原因——是大量的“可用性可及性”。它不太技术性，更多的是关于你如何提供你的网站内容，让每个人都明白它。这是关于为屏幕阅读器提供额外的提示，视频中的字幕，使内容在高对比度模式下可读或者用网页代替 pdf(或者用可访问的 pdf 代替)。

当然，设计也是网页可访问性的一个重要部分。在这篇文章中，我想向开发人员展示五个代码改进，他们现在可以应用它们来使他们的网站更容易访问。

**免责声明:**在这篇文章中，我可能会链接到[无障碍开发者指南](https://www.accessibility-developer-guide.com/)的各个章节。不是因为我工作的公司为该指南做出了贡献，而是因为它对这篇文章中的材料提供了更详细的解释，这些材料实际上是经过测试、验证并保持最新的。与官方的 WCAG 规范相比，它们也更容易理解。

## 开始之前:关于 HTML 和 CSS 的基本思维模式

将 HTML 和 CSS 分开处理是很重要的。在代码中使用一个`<a>`元素，然后将其样式化为按钮，这完全没问题。你的 HTML 标记本身应该是有意义的，不需要样式。这就是为什么像 Twitter 的 Bootstrap 这样的库除了默认的元素样式之外，还为各种排版选择提供了[类。我建议尽可能多地使用类，而不是类型选择器](https://getbootstrap.com/docs/4.2/content/typography/)样式。我保证，以后你的生活会轻松得多。

<figure>[![Screenshot of the heading classes in the Twitter Bootstrap 4 documentation](img/7762f282ae25166467a70cf64d8fb494.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iW4Z7WGi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/433/1%2Aq8A54Uhi25YIFE8K7esjMQ.png) 

<figcaption>Twitter 的 Bootstrap 为除 H1 到 H6 之外的元素提供了完全相同样式的附加类。</figcaption>

</figure>

## Nr。1-修复一般代码语义

语义是指一段代码的*含义。MDN——这是我查找网络平台各种特性的最喜欢的来源——[对 HTML、CSS 和 JavaScript 的“语义化”有一个非常好的描述](https://developer.mozilla.org/en-US/docs/Glossary/Semantics)(我建议快速通读参考页面)。*

我在网站和应用程序中遇到的最常见的错误语义案例——尤其是当使用 React、Vue.js 或 Angular 这样的框架时——是将`<div>`元素误用为一切。

**这里有一个简短的案例列表，可以在您的网站或应用程序上查看:**

*   **片头**被包裹在 H1 到 H6 的标签里。这并不意味着每个标题都必须是标题元素。想象一下用微软 Word 写一个文本文档。什么时候你会把一行文字设计成标题？你什么时候会做出大胆的事情？请注意，使用屏幕阅读器的人喜欢使用从 H1 到 H6 的标签来跳转你的内容。相比之下，文本的粗体，即使标记了`<strong>`标签，也不会影响屏幕阅读器访问和阅读内容的方式。
*   **列表**，无论是导航项列表还是这里这样的列表，都定义为[无序列表](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/ul)、[有序列表](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/ol)或[描述列表](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dl)。确保没有只有*一个*元素的列表。例如，如果您的导航在任何情况下都只能有一个元素，请移除列表包装。只有一个元素的列表可能会让使用屏幕阅读器的人感到困惑。
*   **交互元素**或者是[锚点](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/a) ( `<a>`)、[按钮](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button) ( `<button>`)或者是表单元素(如 [`<input>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input) 、 [`<select>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select) )。如果您在像`<div>`这样的元素上发现了类似点击处理程序的东西，那么这个元素很可能就是上面的元素之一。
*   **使用** [**HTML5 section 元素结合咏叹调地标**](https://dequeuniversity.com/assets/html/jquery-summit/html5/slides/landmarks.html) **来标记内容。不是所有的对屏幕阅读器都有影响，但有些有。请参阅参考页面，了解详细的解释以及如何实现。**

这当然不是你能做的全部，但是这些是我在易访问性测试中发现的最常见的错误语义。更详细的解释和例子可以在[易访问性开发者指南](https://www.accessibility-developer-guide.com/knowledge/semantics/)中找到。

## Nr。2 —修正你的标题结构

即使你所有的标题都包含了正确的 H1 到 H6 元素，也并不意味着它们被正确使用了。

检查是否有航向水平间隙:例如，`<h2>`必须跟在`<h1>`后面，`<h3>`必须跟在`<h2>`后面，等等。例如，如果你发现一个孤独的`<h5>`，没有`<h4>`而只有`<h3>`元素，那就有点不对劲了。所以你应该把你的`<h5>`重写为`<h4>`。

有一个[书签](https://www.accessibility-developer-guide.com/setup/browsers/bookmarklets/h123/)可以帮你找到这些问题。

<figure>[![Screenshot of h123 Browser Bookmarklet output](img/7671f2527c509e73e18f3f5a7e10f1ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oGPwpOgL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/448/1%2ATopnw0q77zlTLzk9FkzdJg.png) 

<figcaption>h123 小书签输出为 [gov.uk](https://gov.uk)</figcaption>

</figure>

**提醒:**元素的可视化表示可以不同于它的 HTML 元素。您可以通过添加 CSS 类将`<h5>`设计成类似于`<h4>`的样式。这就是你在上述情况下应该做的。

## Nr。3-在结构中添加隐藏标题，为屏幕阅读器标记站点部分

这个改进属于本文开头提到的 20%:这不是一个自动测试套件会告诉你的。这些是额外的内容改进，你可以根据手工测试的反馈或者直觉来处理。我说的直觉是指:

> 当网站在视觉上表现出来，但在 HTML 标记上却不那么明显时，什么是明显的？

*隐藏标题*的意思是**肉眼看不到但可以通过屏幕阅读器**访问的标题。这意味着在你的内容中添加标题，如“主网站导航”、“语言切换器”和“网站页脚”。

再次以英国政府为例。例如，你会在页脚看到一个隐藏的链接标题。

<figure>[![Screenshot of h123 browser bookmarklet output for gov.uk](img/8efdf483a3b7c19fe0526f8bf1cfd789.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AXL-nYw6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/431/1%2AgvuY95Zv3iW7NkQKNvNTpw.png)

<figcaption>gov . uk 的 h123 bookmarklet 输出的一部分，激活了“标记可视隐藏”选项。< h2 >“支持链接”被标记为可见隐藏。</figcaption>

</figure>

gov.uk 页脚的

<figure>[![Screenshot of gov.uk footer section](img/bb915d9b625a2779358685624240ba94.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--az6UiE1f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AxLSliPq9LmZuTUDFzuifTw.png) 

<figcaption>截图，显示“支持链接”的< h2 >在视觉上是隐藏的。</figcaption>

</figure>

在[易访问性开发者指南](https://www.accessibility-developer-guide.com/examples/headings/visually-hidden-headings/)中关于标题的部分很好地涵盖了这一点。

*提示:*你和我可能不同意[使用多个](https://www.accessibility-developer-guide.com/examples/headings/multiple-h1-okay-for-seo/)

# [来提高可访问性](https://www.accessibility-developer-guide.com/examples/headings/multiple-h1-okay-for-seo/)，因为 gov.uk 证明你可以基本上遵循 HTML 规范而没有任何可访问性问题。我个人使用 one

# 的方法，我建议在所有项目中遵循最佳实践。

## Nr。4 —修复您的表单

在我深入探讨可访问性之前，表单可能出错的地方比我最初想象的要多。Nr 里没有提到这个。因为这不仅仅是关于语义。要使表单可访问，还需要做一些额外的事情，尤其是验证。

1.  **检查每个输入、选择或文本区域是否有一个** [**标签**](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label) :如果没有`<label>`，那么对于屏幕阅读器用户来说，表单域的内容很可能是不可见的。您可以将表单字段包装到`<label>`标签中，这是有效的，但是为了更加灵活，我建议使用 for 属性通过 id 连接到表单字段。
2.  **用** **必填属性**标记必填字段:这让屏幕阅读器清楚地知道该字段是必填的。
3.  **将内联验证错误和其他提示与** **联系起来:假设您在包含错误的表单字段下方显示错误，当屏幕阅读器将焦点放在该字段上时，它不会读取错误消息。为此，您需要通过向表单字段添加一个链接到错误消息 ID 的`aria-describedby`来将最有可能包含错误消息的`<p>`标记与表单字段连接起来。详细描述可以在[可访问性开发者指南](https://www.accessibility-developer-guide.com/examples/forms/validation-messages/)中找到。**

你可能想对表单进行更多的改进，但是它们也可能变得更加复杂。

## Nr。5-修复键盘焦点的可见性和顺序

这很容易测试。打开您的网站或应用程序，无需使用鼠标即可浏览(制表键、Shift+表格键和箭头是您的朋友，但[还有更多](https://www.accessibility-developer-guide.com/knowledge/keyboard-only/controlling-a-computer/))。

你总是看到你现在在哪里吗？能不能把所有交互元素都集中起来？这个顺序对你有意义吗？

**您可以应用的修复:**

1.  **改变元素的源顺序:**默认的键盘焦点依赖于 HTML 的源顺序。因此，可能会有这样的情况，您希望更正代码中的顺序，然后用 CSS 直观地更改顺序，以便适合您的设计。只要焦点没有从左到右或者从上到下再到后的巨大跳跃，这是完全有效的，这可能会非常混乱。
2.  **改变默认的焦点样式，使其更加清晰可见:**浏览器实现默认的焦点样式有很大的不同。当元素被聚焦时，一些浏览器显示蓝色轮廓。这在白色背景下可以，但在蓝色背景下就不行了。有些浏览器，比如 Firefox，只在聚焦的元素周围显示一条几乎看不见的虚线。[从不依赖默认的焦点样式](http://adrianroselli.com/2017/02/avoid-default-browser-focus-styles.html)。[打造自己的](https://css-tricks.com/focusing-on-focus-styles/)。对于最新的浏览器，现在甚至有一个[修饰符用于键盘焦点](https://css-tricks.com/keyboard-only-focus-styles/)，所以你可以通过鼠标点击和键盘来区分焦点。
3.  **使用原生 HTML 元素而非定制:**例如，如果您想要为单选按钮使用[定制样式，使用`<input type=“radio”>`元素并覆盖样式，例如，通过可视地隐藏输入，并用“假的”输入样式将标签保持在原位。](https://www.w3.org/WAI/tutorials/forms/custom-controls/)

<figure>[![Screenshot of focused form field on gov.uk](img/0d7c2788b5682ee0bc2ddba319be6815.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CxDuqx3X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/639/1%2AVcnWBrlaKWQhdJjVB3lAwA.png) 

<figcaption>为 gov.uk 上的表单字段自定义焦点样式</figcaption>

</figure>

## 结论

网站或应用程序的基本可访问性并不难实现。你的 UX 和设计变得越现代，就越需要努力让它变得无障碍，例如当你使用 React、Vue.js 或 Angular 等水疗解决方案时。

这五个修正只是你可以做的许多事情中的一小部分，但是你应该能够在几个小时内应用它们，并且已经产生了很大的影响。

如果您对如何使您的网站和应用程序具有可访问性的更深入的知识感兴趣，我可以为您提供一些链接:

*   [**易访问性开发者指南**](https://www.accessibility-developer-guide.com/) 一个主要针对前端开发者的关于如何构建和测试网站易访问性的指南。包括许多来自真实世界测试的实用知识。
*   [**包容组件**](https://inclusive-components.design/) 一个关于构建可访问组件的博客。
*   [**WCAG**](https://www.w3.org/WAI/standards-guidelines/wcag/) 事实上的网页无障碍标准。
*   [**一个列表分开，话题无障碍**](https://alistapart.com/topic/accessibility) 很多关于无障碍的有用资源。

如果您对可访问性有任何疑问，请随时通过 [rey@nothing.ch](//mailto:rey@nothing.ch) 联系我。

* * *