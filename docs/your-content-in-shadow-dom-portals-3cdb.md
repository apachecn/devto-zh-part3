# 影子 DOM 门户中的内容

> 原文：<https://dev.to/westbrook/your-content-in-shadow-dom-portals-3cdb>

> 这是从[媒体](https://medium.com/@westbrook/your-content-in-shadow-dom-portals-b964578a2e74)交叉发布的，如果你在那里看到了它，感谢再次查看🙇🏽‍♂️，如果这是你第一次来，欢迎你。我已经开发了 dev.to，让 Stackblitz 嵌入指向特定文件的指针，所以希望它能很快发布与那篇功能对等的帖子。🤞

#### 他们现在在哪里？

最近，我在 Twitter 上看到了一个链接，链接指向 [PortalVue@2.1.0](https://www.npmjs.com/package/portal-vue) ,正如我经常看到的那样，我不禁想，作为一个 web 组件，它会是什么样子呢？尤其是 Vue 对我来说已经有点像 web 组件了(也许是它们标签名中的连字符，也许是更接近 HTML 模板，也许是他们[直接从他们的 CLI 构建 web 组件](https://cli.vuejs.org/guide/build-targets.html#web-component)的方式，谁能确定呢)，所以这让我更容易想象移植一些东西。不用说，我进入了工作，并发现了一些有趣的早期成功，我很想与你分享。在这个过程中，我还遇到了许多关于 API 设计、工作范围和 doneness 的问题，希望您有兴趣参与。

### 什么是传送门？

对于那些选择不签出以上 PortalVue，或者在过去没有使用过 portals 的人来说，基本概念如下。当您在站点的一个部分中有一些内容或组件，而您希望在另一个位置显示这些内容，同时仍然将这些内容绑定到初始位置的数据和功能时，门户允许您将这些内容投影到第二个位置。虽然像`position: absolute`这样的概念可能从一开始就显得微不足道，但这种方法可能会受到布局现实的阻碍(例如`overflow: hidden/auto`、`transform: translate...`、`z-index: ...`等)。都干扰这个思路)。门户允许您不必担心这些复杂性，因为它为您提供了初始内容的本地接收器，该接收器会将所需的内容投射到您选择的目的地，同时管理过程中可能出现的任何问题。这有助于管理的一个相当常见的模式是打开模态对话框/与模态对话框交互。我将在下面讨论它以及我想到的其他一些用途。

### 门户不是什么

也许“不是”这个词对于这部分来说是错误的，因为由 [WICG](https://wicg.github.io/admin/charter.html) 指定的[门户](https://wicg.github.io/portals/)是“一个在站点或页面之间实现无缝导航的提议”所以在现实中，这就是门户。然而，为了这次对话的目的，我将坚持门户的 [React 定义，即“提供一种一流的方法来将子组件呈现到存在于父组件的 DOM 层次结构之外的 DOM 节点中。”对于那些寻找其他信息的人，很抱歉。不管怎样，我真的希望你能留下来听听我剩下的想法。并且，对于那些想要了解 WICG 规范的人来说，欢迎你们的到来！](https://reactjs.org/docs/portals.html)

### 意为

在进入实际代码之前，我想介绍几个概念，我计划在制作 web 原生门户时利用这些概念；阴影 DOM 和自定义事件。影子 DOM 及其 slot API 是 OG web 原生门户。Shadow DOM 允许隐藏组件的内部结构，修饰 Light DOM 中提供的任何内容。当与`<slot/>`元素上可用的`slotchange`事件混合使用时(光照 DOM 将被放置在阴影 DOM 中的位置),所提供的功能非常适合接收和维护要跨门户发送的内容。自定义事件允许您通过`CustomEventInit`字典中的`detail`条目(例如`new CustomEvent('name', {detail: 'Info Goes Here'})`)传递任意信息。结合`event.composed`，它允许您的事件通过阴影 DOM 障碍，在 DOM 周围传输有问题的内容似乎触手可及。

根据这些决定，我可以看到实现门户功能的许多不同方式。因为这种几乎压倒性的灵活性，我决定不去想我能以某种方式独自找到最好的方法，而是尝试一起找到接近最简单的方法，然后作为本文的一部分讨论如何强化该方法的选项。为此，我希望您能在下面的代码大纲中找到兴奋或疑问，因为我想听听它。希望你的很多问题也是我想过的，但请保持诚实，在下面评论/发推特给我 [@westbrookj](https://twitter.com/WestbrookJ) 告诉我你的想法。一旦这种方法被进一步锁定，我期待着向 NPM 发布这些元素，这样整个社区都可以从中受益。

## 传送门

像任何好的门户一样，我们的门户将有一个`portal-entrance`，它将标记我们的内容绑定到数据和功能的位置，还有一个`portal-destination`，它将标记我们的显示位置。我们的`portal-entrance`将有一个`destination`属性，该属性通过一个匹配的`name`属性将它寻址到我们的`portal-destination`的一个特定实例。这两者之间的连接将由自定义事件构成，并从一个`slotchange`事件中发出。这在`<slot/>`元素的内容发生任何变化时触发，并可用于我们的`portal-entrance`元素，如下所示:

```
constructor() {
    super();
    this.attachShadow({mode: 'open'});
    this.shadowRoot.innerHTML = this.render();
    const slot = this.shadowRoot.querySelector('slot');
    slot.addEventListener(
        'slotchange',
        this.projectSlot.bind(this)
    );
}
render() {
    return `
        <style>
            :host {
                display: none;
            }
        </style>
        <slot></slot>
    `;
} 
```

从事件对象中，我们可以访问`e.target.assignedElement()`,它概述了 light DOM 提供的槽的内容，这很重要，因为`e.target.children`在这个元素中不可用:

```
this.dispatchEvent(
    new CustomEvent(
        'portal-open',
        {
            composed: true,
            detail: {
                destination: this.destination,
                content: e.target.assignedElements(),
            }
        }
    )
); 
```

关于此处调度的事件，需要注意两件重要的事情:

1.  事件是`composed`,这意味着它将能够通过影子 DOM 边界。这是结合 Shadow DOM 处理事件的一个重要且容易被忽略的部分。一个`shadowRoot`是一个`document-fragment`，如果没有`composed: true`，你的事件将被锁定在这个范围内运行它的整个生命周期。
2.  事件不`bubble`。在我们门户的上下文中，我们应该能够相信，在我们将内容放入`portal-entrance`的任何时候，它都会被打开。为了确保为响应这些变化而分派的事件不会过早停止传播，`portal-destination`中的侦听器将被放置在`document`上，并在捕获阶段完成其工作，使其第一个访问事件。

对于那些不经常(或者可能从来不)使用捕获阶段的人(像我一样)，这是第一个[越来越多的选项](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Parameters)加入到我们的`addEventListener`的第三个论点中。您可以通过以下方式将监听器隐式连接到事件的这个阶段:

```
document.addEventListener(
    'portal-open',
    this.acquireContent,
    true
); 
```

上述内容可以追溯到第三个参数唯一可用的功能是`capture`的时代，然而现实已经过去，我们的门户代码更愿意通过以下方式明确概述`capture`的用法:

```
document.addEventListener(
    'portal-open',
    this.updatePortalContent,
    {
        capture: true
    }
); 
```

默认情况下，我们将主要通过`portal-entrance`元素是否连接到 DOM 来管理我们的内容是否被投影到其目的地。这意味着我们可以利用`disconnectedCallback()`生命周期方法来分派`portal-close`事件，这将告诉我们的`portal-destination`内容不再被投射到它里面。

```
disconnectedCallback() {
    this.close();
}
close() {
    document.dispatchEvent(
        new CustomEvent(
            'portal-close',
            {
                composed: 1,
                detail: {
                    destination: this.destination
                }
            }
        )
    );
} 
```

在这里，这个功能出现在`close()`方法中，这意味着这个功能也可以在我们的`portal-entrance`元素中直接使用，以便根据需要强制调用。它还被分派到`document`上，以确保当移除`portal-entrance`元素或其任意数量的祖先作为对 DOM 树的较大更改的一部分时，它被挂在将保留在文档事件中的元素上。

将所有这些放在一起，我们的`portal-entrance`元素如下所示:

[https://stackblitz.com/edit/vanilla-portal?embed=1&view=editor&file=portal-entrance.js](https://stackblitz.com/edit/vanilla-portal?embed=1&view=editor&file=portal-entrance.js)

<figcaption>portal-entrance.js in all its 46 lines of glory.</figcaption>

## 目的地

我们已经注意到，我们的`portal-destination`将通过`portal-open`事件的捕获阶段监听从`document`投射到它的内容，比如:

```
document.addEventListener(
    'portal-open',
    this.updatePortalContent,
    {
        capture: true
    }
); 
```

重要的是，我们要与元素生命周期并行地管理这个事件的添加和删除，以便不留下任何僵尸事件。为此，我们可以依靠`connectedCallback()`和`disconnectedCallback()`来分别管理`addEventListener`和`removeEventListener`。但是，当我们`updatePortalContent`的时候，我们实际上在做什么呢？

首先，我们将从`portal-open`事件中`updatePortalContent`，但前提是它是针对这个`destination`的。按照目前通过`connectedCallback()`和`disconnectedCallback()`管理听众的方法，可以有多个`portal-destinations`的想法是由这些目的地中的每一个管理它们自己。由于这一事实，每个目的地将听到所有的`portal-open`事件，然后需要通过检查其`detail`对象中事件的`destination`来确定哪些是针对它的。匹配事件目前已经停止了传播，因此在将投影的内容缓存到元素中之前，事件不会继续沿着 DOM 树向下进行捕获。

```
acquireContent(e) {
    if (e.detail.destination !== this.name) return;
    e.stopPropagation();
    this.projected = e.detail.content;
} 
```

从那里，getter/setter 对被用来管理对`this.projected`值的改变的副作用:

```
get projected() {
    return this._projected || [];
}
set projected(projected) {
    this._projected = projected;
    this.project();
}
styles() {
    return ':host{display: contents}';
}
conditionallyAppendStyles() {
    if (this.shadowRoot.adoptedStyleSheets) return;
    let style = document.createElement('style');
    style.innerHTML = this.styles();
    this.shadowRoot.appendChild(style);
}
project() {
    this.shadowRoot.innerHTML = '';
    this.conditionallyAppendStyles();
    this.projected.map(el => this.shadowRoot.appendChild(el));
} 
```

就这样，我们差不多完成了。在这一点上，没有节点，所以实际的 DOM 和在它的初始位置发生的任何绑定将被保存在它的新位置。保存这些节点或保存这些节点中的位置以备将来更新的绑定策略将保持对它们的直接访问以备将来更新和响应 DOM 事件，而重建 DOM 的策略将触发一个新的`slotchange`事件，重新开始移植过程。您还会注意到，在我们的`portal-entrance`元素中，当元素为空时，它不会调度`portal-open`事件，以防止不克隆移植的元素触发会从两个位置删除内容的次级事件。剩下的就是清理了。

我们的`portal-destination`元素为`document`上的`portal-close`事件挂起了一个监听器，这也指向了`updatePortalContent`方法，但是这次没有实际应用的内容。这将把`projected`的值“设置”为`undefined`，但是它的 getter 将确保这个虚假数据退回到一个空数组。

随后，设置`projected`的副作用再次运行，元素内容被重置，完成。当你把所有这些放在一起，它看起来像:

[https://stackblitz.com/edit/vanilla-portal?embed=1&view=editor&file=portal-destination.js](https://stackblitz.com/edit/vanilla-portal?embed=1&view=editor&file=portal-destination.js)

## 我们的门户在行动

眼见为实，现在来看看实际运行的代码(如果你有耐心不去看的话):

[https://stackblitz.com/edit/vanilla-portal?embed=1&view=preview&](https://stackblitz.com/edit/vanilla-portal?embed=1&view=preview&)

当您单击“切换投影”时，会在页面左上角红色轮廓所包围的元素中添加/删除一个`portal-entrance`，其内容会被移植到页面右下角绿色轮廓所包围的`portal-destination`元素中。一旦您的内容被投影，您可以增加计数器，其值将在切换期间保持不变。您还可以使用“重置计数”按钮在数据源重置绑定数据。

这个用例当然是人为的，下面是一些真实的例子和用例。但是，首先，我想谈谈我对当前方法的一些疑问，以及一些后续步骤，特别是关于将它变成其他人可能想要使用的形状。在那之后，我们可以重温一下由 [VuePortal](https://portal-vue.linusb.org/guide/getting-started.html#use-cases) 和 [React Portals](https://reactjs.org/docs/portals.html#usage) 提出的一些想法，甚至可能想出一些新的和/或一些通过与其他 web 组件巧妙结合而实现的用例…

## 但是，现在呢？

### 镜像监听

目前，API 契约规定，当带有该目的地的`portal-entrance`连接到 DOM 时，必须有一个`portal-destination`可用并被命名。这够远了吗？当一个`destination`随后被连接时，在已建立的`portal-entrance`上是否也应该有镜像监听器来重新分发内容？似乎有很多使用模式想要利用这一系列事件，就像利用当前模式一样。研究应该深入到那些模式可能看起来像什么，以及在添加它们有意义的情况下，是内置的还是扩展的对那些特性的支持更合适。

一旦您开始考虑后期绑定目的地，绑定到多个目的地的大门也就打开了。这将对话引向更有结构意义的方向，因为我们依赖于只有一个 DOM 元素实例通过门户传递，并且能够在 DOM 树中移动该元素。如果将相同的内容移植到多个位置是有意义的，那么该内容的绑定和克隆将需要对其当前采用的形式进行重大改变。虽然我可以肯定地看到这可能发生的方式，但我不确定它在当前门户存在的工作范围内是否有意义。改变主意！

### 多个入口

与多个目的地的想法相关，我认为 [PortalVue](https://portal-vue.linusb.org/guide/getting-started.html#multiple-portals-one-target) 目前支持的一个特性是支持多个`portal-entrance`元素将它们的内容传递到同一个目的地。以类似于在`select`元素中发现的方式为`portal-destination`添加一个`multiple`属性，会立即让人想到用一种“chips”UI 支持“多选”输入字段或面包屑聚合的方法。这种方法最有可能需要一个类似于 PortalVue 所使用的`order`属性，但是依赖 DOM 或交互顺序可能会有一些有趣的东西，也值得思考。

### 跨浏览器支持

现在您已经看到了我用影子 DOM 和定制元素创建门户的方法的基础，最重要的下一步是确保功能是跨浏览器兼容的。很容易将这一点认为是 web 组件的祸根，但实际上浏览器的支持是相当高的。 [Shadow DOM](https://caniuse.com/#search=shadow%20dom) 目前拥有 87%的原生支持，[自定义元素](https://caniuse.com/#search=custom%20element)列在 86%的列表中，随着我们的演示中你可能已经注意到的多填充，这两个数字都接近完全覆盖。

```
<script src="/node_modules/@webcomponents/webcomponentsjs/webcomponents-bundle.js"
></script> 
```

这种门户方法开始出现问题的地方在于它对`slot.assignedElements()`的使用。由于阴影 DOM 的 polyfill 实际上相当大而且很慢，我们用一个叫做阴影 DOM 的粗略近似来代替。Shady DOM 没有完全重新创建 Shadow DOM 规范，而是模仿了其特性 sest 的重要部分。这里忽略的一个主要因素是`slot.assignedElements()`。在一个更复杂的组件中，这可能是需要来自聚合物库的`FlattenedNodesObserver`的地方，但是简单的只有一个`slot`并且在我们的`portal-entrance`中没有其他 DOM 要担心，这允许我们使用`[...el.children]`来获得一个类似于从`slot.assignedElements()`中可获得的值。

这不会捕捉到现成的样式封装。需要研究将可疑的 DOM 库引入到`portal-destination`元素中的正确平衡，以限定任何可能穿过门户的`style`标签的范围。我希望我们可以利用一些或所有交付到目的地的样式，但是需要额外的代码来支持它的成本，或者需要某种类似 BEM 的命名约定，它们的好处还不清楚。

### 更有风格

在多填充浏览器中获取样式的想法被解决或避免后，更现代的浏览器中可用的一些超级功能，尤其是通过采用样式表提供的功能，打开了大量选项的大门，我认为这些选项值得探索。您可以在我们的代码中看到目前 API 使用最少的地方:

```
if (this.shadowRoot.adoptedStyleSheets) {
    const sheet = new CSSStyleSheet();
    sheet.replaceSync('a { color: red; }');
    this.adoptedStyleSheets = [sheet];
} 
```

考虑到这一点，当从入口到目的地传输样式时，我们可以利用这个 API 来简化这些样式的交付。然而，更有趣的问题是，沿着 DOM 树向上移动并从父阴影根中获取样式以传输到`portal-destination`是否有意义。许多问题立即浮现在脑海中，比如带有`::slotted()`风格 API 的 light DOM，或者在备用 DOM 树扩展中管理 CSS 自定义属性应用程序，但是能够在这方面做出某些保证会使模式更加有趣。

### 声明式 API

现在我们的`portal-entrance`和`portal-destination`元素共同依赖于两个属性；`destination`和`name`。然而，我们已经讨论了几个可能对这个家庭有益的补充。在`portal-entrance`上，拥有一个`opened`属性来概括门户内容何时被分发到它的`portal-destination`可能是管理这项工作的一个非常有用的方法，而不必每次都添加和删除`portal-entrance`元素。我们还讨论了在入口添加`order`的想法，以便管理向单个目的地发送多条内容。为了支持这一点，还可以添加`multiple`作为`portal-destination`的属性。当只有两个属性驱动这些元素时，直接从属性中读取数据是有意义的，不需要担心对数据的反应，但是我们添加的属性越多，就需要越多的样板文件和连线来管理大量的数据。依赖一个更正式的基类来定制我们的元素可能是值得的。以少量 KB 为代价，我们可以依靠`LitElement`为我们管理其中一些。它现在只保存了几行 JS，但是随着我们 API 的增长，它可能会越来越有用，特别是它的助手围绕着采用的样式表和可疑的 DOM/CSS 支持。然而，高性能渲染确实是`LitElement`的超能力，我们目前在`portal-destination`中只做了最少量的渲染。这可能有点矫枉过正，但是看看像指令(例如 [`cache`](https://lit-html.polymer-project.org/guide/template-reference#cache) )这样的东西是否可以在页面的生命周期中保存我们的元素。这是这种转换的一个原型的早期版本，它无疑使演示门户更加容易，如果没有其他的话:

[https://stackblitz.com/edit/lit-element-portal?embed=1&&](https://stackblitz.com/edit/lit-element-portal?embed=1&&)

## 例子

### 内容填充菜单

VuePortal 项目的一个很好的例子是能够将子导航或上下文内容移植到主内容区域的旁边:

[https://codepen.io/LinusBorg/embed/xdQZqa?height=600&default-tab=result&embed-version=2](https://codepen.io/LinusBorg/embed/xdQZqa?height=600&default-tab=result&embed-version=2)

这很好地确保了相关内容在您的标记/内容交付中共存，但并不要求它们在您的实际 UI 中共存。随着系统内容的增长，次要内容变得不一样的可能性也在增加，所以让它与主要内容一起管理，而不是作为数据的解释，是很有意义的。我用下面的静态页面(没有导航)复制了这种方法:

[https://stackblitz.com/edit/content-populates-menu?embed=1&&](https://stackblitz.com/edit/content-populates-menu?embed=1&&)

在这种情况下，随着动态页面切换，你已经完全控制了 HTML，可以把它放在你喜欢的任何地方，这种想法开始引起对这种模式的价值的质疑。

### 菜单填充内容填充菜单

这种内容抛出可能不会发生，因为在这种情况下，整个 HTML 响应会同时停止。然而，将这种方法与更动态获取的内容结合起来，这些内容由来自贾斯汀·法格尼的 [`html-include`](https://www.npmjs.com/package/html-include-element) 提供支持，你会开始有一些更有趣的东西。使用这种模式，不仅您的内容可以填充您的菜单，而且您的菜单也可以填充您的内容。

[https://glitch.com/embed/#!/embed/real-gemini?path=index.html](https://glitch.com/embed/#!/embed/real-gemini?path=index.html)

您会注意到，在页面导航中，标题链接的`href`被捕获来填充`html-include`元素的`src`，它们通过门户发送到基于最近打开的内容的主内容区域。这些 includes 随后加载的每个页面都包含一个内容特定的二级导航，该导航通过门户发送到页面的正确部分进行显示。这仍然是一个相当原始的用法，但我对以下在构建类似博客或博客软件网站时的简单性感兴趣:

```
<header>
    <nav>
        <portal-link>
            <a href="page1.html">Page 1</a>
        </portal-link>
        <portal-link>
            <a href="page2.html">Page 2</a>
        </portal-link>
        <portal-link>
            <a href="page3.html">Page 3</a>
        </portal-link>
    </nav>
</header>
<aside>
   <nav>
        <portal-destination name="navigation"></portal-destination>
   </nav>
</aside>
<main>
   <portal-destination name="main"></portal-destination>
</main> 
```

请注意，第一页的内容是以模拟服务器端呈现的方式手动复制到这个结构中的。只需稍加努力，确保服务器响应在加载时发送特定于每个页面的内容，这些 web 组件就会在服务器端呈现。

### 模态

为什么要让内容逃离 DOM 树的 OG 例子是用模态显示它。这里有一个在一对`portal-entrance`和`portal-destination`元素的内容中管理一个模型的超级简单的例子，仅仅是为了证明我们已经涵盖了这个用例:

[https://stackblitz.com/edit/vanilla-portal-modal?embed=1&view=preview&](https://stackblitz.com/edit/vanilla-portal-modal?embed=1&view=preview&)

## 你最喜欢什么？

有很多案例证明了这种内容门户在应用程序中的用途。即使只是在支持模态用例的方式上，这种方法也使得一个非常普通的技术变得更加容易。既然如此，我期待着继续这方面的研究。如果围绕使用 API 和模式有更多的结构，那么用一对定制元素在页面上移植内容的能力对其他人来说也是有价值的。如果您对上面的某个模式感兴趣，或者有门户可能支持的其他东西，我很乐意听听。围绕这一技术的 API 解决方案背后的声音越多，它在整个社区中就越强大、越有用。此外，我听到的对这种技术感兴趣的声音越多，我就可能越快完成...让我们回家吧！