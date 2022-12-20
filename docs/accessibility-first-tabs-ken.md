# 辅助功能优先:选项卡

> 原文：<https://dev.to/link2twenty/accessibility-first-tabs-ken>

我已经决定拿起这个系列的另一个元素。我受到了 [@lkopacz](https://dev.to/lkopacz) 关于可访问性和 javascript 的帖子的启发，它值得一读，以制作需要 javascript 但又保持其可访问性的东西。

[![lkopacz](img/cd7d14f0da8b5733c447c96ff9f5a2f6.png)](/lkopacz) [## a11y 和 JS——一段看似标新立异的恋情

### 林赛·科帕奇 1 月 28 日 196 分钟阅读

#a11y #javascript #frontend #discuss](/lkopacz/a11y-and-js---a-seemingly-unconventional-romance-24i0)

我决定制作一种选项卡式导航的形式，它松散地遵循材料设计规范。我们的成品看起来会有点像这样

[https://jsfiddle.net/link2twenty/v2d1htzx/embedded/result//dark](https://jsfiddle.net/link2twenty/v2d1htzx/embedded/result//dark)

## 要求

为了让我们的标签可访问，我们需要能够使用键盘和鼠标与它们交互，我们也不能假设我们的用户是视力正常的。

### 键盘:

*   **Tab 键**，我们必须能够使用选项卡来沿着选项卡移动焦点
*   返回键，我们必须能够按下返回当一个标签是焦点移动到它
*   **空格键**，空格键应该像回车键一样
*   **Home 键**，我们必须选择列表中的第一个选项卡
*   **结束键**，我们必须选择列表中的最后一个选项卡
*   **箭头键**，我们必须能够移动到下一个或上一个标签时，按下右键或左键，但只有当焦点在我们的`tablist`

*这些键盘要求可以在[这里找到](https://www.w3.org/TR/wai-aria-practices/examples/tabs/tabs-1/tabs.html#kbd_label)T3】*

### 鼠标:

*   **点击选项卡上的**会将该选项卡设置为活动状态
*   悬停应该给出目标的一些指示

### 无视力:

*   依靠**键盘支持**
*   必须使用**屏幕阅读器**

我相信这就是我们所需要的，尽管如果我错了请告诉我，我也相信上面的例子符合我们清单上的每一项。所以我们继续吧。

## Markup

我有一个包含整个选项卡“元素”的`<div>`,它需要一个 ID，这样我们就可以在稍后的 javascript 和`tab-container`类中找到它，这样我们就可以用 CSS 对它进行样式化。

现在我们有一些角色，角色告诉浏览器每个元素应该如何处理，我们有一个`<ul>`和角色 [`tablist`](https://www.w3.org/TR/wai-aria-1.1/#tablist) 。这让我们的浏览器知道我们列出了一些标签，这意味着当屏幕阅读器看到标签时，它会说“两个选中的标签中的一个”。

接下来，我们有一个角色为 [`tab`](https://www.w3.org/TR/wai-aria-1.1/#tab) 的`<li>`，这些是我们用于控制整个“元素”的“按钮”，我们必须给每个选项卡 0 的 [`tabindex`](https://html.spec.whatwg.org/multipage/interaction.html#attr-tabindex) ，并且每个选项卡必须有一个 [`aria-control`](https://www.w3.org/TR/wai-aria-1.1/#aria-controls) 属性，即对应面板的 ID。最后，还有一个 [`aria-selected`](https://www.w3.org/TR/wai-aria-1.1/#aria-selected) ，它包含真或假，这取决于该选项卡是否是活动的/选中的选项卡。

最后，让我们看一下`<main>`内容，我们为每个面板都准备了一个`<div>`，它们需要角色 [`tabpanel`](https://www.w3.org/TR/wai-aria-1.1/#tabpanel) ，我们还需要 [`aria-expanded`](https://www.w3.org/TR/wai-aria-1.1/#aria-expanded) 属性，该属性根据面板是否活动/展开来判断是真还是假。ID 属性是必需的，它对应于`<li>`元素的`aria-control`属性。

```
<div id="some_ID" class="tab-container">
  <ul role="tablist">
    <li role="tab" aria-controls="some_ID_1" tabindex="0" aria-selected="true">Tab 1</li>
    <li role="tab" aria-controls="some_ID_2" tabindex="0" aria-selected="false">Tab 2</li>
  </ul>
  <main>
    <div id="some_ID_1" role="tabpanel" aria-expanded="true">
      <p>
        content for 1
      </p>
    </div>
    <div id="some_ID_2" role="tabpanel" aria-expanded="false">
      <p>
        content for 2
      </p>
    </div>
  </main>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

以下是示例中的标记。

[https://jsfiddle.net/link2twenty/d3kc2qgv//embedded/html,result//dark](https://jsfiddle.net/link2twenty/d3kc2qgv//embedded/html,result//dark)

## 样式

我不会详细介绍这些风格，因为它们是我个人的喜好，但我会指出几件事。

除了类`.tab-container`之外，我尝试使用角色作为选择器，这意味着如果我错过了一个选择器，这将是显而易见的，但这也使代码更干净。

我有悬停效果，但没有聚焦效果，我认为你用`tabindex`得到的轮廓应该足够了，如果你不同意，请随时打电话给我。

```
.tab-container {
  overflow: hidden;
  background: #fff;
}

.tab-container [role=tablist] {
  display: flex;
  margin: 0;
  padding: 0;
  box-shadow: 0 3px 6px rgba(0, 0, 0, 0.16), 0 3px 6px rgba(0, 0, 0, 0.23);
}

.tab-container [role=tab] {
  position: relative;
  list-style: none;
  text-align: center;
  cursor: pointer;
  padding: 14px;
  flex-grow: 1;
  color: #444;
}

.tab-container [role=tab]:hover {
  background: #eee;
}

.tab-container [role=tab][aria-selected=true] {
  color: #000;
}

.tab-container [role=tab][aria-selected=true]::after {
  content: "";
  position: absolute;
  width: 100%;
  height: 4px;
  background: #f44336;
  left: 0;
  bottom: 0;
}

.tab-container main {
  padding: 0 1em;
  position: relative;
}

.tab-container main [role=tabpanel] {
  display: none;
}

.tab-container main [role=tabpanel][aria-expanded=true] {
  display: block;
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们将样式添加到示例中。

[https://jsfiddle.net/link2twenty/hm32w5n0//embedded/css,result//dark](https://jsfiddle.net/link2twenty/hm32w5n0//embedded/css,result//dark)

## JavaScript

开始了，我要添加一些 javascript。这意味着选项卡将不再可用，对吗？当然不是，我们来看看。

同样，我不会讲太多细节，因为，真的，这只是一堆事件监听器。你可能想知道我为什么用一个类，因为我喜欢它们，你不必用一个类，我只是喜欢用它们。

我使用了和 CSS 一样的选择器样式，它对我来说很有意义。我只有一个公共函数，它所做的就是改变`aria-selected`和`aria-expanded`属性。我们的 CSS 处理所有的样式变化。

```
class TabController {
  constructor(container) {
    this.container = document.querySelector(container);
    this.tablist = this.container.querySelector('[role=tablist]');
    this.tabs = this.container.querySelectorAll('[role=tab]');
    this.tabpanels = this.container.querySelectorAll('[role=tabpanel]');
    this.activeTab = this.container.querySelector('[role=tab][aria-selected=true]');

    this._addEventListeners();
  }

  // Private function to set event listeners
  _addEventListeners() {
    for (let tab of this.tabs) {
      tab.addEventListener('click', e => {
        e.preventDefault();
        this.setActiveTab(tab.getAttribute('aria-controls'));
      });
      tab.addEventListener('keyup', e => {
        if (e.keyCode == 13 || e.keyCode == 32) { // return or space
          e.preventDefault();
          this.setActiveTab(tab.getAttribute('aria-controls'));
        }
      })
    }
    this.tablist.addEventListener('keyup', e => {
      switch (e.keyCode) {
        case 35: // end key
          e.preventDefault();
          this.setActiveTab(this.tabs[this.tabs.length - 1].getAttribute('aria-controls'));
          break;
        case 36: // home key
          e.preventDefault();
          this.setActiveTab(this.tabs[0].getAttribute('aria-controls'));
          break;
        case 37: // left arrow
          e.preventDefault();
          let previous = [...this.tabs].indexOf(this.activeTab) - 1;
          previous = previous >= 0 ? previous : this.tabs.length - 1;
          this.setActiveTab(this.tabs[previous].getAttribute('aria-controls'));
          break;
        case 39: // right arrow
          e.preventDefault();
          let next = [...this.tabs].indexOf(this.activeTab) + 1;
          next = next < this.tabs.length ? next : 0
          this.setActiveTab(this.tabs[next].getAttribute('aria-controls'));
          break;
      }
    })
  }

  // Public function to set the tab by id
  // This can be called by the developer too.
  setActiveTab(id) {
    for (let tab of this.tabs) {
      if (tab.getAttribute('aria-controls') == id) {
        tab.setAttribute('aria-selected', "true");
        tab.focus();
        this.activeTab = tab;
      } else {
        tab.setAttribute('aria-selected', "false");
      }
    }
    for (let tabpanel of this.tabpanels) {
      if (tabpanel.getAttribute('id') == id) {
        tabpanel.setAttribute('aria-expanded', "true");
      } else {
        tabpanel.setAttribute('aria-expanded', "false");
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以实例化一个选项卡导航的实例，如下所示

```
const someID = new TabController('#some_ID'); 
```

Enter fullscreen mode Exit fullscreen mode

将这一切结合在一起

[https://jsfiddle.net/link2twenty/v2d1htzx/embedded/js,result//dark](https://jsfiddle.net/link2twenty/v2d1htzx/embedded/js,result//dark)

## 注销

我希望你喜欢这篇小文章，并在你的任何网站上随意使用这些技术，或者全部。我真的很感兴趣听到任何方法，你可能有这样做，没有 JavaScript，我认为这可以用一个电台组，但我现在不打算尝试。

感谢您的阅读！
🦄❤🦄🦄🧠❤🦄