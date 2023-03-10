# DOM 性能案例研究

> 原文：<https://dev.to/areknawo/dom-performance-case-study-dg9>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

我有一个有趣的问题要问你——你最后一次使用真正纯粹的 [**DOM API**](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model) 和方法来构建一个真正的项目是什么时候？是啊，也记不起这些时间了。😂但是它们真的存在吗？因为，你知道，你几乎总是在 DOM API 的帮助下使用 HTML 来做一些更具交互性的东西，但是你绝对不会把它作为一种独立的方式来创建你的 UI。但是，随着现代的 **UI 框架**和**库**，像 **React** 、 **Vue** 或 **Angular** 时代变了，创建 UI 的方式也变了。所以，除非你使用某种框架，让 [**把你的代码**](https://svelte.technology/) 编译成 HTML/CSS/JS trio，否则你最有可能把你的应用建立在某种基于 DOM API 的工具上。😉也就是说，这些工具为我们提供的控制水平令人难以置信。这确实有助于创造更好、更漂亮、更快的体验。是的，速度-这就是我们今天要研究的。

您可能知道或听说过，任何与 DOM 的交互都是**昂贵的**。当你没有正确使用时，这些调用会给你带来**的巨大性能损失**。即使我们谈论的是几分之一毫秒，它仍然很重要。如果你的用户界面不能流畅地工作，锁定在 **60 FPS** (+1/-1)，那么事情就不对劲了。但你的 Vue/React/Angular 应用不应该是这种情况，除非你做了一些真正糟糕的事情或执行了高要求的任务(BTC 矿业、WebGL、人工智能和其他数字运算)😁).这是因为这些工具的优化程度。因此，让我们在这里做一个案例研究，并检查一些 **DOM 优化技术**，包括这些库使用的技术，以了解这是如何做到的！**尽情享受吧！**👍

