# 超越聚合填充:Web 组件如何影响今天的我们？

> 原文：<https://dev.to/webpadawan/beyond-the-polyfills-how-web-components-affect-us-today-3j0a>

在最近公开宣布基于 Chromium 的 Edge canary builds 之后，Twitter 上出现了很多兴奋的消息，因为 Web 组件终于要登陆所有 evergreen 浏览器了。高分子界已经等了这么多年了，我也有同感。Web 组件在这里...但我还是会很小心地说。

让我们假设我们不必支持 IE11 或任何其他传统浏览器。所以，evergreen 浏览器同时支持自定义元素和阴影 DOM。好消息是我们可以忘记 [polyfills](https://github.com/webcomponents/webcomponentsjs) 。坏消息是，“零聚合填充”并不真正意味着“零 bug”。今天，我想分享一下我的痛苦，谈谈仍然存在的问题。

这篇博文是基于 3 年来对 0.5 以来所有聚合物版本的商业经验而写的(是的，我还记得在同一个组件中有多个影子根的怪异之处)。在此期间，我一直在开发一个相当复杂的生产应用程序和一个 web 组件库，所以我的观点是基于实际背景的。

我现在写这篇文章也是因为即将召开的关于 Web 组件实现进展的[“面对面”(F2F)](https://github.com/w3c/webcomponents/issues/802) 会议。浏览器供应商以及 GitHub 和 Salesforce 等公司的代表都计划参加这次会议。要讨论的话题包括未来，像往常一样，看起来光明而闪亮。但是今天我会更关注当下，因为仍然有一些问题不太可能被解决。

第一个(你猜到了)是关于[扩展内置 HTML 元素](https://developers.google.com/web/fundamentals/web-components/customelements#extendhtml)。假设我们想要实现自己的锚，并使用它进行路由，这是 SPA 的常见做法。我在 Custom Elements V0 时代使用了这样一个 [web 组件](https://github.com/erikringsmuth/pushstate-anchor)，它工作得很好。但是 Safari [拒绝](https://github.com/w3c/webcomponents/issues/509#issuecomment-222860736)实现`“is”`属性，因为他们不想改变 HTML 解析器。

因此，你不能简单地在 Safari 中扩展锚(或按钮，或任何类似的东西)。这种情况不太可能改变，即使相应的问题早就解决了。它甚至没有被谷歌的官方聚合填充所覆盖。当然，这里有一个[社区聚划算](https://github.com/ungap/custom-elements-builtin)。但是真的有人愿意用它，永远猴子式的修补浏览器内部吗？

与 Safari 意见不一致的另一个问题与 Shadow DOM 的样式有关。具体来说，它是关于 [`:host-context`](https://developers.google.com/web/fundamentals/web-components/shadowdom#contextstyling) CSS 选择器的，旨在根据父组件改变 web 组件的样式。CSS 中的任何其他地方都不支持这种控制反转。而 Safari 拒绝实现`:host-context`和[要求将](https://github.com/w3c/csswg-drafts/issues/1914#issuecomment-468864879)从规范中移除。到目前为止，它只能在 Chrome 中工作。听起来很熟悉，不是吗？

`:host-context`如此重要的原因是它是 web 组件基于其父组件调整样式的唯一方式。例如，在一些 CSS 框架中，放在禁用的`<fieldset>`中的`<button>`看起来应该是禁用的。我们可以用全局 CSS 来实现，但是用影子 DOM 就不行了。我们唯一能做的就是...来改变我们的想法。

Shadow DOM 影响的另一件事是处理焦点，尤其是跳转到任何可聚焦元素时的导航顺序，比如 Shadow 根中的`<input>`。这同样只在 Chrome 上有效，标志名为 [`“delegatesFocus”`](https://github.com/w3c/webcomponents/blob/0625cf4c42785aa1202a9d0daed182e12466aa29/proposals/ShadowRoot-delegatesFocus-Proposal.md) 传递给`attachShadow()`调用。一旦所有的浏览器都支持它，一行代码有望创造奇迹并节省我们大量的时间。在那之前，我们不得不使用[临时](https://github.com/vaadin/vaadin-control-state-mixin) [变通办法](https://github.com/elix/elix/blob/master/src/DelegateFocusMixin.js)。

我说“暂时”了吗？在写这篇文章的时候，事情的[状态](https://github.com/w3c/webcomponents/issues/802#issuecomment-477807379)听起来是这样的:没有人做过重构的工作，也没有人做过[将特性](https://github.com/whatwg/html/issues/2013)集成到 HTML 规范中。Chrome 的开发人员很久以前就被指派从事这项工作，现在已经离开了这个团队。所以快 3 年了也没什么进展。真希望 F2F 会后能看到。

向前看，我们偶然发现了另一个与影子 DOM 相关的东西。所以现在，说几句关于 [CSS 阴影部分](https://github.com/fergald/docs/blob/master/explainers/css-shadow-parts-1.md)。它们最近已经在 Chrome 中发布，Firefox 也有一些兴趣，它正在将其内部迁移到自定义元素。但是同样的， [WebKit 跟踪问题](https://bugs.webkit.org/show_bug.cgi?id=149443)已经一年多没有更新了。因此，又一个 Chrome 独有的功能。

与上面的例子不同，缺少对 CSS 阴影部分的支持不会破坏任何东西…除了它完全破坏了开发人员的体验。在现实世界中，样式封装对用户来说可能是一件坏事——尤其是当一个 web 组件有许多可定制的内部构件时，很难提供一个只使用定制 CSS 属性的灵活架构。

事实上，用户不希望样式泄露组件。但与此同时，他们往往希望某些风格能够神奇地渗透进来。是的，这已经被部分覆盖了[可构造的样式表](https://github.com/WICG/construct-stylesheets/blob/gh-pages/explainer.md)(你能猜到目前唯一支持它们的浏览器吗？).但是我们仍然需要 CSS 阴影部分来替换[废弃的 CSS 混合](http://www.xanthir.com/b4o00)和`@apply`。

到目前为止还不错，但看起来我不得不再次提到 Safari 现在是因为一个更高级的话题。如果你曾经尝试过在影子根中使用富文本编辑器 JS 库，比如 [CKEditor](https://github.com/ckeditor/ckeditor5-engine/issues/692) 、 [Quill](https://github.com/quilljs/quill/issues/2021) 、 [Prosemirror](https://github.com/ProseMirror/prosemirror/issues/476) 甚至 [Trix](https://github.com/basecamp/trix/issues/403) (它是用定制元素构建的)，你就会理解我了。这也是浏览器厂商需要解决的问题。

在 Safari 中调用`document.getSelection()`不会从影子树返回节点。Chrome 也在影子根上实现了这个方法，但是 Safari [没有](https://bugs.webkit.org/show_bug.cgi?id=163921)。在之前的一次 F2F 会议上已经有[的大致共识](https://github.com/w3c/webcomponents/issues/79#issuecomment-432974389)和[的相关问题](https://github.com/w3c/selection-api/issues/98)提交给相应的回购。在这个问题解决之前，我们应该考虑谨慎使用 shadow DOM。

您可能已经注意到，除了第一个问题，所有问题都与影子 DOM 有关。即使没有 ShadyDOM shim 使某些猴子修补的 API [在“好的老的”Edge 中变慢](https://github.com/webcomponents/shadydom/issues/248)，我们仍然要记住一些问题。实施者反对的声明性阴影 DOM 提议(服务器端渲染需要)是另一个严峻的挑战。

我希望现在已经清楚为什么这么多人对 Web 组件规范不感兴趣了。它们确实提供了缺失的原语并解决了一定数量的问题——代价是引入了其他挑战。因此，盲目地支持 Web 组件实际上会导致适得其反的结果，因为有人甚至在今天还会称之为“违背诺言”。

综上所述，我确实相信 web 组件是一件大事，并且有希望使我们的 Web 应用程序的架构更加灵活，我们的包的大小更小。它们可以与 CSS 网格或 ES 模块相比，除了 web 组件与 Web 平台的所有底层部分相耦合的事实:HTML、CSS、JavaScript 和 DOM。

最后，正如上面已经提到的，我真的希望在接下来的几周内更新这个帖子，并看到至少某些棘手的问题得到解决。如果你，读者，和我一样看了很多 GitHub 库和问题，你可能会在里面给我一些帮助。无论如何，我会很高兴听到任何关于这个帖子的有价值的反馈！

UPD:请注意，我不是在责怪 Safari 或 Chrome，老实说，Firefox 在推出 63 版后也出现了一些小问题。这里的重点是共识意味着什么，以及 Web 组件 API 需要多长时间才能成熟和稳定。