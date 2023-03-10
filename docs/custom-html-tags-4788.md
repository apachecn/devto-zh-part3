# 自定义 HTML 标签

> 原文：<https://dev.to/jfbrennan/custom-html-tags-4788>

设计更好的组件 API，避免过度使用定制的 HTML 标签。

尽管我热爱 JavaScript，但我最喜欢的网络语言是 HTML。它的声明式风格让我可以最容易地表达我的想法，并且通过刷新浏览器，我可以立即在屏幕上看到我的创作。

写 HTML 是设计和工程一气呵成，我喜欢它！

可以理解的是，HTML 不再像以前那样受到关注——我们现在正在浏览器中构建越来越复杂的应用程序——但是在 *UI 组件*的范围内，让我向您展示一种方法，它可能会让您对 HTML 产生新的兴趣。

### HTML 的用途

HTML 的主要工作是给你的内容结构和意义。随着网络的发展，HTML 加入了新的元素，为越来越多类型的内容提供语义支持，比如用于导航的`<nav>`和用于视频的`<video>`。它还为现有元素增加了新的功能，比如`autofocus`属性，它告诉浏览器在页面加载后应该关注哪个元素(这是登录或搜索页面的必备功能！).这些附加功能以及更多功能都是通过常见的 HTML 结构实现的:

*   标签
*   属性
*   嵌套

如果你需要复习，看看这个例子:

```
<p>Download your <a href="example.com/files" download>files</a></p> 
```

Enter fullscreen mode Exit fullscreen mode

那是一个“段落”元素。它的**标签**是`p`，它有一个“锚”元素**嵌套在里面**。主播有`download`和`href`(超文本参考的简称)**属性**。所有的 HTML 元素都是这样设计的。

下面是更多的例子(注意语义标签和属性名以及父子关系):

```
<input type="email" placeholder="name@example.com" autofocus>

<video src="example.com/vids/cats.mp4" poster="example.com/posters/cats.jpg" autoplay loop controls></video>

<section>
  <header>...</header>
  <footer>...</footer>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

标签、属性和嵌套是 HTML 用于实例化元素的声明性 API 的全部内容。简单又强大！

但是正如你所知道的，HTML 没有我们需要的所有元素，也永远不会有。因此，开发人员必须创建他们自己的自定义 UI 组件。对于更复杂的组件，这通常是使用类和 CSS 和/或 JavaScript 框架来完成的。不幸的是，这些组件总是偏离 HTML 的原始设计，失去了它的许多好处。

### 古老的方式

以图标为例，下面是 GitHub 用户界面中的一些图标:

[![Icons used in a tabs user interface](img/3a171b32665654112de0fe1ee74a28bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9dkky65z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.avalara.com/content/dam/avalara/us/blog/github-tabs.png)

因为 HTML 没有提供一个`icon`标签来标记网站的图标，所以开发者想出了他们自己的解决方案。下面是四个真正的自定义图标解决方案:

```
<i class="fa fa-gear"></i>

<i class="icon icon-gear"></i>

<span class="oi oi-gear"></span>

this is what GitHub does in the screenshot above

  <path d="..."></path>
 