[![red and blue smoke bombs](img/c98b21951896adff1120bd5f3008050c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--knFxvsbp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1523958631412-cd125bb67e58%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

# 回流

从最臭名昭著的一个开始，接下来是**回流**——你最坏的敌人和最好的朋友同时出现。回流(也称为**布局垃圾** 🗑)是当你与 DOM、CSS 和所有这些东西交互时，在你的浏览器中发生的所有过程的名称。这意味着重新渲染和重新计算你的网站布局(元素的位置和大小)。所有好的方面——回流在幕后处理所有这些复杂的事情。让我们继续最糟糕的部分——回流是一个**用户阻塞**操作！这意味着如果在执行回流时有太多的工作要做，你的 UI 会**降低帧速率**，冻结或者——在最坏的情况下——甚至崩溃。这些都是你很可能不希望你的用户拥有的体验。也就是说，处理 DOM 很重要，因此需要特别小心地进行重排版。

那么到底是什么引发了回流呢？如果你想了解更多，GitHub gist 中有一个 [**大列表**](https://gist.github.com/paulirish/5d52fb081b3570c81e3a) 。但在这里，让我们快速看一下其中最重要的:

*   `getComputedStyle()` -极其有用**极其昂贵**；
*   **框度量**和**滚动**——类似`clientHeight`、`scrollTop`的东西；
*   **窗口属性** - `clientHeight`，`scrollY`；
*   **事件的位置数据** & **SVG**

所以这些只是基本的，更普通的。当然，一些像访问属性这样的任务比一些像`getComputedStyle()`这样更高级的方法有更少的性能开销(回流时间)。

# 配料

所以，回流不是很好。我们能做些什么来最小化它们或者至少优化它们以获得性能提升呢？🚀嗯，实际上相当多。首先，最好和最流行的技术被称为**批处理**。它的基本意思是，你应该**分组**你的 DOM **读和写操作**并且尽可能地分别提交它们。这个过程允许浏览器优化您的呼叫，并导致整体性能的提高。

```
// This will always be faster...

const width = element.clientWidth + 10;
const width2 = element.clientWidth + 20;

element.style.width = width + 'px';
element.style.width = width2 + 'px';

// ...than this.

const width = element.clientWidth + 10;
element.style.width = width + 'px';
const width2 = element.clientWidth + 10;
element.style.width = width2 + 'px'; 
```

Enter fullscreen mode Exit fullscreen mode

除此之外，你还应该批处理和**减少**任何其他类型的 DOM 交互。例如，让我们采用向 DOM 树添加新元素的标准方式。当你只添加一两个的时候，可能不值得额外的麻烦。但是当我们谈论几十个或者几百个元素的时候，恰当地提交这样的调用是非常重要的。我这么说是什么意思？嗯，要将所有这些调用整合成一个调用，很可能需要借助 [`DocumentFragment`](https://developer.mozilla.org/en-US/docs/Web/API/DocumentFragment) 。

```
// Unoptimized
for(let i = 0; i < 100; i++){
    const element = document.createElement('div');
    document.body.appendChild(element);
}

// Optimized
const fragment = document.createDocumentFragment();
for(let i = 0; i < 100; i++){
    const element = document.createElement('div');
    fragment.appendChild(element);
}
document.body.appendChild(fragment); 
```

Enter fullscreen mode Exit fullscreen mode

这样一个简单的改变会带来很大的不同。我认为，不言而喻，你应该在任何时候和任何地方应用相同的实践/想法**。除此之外，你的浏览器的**开发工具**也会被证明是有用的。您可以使用它的**呈现时间线**来查看所有关于您的 DOM 是如何呈现的相关数据。当然，只有在进行了适当的优化之后，它才有用。**

# 杂项

现在，让我们谈谈更一般的东西。最显而易见的建议就是让事情变得简单。但它的深层含义是什么？

*   减少 DOM 的深度——不必要的复杂性只会让事情变得更慢。此外，在许多情况下，当您更新父节点时，可能需要更新子节点，从而导致需要处理在指定节点下形成的**整个结构**。更新还可能调用 DOM 树中的所有更改。简而言之，它使回流需要更多的时间。
*   **优化 CSS**——自然，不使用的 CSS 规则其实根本不需要。你应该把这些都去掉。接下来，复杂的 **CSS 选择器**也会导致问题。但是，如果您已经遵循了前面的规则，这些可能会被证明是无用的，无论如何在您的代码中都不需要它们。**内联**你经常改变的风格也是一个很好的实践。显然，相比之下，许多元素使用的样式应该作为 CSS 规则**单独制作。**
*   **动画**——这些可以打得相当狠。您应该尽可能将动画限制为仅具有变换和不透明度属性。此外，最好将它们**排除在流程**之外，这意味着将`position`设置为`absolute`或`fixed`。这确保了你的动画不会干扰 UI 的其他部分，导致更慢的重排版。除此之外，通过利用`will-change`属性，让您的浏览器知道指定的属性将要改变。最后，你可能想使用 **CSS 动画**或 **Web 动画 API** 制作动画。这样你所有的动画都在特殊的、独立的**“合成器线程”**中执行，从而使它们**非阻塞**。

这些建议可以大大提高你的表现！所以，你可以随时使用它们。

[![low angle photo of glass buildings](img/75500d92b6998e20a7f044dc94974a99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rOS0yvQZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1467806757054-808649965a76%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

# 从不同的角度看问题

既然我们知道为我们处理视图更新的重流是万恶之源😈，让我们总结一下，从一个稍微不同的角度看一下所有以前的信息。

你屏幕上发生的一切都应该保留每个人都渴望的那种被夸大的 60 FPS。这意味着屏幕应该每秒钟刷新 **60 次**(对于刷新率更高的设备可能会更多)。更具体地说，这意味着在这个单一框架(JS、reflows 等)上发生的一切。)应该在 **10 毫秒**内发生(事实上你有大约 16 毫秒，但浏览器使用这 6 毫秒进行内部管理)。也就是说，当任务太大且耗时太长(超过 10 毫秒)时，帧速率**会下降**并出现滞后。

让我们看看这个图，看看**在这个单帧上**到底发生了什么:

[![img](img/9acaa34e62fd819a987117e265d6c009.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZHPa34VY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://areknawo.com/conteimg/2019/02/frame-full.jpg)

我认为 **JavaScript** 部分不需要进一步解释，除了它通常是**触发视觉变化**(它也可以是 CSS 动画、网络动画 API 等。).

**样式**标记**样式计算**发生的时间。在这里，你所有的 CSS 规则都被处理和应用**(CSS 选择器的东西)。**

 **布局和油漆步骤对我们来说是最重要的，因为它们很容易优化。**布局**步骤是回流起点。在这里，在你的样式已经在前面的步骤中被应用之后，可能需要**几何重新计算**的属性正在被处理。这包括`width`、`height`、`left`、`top`等。这些属性的改变可能需要**更新其他元素**，包括 DOM 树顶部和底部的元素。

优化这一步可以做的是明智地管理对这些属性的更改，或者拥有一个良好的 DOM 层次结构，在一次元素更新中不需要太多的更改。当然，你也可以改变`position`属性。正常流程之外的元素**不会触发其他元素的变化**。当没有改变布局属性时，浏览器**省略此步骤**。

之后是**油漆**步骤。这里处理不干扰布局的属性。这些包括`background`、`color`、`shadow`等等。一般纯视觉。重画不像布局改变那样昂贵，并且(就像以前一样)当不需要时**被省略**。

复合是最后一步，也是必须的一步。在这里，所有先前创建的层被粘在一起，以获得最终结果。这将在以后被一个像素一个像素地绘制到你的屏幕上。

我认为这些关于这一切是如何发生的内幕可以真正启发你进一步挖掘如何优化你的代码。此外，如果您认为您的应用程序在没有任何优化的情况下已经足够快了，请想想您可以用这种**额外的计算能力**做些什么——更多的视觉效果，更好的动画——选项几乎是无穷无尽的！🌟

# 虚拟 DOM 上的一句话

在所有这些技巧和提示之后，我想你现在可以很容易地理解这整个**虚拟世界**背后的神奇之处，它最近如此受欢迎主要是因为**反应**和 **Vue** 的巨大影响。它允许你以 JS 原生结构的形式保存你的可视节点的数据，因此不需要访问 DOM(结果是回流和其他东西)！

那么，简而言之，它是如何工作的呢？首先，您与 VDOM 进行交互，并对其应用您的更改。然后(我可能跳过了一些更详细的东西，但是它非常重要😁)接下来是**和解**步骤。这里，新的 VDOM 树**与旧的**进行比较，以区分变化。这些后来被应用到真实的 DOM 中。

现在，协调步骤是像 React vs Vue(性能方面)这样的讨论的真正起源。这种比较实际上是虚拟 DOM 背后最重要和最关键的想法。这是 React 16 ( **React Fibre** )在优化方面做得非常棒的地方。但是 Vue 同样令人印象深刻，它的虚拟 DOM 实现能够**选择性地选择**哪些节点需要更新(而不是它如何反应——通过向下更新整个树)。无论如何，这两个在**上做了很好的工作，提高了很多 JS 程序员的性能**和**开发经验**，所以**对此竖起大拇指**！👍

# 底线

我希望这篇文章能让你对如何提高基于 JS/React/Vue/DOM 的应用程序的性能有所了解。😀所以，除了 go 和**让网络更快**，我想没什么好说的了。😄一如既往，如果你喜欢这篇文章，考虑一下 **[，看看并分享我的个人博客](https://areknawo.com)** 。另外，关注我的 [**Twitter**](https://twitter.com/areknawo) 和我的 [**脸书页面**](https://www.facebook.com/Areknawos-Blog-770620059945105) 了解更多有趣的内容。🚀

# 资源

如果你想了解更多关于文章中讨论的话题，从这里开始**每一个可以拥有它的帖子**，**将拥有它**。😉一切为了更好的读者体验！✌

*   [**最小化浏览器回流**T3】来自【developer.google.com】T4](https://developers.google.com/speed/docs/insights/browser-reflow)
*   [**什么势力布局/回流【gist.github.com】**](https://gist.github.com/paulirish/5d52fb081b3570c81e3a)从
**   [**渲染表现**T3】来自【developer.google.com】T4](https://developers.google.com/web/fundamentals/performance/rendering/)*   [**动画及表演**](https://developers.google.com/web/fundamentals/design-and-ux/animations/animations-and-performance) 来自【developer.google.com】的**   [**反应纤维建筑**T3】来自【github.com】T4](https://github.com/acdlite/react-fiber-architecture)*   [**Vue 对比**T3】来自【vuejs.org】T4](https://vuejs.org/v2/guide/comparison.html)****