```

Enter fullscreen mode Exit fullscreen mode

这些解决方案使用类来定义组件类型及其属性，虽然这没有什么错，但也有缺点:

**1。差命名:**
那些只有一个有意义的名字。此外，`fa-`、`icon-`、`oi-`和`octicon-`前缀是必需的，这导致了非干模式。

**2。随着时间的推移，清晰度的丧失:**
其他开发人员可能会削弱元素的用途。

```
<i class="icon icon-gear"></i>  original code
<i class="icon icon-gear foo"></i>  six weeks later
<i class="bar-baz icon icon-gear foo"></i>  a year later...what exactly is this element now? 
```

Enter fullscreen mode Exit fullscreen mode

##### 3。标签和类属性是不可避免的样板文件，没有任何意义:

```
<div class="icon icon-gear"></div> 
```

Enter fullscreen mode Exit fullscreen mode

全都是毫无意义的样板文件。真扫兴！

##### 4。与标准元素相比，基于类的设计显得格格不入:

```
<i class="icon icon-gear"></i>
<input type="email" autofocus> 
```

Enter fullscreen mode Exit fullscreen mode

如果用类来处理标准元素会怎么样？代替上面的输入，我们会有:

```
<div class="input input-type-email input-autofocus"> 
```

Enter fullscreen mode Exit fullscreen mode

恶心！

如果你跟着 BEM，情况会变得更糟。下面是一个来自流行设计系统的边界元法的例子:

```
<div class="mdc-dialog__actions mdc-dialog__actions--full-width"> 
```

Enter fullscreen mode Exit fullscreen mode

其他方法变得更加奇怪

```
<span uk-icon="icon: gear"></span> 
```

Enter fullscreen mode Exit fullscreen mode

我们不必这样做。

我们不需要使用类或者诡计。

还有更好的。

### 新方法

通过使用 HTML 标记、属性和嵌套，您可以使用更有意义和更熟悉的 API 来设计自定义 UI 组件。这里有一个例子:

*老款基于类的图标设计*

```
<i class="icon icon-gear"></i> 
```

Enter fullscreen mode Exit fullscreen mode

*同样的东西，但带有自定义标签和属性*

```
<icon name="gear"></icon> 
```

Enter fullscreen mode Exit fullscreen mode

如果这让你感到不安，不要担心。自定义标签兼容所有浏览器，甚至旧的 IE 浏览器。浏览器很乐意下载、解析和呈现定制标签，就像任何“真正的”HTML 一样，因为这个*是*真正的 HTML。浏览器不会为您的自定义标签(被浏览器注册为“未知”标签)提供任何默认样式或内置行为，但这根本不是问题。这些是真实的元素，所以您可以为它们创建 CSS 规则，并在 DOM 中查询它们。

因此，在图标的情况下，我们简单地样式化定制的*标签*和*属性*，而不是图标*类* :

```
icon {
  /* display: inline; Browsers display all unknown tags as inline, you can set it to whatever you want */
  font-family: 'My Icons';
}

icon[name="gear"]:before {
  content: "\u123"; /* a gear-shaped glyph */
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。没有黑客，没有依赖，没有新的或专有的东西！

我们再做一个。让我们转换流行的徽章组件:

*旧班徽设计*

```
<span class="badge badge-success">1</span> 
```

Enter fullscreen mode Exit fullscreen mode

*带标记和属性的新徽章*

```
<badge count="1" type="success"></badge> 
```

Enter fullscreen mode Exit fullscreen mode

自定义标签设计作为一个语义标记元素非常突出，它有自己有意义的属性，就像标准元素一样！

看看这个:用一个小小的 CSS 我们就可以给徽章添加智能，这样当它的计数为零或没有计数时，它就会消失:

```
badge[count="0"], badge[count=""] { 
  display: none; 
} 
```

Enter fullscreen mode Exit fullscreen mode

那很酷！

下面是一些其他的通用组件的例子，它们被设计成带有属性而不是类的定制标签:

```
<loader loading></loader>

<alert type="success">...</alert>

<row>
  <col span="6" hide="sm">...</col> hides on small screens
  <col span="6 sm-12">...</col> goes to 12 cols on small screens
</row> 
```

Enter fullscreen mode Exit fullscreen mode

我们用边界元方法重做材质的对话框动作组件怎么样？

*之前* 

```
<div class="mdc-dialog__actions mdc-dialog__actions--full-width">...</div> 
```

Enter fullscreen mode Exit fullscreen mode

后*后* 

```
<mdc-dialog-actions size="full-width">...</mdc-dialog-actions> 
```

Enter fullscreen mode Exit fullscreen mode

你能看出区别吗？

你开始感觉到好处了吗？

用标签和属性而不是类来设计 UI 组件很有趣，而且更好。客观上更好:

*   定制标签提供了强有力的、干巴巴的、语义化的名称，与类相比，这些名称很容易识别:`<badge>`与`<span class="badge">`
*   自定义标签保留其语义身份，而不管随时间增加的修饰符类别:`<badge class="foo bar">`与`<span class="foo bar badge">`
*   标签和属性为开发人员提供了丰富而熟悉的 API，而不是带有混合类列表的样板标签:`<col span="6" hide="sm">` vs. `<div class="col col-6 col-hidden-sm">`
*   基于类的设计不再需要 BEM 或其他工程方法
*   在许多情况下，你可以抛弃昂贵的抽象和它们的依赖:用无依赖的`<icon name="gear"></icon>`代替`{{> icon name="gear"}}`(手柄)或`<OverEngineeredIcon name="gear"/>`(反应)
*   结果是更干净和更短的代码，模仿标准的声明式 HTML API。

官方支持使用定制标签和属性(稍后会有更多的细节)。HTML 本来就是这样扩展的，但是开发人员却疯狂地喜欢上了类，这种模式很快成为了标准。是时候重新考虑了！

使用定制标签和属性还有另一个非常大的好处:**它可以更好地定位您的组件，以便将来改进。**怎么会这样？让我们现在进入那个。

### 组件进化

创建和共享定制组件是一种承诺。随着时间的推移，您的组件将不断发展，并添加新的功能。让我们来看看定制警报(也称为标注)组件的可能发展:

*原设计*

```
<alert type="success">
  <p>Custom tags are great!</p>
</alert> 
```

Enter fullscreen mode Exit fullscreen mode

```
alert { 
  display: flex; 
  color: white;
}

alert[type="success"] { background-color: green; }
alert[type="warn"] { background-color: orange; }
alert[type="error"] { background-color: red; } 
```

Enter fullscreen mode Exit fullscreen mode

看起来大概是这样的:

[![Alert component](img/9b177df2328111043bb8f3e5b68f7cca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o1IIbLfT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.avalara.com/content/dam/avalara/us/blog/alert-component-v1.png)

请注意，这里没有依赖关系。没有什么需要下载的，没有工具，也没有什么需要构建的。没有魔法，没有黑客，没有专有，没有框架或特殊语法，什么都没有。说到构建软件，没有什么比什么更好。

我们的警报现在非常简单，所以让我们看看是否可以给它一个图标:

*带图标*

```
<alert type="success">
  <icon name="check"></icon>
  <p>Custom tags are great!</p>
</alert> 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但是这不是设计组件的正确方法。让我们获得一个图标，而不要把它留给实现者:

*用图标推断出*

```
<alert type="success">
  <p>Custom tags are great!</p>
</alert> 
```

Enter fullscreen mode Exit fullscreen mode

```
alert[type="success"]:before {
  font-family: 'My Icons';
  content: "\u555"; /* gets us a ✓ icon */
} 
```

Enter fullscreen mode Exit fullscreen mode

[![Alert component with an icon](img/729241bda076d027b467234f0371993a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XTB0ZXqF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.avalara.com/content/dam/avalara/us/blog/alert-component-v2.png)

好了，这看起来真的有点像了。(注意，这里的 CSS 不包括所有需要的属性，如字体大小和填充)

警报自动消失是很常见的，所以让我们添加对它的支持。如果真的有一个 HTML `alert`元素，并且它有一个自动消失的特性，人们可以想象它会有一个`autodismiss`属性来触发这种行为，所以让我们来看看:

*新的自动识别功能*

```
<alert type="success" autodismiss>
  <p>Custom tags are great!</p>
</alert> 
```

Enter fullscreen mode Exit fullscreen mode

```
alert {
     transition: opacity 2s 4s ease-in-out; /* 4 second delay, then fade out */
     opacity: 1; 
}

alert[autodismiss] {
    opacity: 0; 
} 
```

Enter fullscreen mode Exit fullscreen mode

不错！我们真的得到了一个有用的组件，不需要任何依赖、构建步骤或聚合填充！看看它友好的小 API:

*   `alert`标签
*   `type`属性(*必需的*)—“成功”、“警告”或“错误”之一
*   `autodismiss`属性(*可选* ) -如果存在，警报将在四秒后消失
*   `id`、`class`、`aria-`和其他“继承”属性仍然适用
*   事件- DOM 事件，在警报消失后触发
*   接受嵌套内容，包括其他自定义标签

如果你不知道，你可能会认为这只是一个标准的 HTML 元素。这是我们走上正轨的标志！

#### 接近，但不完全

不过，有一个小问题。问题是我们的标签名并不完全适应未来。这里有两个考虑因素:

##### 碰撞

首先，有一天 HTML 可能会有一个和我们同名的标签。我每晚睡觉前都祈祷 WHATWG 会给我们`<icon>`，但如果 WHATWG 不给，其他开发者也有可能给我们。无论哪种方式都有冲突的风险，这就把我们带到了第二个考虑因素:前缀。

##### 前缀

虽然在这一点上这些不是技术上的[定制元素](https://developer.mozilla.org/en-US/docs/Web/API/Window/customElements)，但是您将希望通过为您的定制标签名称使用前缀来遵循该规范。在 Avalara，我们使用`s-`作为前缀。`s`是 Skylab 的缩写，是我们设计系统的名称，但它也意味着:

*   标准——我们总是追求标准，直到我们真的需要引入依赖关系
*   带属性的语义标签比带类的语义标签更具语义性
*   简单的 HTML 和 CSS 可以让你走得更远，而不需要像 React 这样的开销
*   共享——这些组件由我们的 20 多个网络应用和三倍多的开发者共享

所以，前缀是一种最佳实践。它解决了标签冲突的风险，并且是标准和定制标签之间的有用的视觉区分器。更重要的是，当需要支持 JavaScript 的功能，并且您的小“微”组件需要成长并成为真正的定制元素时，它可以很好地为您设置。你看，使用带前缀的定制标签而不是类允许你的组件向两个方向扩展:你可以向下扩展到轻量级的 CSS-only 组件，比如 Icon 和 Badge，或者一直扩展到响应状态变化的交互式组件*，同时为标准元素、定制标签和成熟的定制元素保持相同的 HTML 界面*。秘诀是从一个带前缀的定制标签开始。

让我们看看我们的警报如何从一个带有样式的基本定制标签变成支持 JavaScript 的交互式组件，而不会中断更改或转换范例。

在 Alert 的未来版本中，假设我们增加了设置`autodismiss`持续时间的功能。您可以通过简单地添加属性来使用默认的四秒钟，或者通过将其值设置为一个数字来缩短或延长该持续时间:

*覆盖自动忽略持续时间*

```
<alert type="success" autodismiss="10">
  <p>Custom tags are great!</p>
</alert> 
```

Enter fullscreen mode Exit fullscreen mode

但是正如我们所知道的，最好的做法是加上前缀，所以应该是:

```
<s-alert type="success" autodismiss="10">
  <p>Custom tags are great!</p>
</s-alert> 
```

Enter fullscreen mode Exit fullscreen mode

> 旁注:如果你是共享库的维护者，选择一个对你有意义的短前缀。例如，Twitter 的 Bootstrap 会从:
> 
> ```
> <div class="alert alert-success"> 
> ```
> 
> 到
> 
> ```
> <twbs-alert type="success"> 
> ```
> 
> 或者只是
> 
> ```
> <b-alert type="success"> 
> ```
> 
> 材料可以使用如上所示的`mdc-`。

反正回到`autodismiss`。支持秒的值现在需要使用 JavaScript。在这一点上，大多数人用他们所知道的，或者尝试流行的习惯用法和特殊的语法。如果你是一个只有一个应用程序的小团队，这不是问题，但是如果你有很多 Alert 组件的消费者，你就进入了一个代码契约，这个契约对实现者的要求越少越好，特别是在避免额外依赖的时候！

如果我们选择一个遵循或接近定制元素的解决方案，我们可以最大限度地减少合同，并为长期发展做好更好的准备。以下是目前可用的一些选项:

*   [定制元素](https://html.spec.whatwg.org/multipage/custom-elements.html#custom-elements)或者成熟的 [Web 组件](https://www.webcomponents.org/introduction)当然
*   [聚合物](https://www.polymer-project.org/3.0/start/)
*   [苗条](http://slimjs.com/#/getting-started)
*   视图
*   [Riot](https://riot.js.org) ，那里有我认为最好的 DX，[试试](https://riot.js.org/play/)。甚至有一个 w3c [提案将定制元素规范引向了类似的方向](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Declarative-Custom-Elements-Strawman.md)

这里有两个例子，其中 Alert 已升级为有状态组件，以支持用户定义的`autodismiss`延迟值:

*自定义元素+ `<template>`元素*

```
<template id="s-alert">
  <style>
    :host {...}
  </style>

  <s-icon></s-icon>
  <slot></slot>
</template>

<script>
  let tmpl = document.querySelector('#s-alert');

  customElements.define('s-alert', class extends HTMLElement {
    constructor() {
      super();
      let shadowRoot = this.attachShadow({mode: 'open'});
      shadowRoot.appendChild(tmpl.content.cloneNode(true));
    }

    static get observedAttributes() {
      return ['type', 'autodismiss'];
    }

    get type() {
      return this.getAttribute('type', val);
    }

    set type(val) {
      if (val) {
        this.setAttribute('type', val);
      }
    }

    get seconds() {
      if (this.hasAttribute('autodismiss')) {
        let seconds = (typeof this.getAttribute('autodismiss') === 'number' ? this.getAttribute('autodismiss') : 4) * 1000;
      } else {
        let seconds = 0
      }

      return seconds;
    }

    set seconds(val) {
      if (val) {
        this.setAttribute('autodismiss', val);
      } else {
        this.removeAttribute('autodismiss');
      }
    }

    attributeChangedCallback(name, oldValue, newValue) {
      // Update the type or autodismiss attribute
    }

    connectedCallback() {
      let icon = this.type === 'success' ? 'check' : this.type === 'error' ? 'info' : 'warn';
      this.getElementsByTagName('s-icon')[0].setAttribute('name', icon);

      if (this.seconds > 0) setTimeout(this.remove(), this.seconds);
    }
  });
</script> 
```

Enter fullscreen mode Exit fullscreen mode

*暴动*

```
<s-alert>
    <s-icon name="{icon}"></i>
    <yield/> <!-- same as <slot> -->

    <script>
        this.icon = this.opts.type === 'success' ? 'check' : this.opts.type === 'error' ? 'info' : 'warn';

        this.on('mount', () => {
            if (this.opts.autodismiss) {
                let seconds = (typeof this.opts.autodismiss === 'number' ? this.opts.autodismiss : 4) * 1000;
                setTimeout(this.unmount(), seconds);
            }
        })
    </script>
    <style>
      :scope {...}
    </style>  
</s-alert> 
```

Enter fullscreen mode Exit fullscreen mode

不管实现如何，我们对 Alert 的标记没有改变:

```
<s-alert type="success" autodismiss="10">
  <p>Custom tags are great!</p>
</s-alert> 
```

Enter fullscreen mode Exit fullscreen mode

默认的也一样:

```
<s-alert type="success" autodismiss>
  <p>Custom tags are great!</p>
</s-alert> 
```

Enter fullscreen mode Exit fullscreen mode

### 往前走

前端空间因快速变化而臭名昭著。这是一个炒作和时尚的地方。这可能不会改变，但是如果你选择的东西能让你和其他开发人员使用 HTML 编写 ui，那么这是一个不错的选择。如果某些东西迫使你增加大量的 kb(超过 10 分钟+gz)并编写特殊的语法，那么它对于 UI 合成来说不是一个好的选择，因为我们已经有了这样的 HTML。我们只是没有正确使用它！

能够用这种基于标准的标记编写应用程序不仅是更好的 DX，而且成本更低，因为没有什么专有的东西会不可避免地过时，需要重构。以 GitHub 的 UI 为例。不知道他们用什么建造了它，但是当我写这篇文章的时候，我看着界面想象自己用天空实验室来重建它

```
<body>
  <nav>...</nav>
  <s-tabs>
    <s-tab for="code">
      <s-icon name="code"></s-icon> Code
    </s-tab>
    <div id="code">
      <s-editor mode="md"></s-editor>
    </div>
    <s-tab for="pull-req">
      <s-icon name="merge"></s-icon> Pull requests <s-badge count="0"></s-badge>
    </s-tab>
    <div id="pull-req">
      ...
    </div> 
    <s-tab for="projects">
      <s-icon name="board"></s-icon> Projects <s-badge count="1"></s-badge>
    </s-tab>
    <div id="projects">
      ...
    </div>
  </s-tabs>
  <footer>...</footer>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

现在我知道这并没有解决应用程序状态管理和让 UI 可靠地反映状态的难题。这就是 React 和其他人着手解决的问题，他们做到了。但前端社区似乎无法采取平衡的方法来采用这些新技术，只是开始过度设计眼前的一切。尤其是在 React 社区中，这种现象非常普遍。我可以大胆地说，如果你使用 React，毫无疑问你有一个过度设计的应用程序，或者至少部分是如此。当我看到这样的事情时，我只是想知道所有的 React 开发人员到底在对自己做什么(这些是真正的 React 组件，有 100 个这样的例子):

```
<DisplayText size="extraLarge" element="h4">Good evening, Dominic.</DisplayText> 
```

Enter fullscreen mode Exit fullscreen mode

哪个输出

```
<h4 class="Polaris-DisplayText Polaris-DisplayText--sizeExtraLarge">Good evening, Dominic.</h4> 
```

Enter fullscreen mode Exit fullscreen mode

花一分钟想想那里发生了什么...

这是另一个来自一家伟大公司的应该更了解的例子:

```
<UitkInlineBadge shape="shape-pill" theme="theme-success">10% off</UitkInlineBadge> 
```

Enter fullscreen mode Exit fullscreen mode

哪个输出

```
<span class="uitk-badge uitk-badge-inline shape-pill theme-success">10% off</span> 
```

Enter fullscreen mode Exit fullscreen mode

对 React 和其他共享组件库的过度使用减少了它们的潜在收益，甚至会导致整体的负面结果。真实故事:

*一个工程师应该写十几行 CSS 来制作 Badge，还是应该写 **474 行代码跨越 8 个文件，有多个依赖项和一个强制构建管道**？*

“所以它可以扩展”我听说。所以它可以...然而，10 个实现中有 9 个没有无法扩展的危险，但所有 10 个都通过[插入最喜欢的 js 库]得到了解决，现在该应用程序拥有 10 倍于所需的代码量和极高的依赖性。能不能把*缩小*？以至于它可以离开自己的方式，根本不被需要？

这就是定制标记方法的真正意义所在。是的，定制的标记加属性设计比基于类的设计好得多(这种转变的时机已经到来)，但是能够设计和构建可在任意方向上伸缩的组件——向上或向下——并且在各种用例中遵循标准 HTML API 而没有依赖性，这是一个非常有吸引力的机会！

## 结论

定制 HTML 标签、Web 组件、定制元素规范和一些与之相近的 js 库——这是设计更好的 UI 组件和度过这个过度工程化时代的途径。

你有什么定制标签的经验想分享吗？下面评论！*