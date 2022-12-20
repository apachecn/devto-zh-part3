# 功能优先的手风琴

> 原文：<https://dev.to/lowmess/functional-first-accordions-4opn>

> **更新(2019 年 12 月):**
> 
> 在我写这篇文章的一年里，越来越清楚的是，这不是制作手风琴的好方法。除了使东西功能化的所有工作(你知道，这篇文章的重点)， [Dave Rupert 最近写了一篇短文，详细说明了这种方法的可访问性缺点](https://daverupert.com/2019/12/why-details-is-not-an-accordion/)。我同意 Dave 的观点，即 web 作为一个平台，需要将一些常见的控件带到桌面上，以确保所有用户都能获得一致、可访问的体验。

很少有 UI 组件像 accordion 一样常见。即使你不熟悉这个术语，你也会熟悉它的模式:一串文本，有时伴随着一个按钮或图标，当你点击它时，会显示更多的内容。成千上万的开发人员和数百个 UI 框架已经创建了 accordions，通常使用他们自己的僵硬的标记结构和(可能膨胀的)JavaScript。但是你知道吗，大多数现代浏览器都配有功能完善的手风琴组件。看看这个:

这是我的酷手风琴

100% HTML，宝贝。#使用平台

那都是 HTML！代码是这样的:

```
<details>
  <summary>This is my cool accordion</summary>

  <p>100% HTML, baby. #usetheplatform</p>
</details> 
```

帮助我们实现这一现代工程奇迹的元素被称为 [`details`](http://developer.mozilla.org/en-US/docs/Web/HTML/Element/details) (以及`summary`来设置自定义标题)。它允许我们创建在元素被点击之前不可见的内容。听起来像手风琴！

当然，`details`元素也有一些缺点。首先，这并不完全是我所说的有吸引力。没有办法将内容动画化，也没有办法调整“扭曲”(指示组件状态的三角形图标)。此外，在*大多数*现代浏览器中工作意味着它不能在*所有*现代浏览器中工作，更不用说传统浏览器了。但是，就像我们的 UI 框架开发前辈一样，我们可以使用一些 CSS & JavaScript 来解决这些问题，并且有一个额外的好处，即当 JavaScript(甚至 CSS！)已禁用。

## 美在细节

我们应该做的第一个也是最简单的改变就是让元素在所有浏览器中显示一致。

```
details {
  display: block;
}
summary {
  display: list-item;
} 
```

同样简单，但更固执己见的是，改变光标以突出元素是交互式的。

```
summary {
  cursor: pointer;
} 
```

现在让我们进入有趣的东西。首先是改变默认的扭曲，使之更接近惯例。

```
/* Hide the default twistie */
/* Spec-compliant: */
summary {
  list-style-type: none;
}
/* Non-standard: */
summary::-webkit-details-marker {
  display: none;
}

/* Display a more common one */
summary {
  position: relative;
  padding-right: 1.5rem;
}
summary::after {
  content: '+';
  position: absolute;
  top: calc(50% - 0.5em);
  right: 0;
}
details[open] summary::after {
  content: '-';
} 
```

[这是手风琴](https://codepen.io/lowmess/pen/zMjagE)此时的样子:

[https://codepen.io/lowmess/embed/zMjagE?height=600&default-tab=result&embed-version=2](https://codepen.io/lowmess/embed/zMjagE?height=600&default-tab=result&embed-version=2)

对于几行 CSS 来说还不错！不过，在称之为“好看”之前，我还会做一些修改。

```
details {
  border-bottom: 1px solid #eee;
  padding: 1rem;
}
:not(details) + details,
details:first-of-type {
  border-top: 1px solid #eee;
}

summary {
  margin-bottom: 0;
  font-weight: 700;
}
summary + * {
  margin-top: 1rem;
} 
```

[现在我们有了一个相对有吸引力的手风琴组件](https://codepen.io/lowmess/pen/eQrjBG)。我们现在可以停止，我们将有一个工作组件，通过一些调整，可以放在几乎任何网站上，只有 Work™️.

[https://codepen.io/lowmess/embed/eQrjBG?height=600&default-tab=result&embed-version=2](https://codepen.io/lowmess/embed/eQrjBG?height=600&default-tab=result&embed-version=2)

## 妖娆渐次增强

但是，我们不想被过去的 UI 框架超越。所以我们需要做更多的工作来使内容动画化。基本标记确实需要改变，变得不那么干净，我们需要一些新的 CSS。

```
<details>
  <summary>An updated details accordion</summary>

  <div class="content">
    <p>I'm some content!</p>
  </div>
</details> 
```

```
.content {
  overflow-y: hidden;
  transition: all 0.4s ease;
}

.content.is-closed {
  max-height: 0;
  margin-top: 0;
  margin-bottom: 0;
  padding-top: 0;
  padding-bottom: 0;
  opacity: 0;
} 
```

您可能已经注意到，我们没有将`is-closed`类应用于标记中的内容。因为我们使用 JS 来触发该类，并且它隐藏了内容，所以我们只希望在 JS 被允许在页面上运行时应用该类。

```
for (const content of document.querySelectorAll('details .content')) {
  content.classList.add('is-closed')
} 
```

在触发动画之前，我们还需要设置最后一个部分。当元素关闭时，它有一个应用的`0`的`max-height`。由于`auto`声明是不可动画化的，我们需要在它打开时将一个已定义的`max-height`应用到内容中。为了尽可能平滑的动画，那个`max-height`应该和内容高度一样大。我们可以编写一个简单的函数来计算这个高度，并将其作为属性存储在 content 元素上。

```
const getContentHeight = node => {
  // Force node to display properly
  node.classList.remove('is-closed')
  // Calculate height and store it
  node.setAttribute('data-height', `${node.getBoundingClientRect().height}px`)
  // Reset node to initial state
  node.classList.add('is-closed')
} 
```

现在我们已经考虑了这个问题，我们可以处理元素上的交互。在一点过早的优化中，我们将[将事件](https://javascript.info/event-delegation)委托给文档。

```
document.addEventListener('click', event => {
  if (!event.target.closest('summary')) {
    return
  }

  event.preventDefault()
}) 
```

在这一点上，我们告诉浏览器的只是“如果用户点击了一个`<summary>`元素，不要做任何事情”。与单击`<summary>`元素相关的默认行为是设置/移除其父`<details>`元素上的`open`属性(然后浏览器知道根据该属性隐藏/显示`<details>`中的内容)。浏览器不会在隐藏内容之前等待内容动画出来，所以我们需要在我们的处理程序中手动完成。这样做的代码相当简单，归结起来就是在指定的时间以特定的顺序切换属性和类。

```
// Inside of our event handler
const accordion = event.target.closest('details')
const content = accordion.querySelector('.content')

// Handle closing
if (accordion.hasAttribute('open')) {
  // Animate content out
  content.style.removeProperty('max-height')
  content.classList.add('is-closed')
  // Wait for animation to finish, then remove the `open` attribute
  window.setTimeout(() => {
    accordion.removeAttribute('open')
  }, 400)
  // Exit handler
  return
}

// Handle opening
// Set the `open` attribute so the content will display
accordion.setAttribute('open', '')
// If our content does not have a calculated height, calculate it
if (!content.hasAttribute('data-height')) {
  getContentHeight(content)
}
// Wait a beat for the height to calculate, then animate content in
window.setTimeout(() => {
  content.style.maxHeight = content.getAttribute('data-height')
  content.classList.remove('is-closed')
}, 0) 
```

[把所有这些放在一起](https://codepen.io/lowmess/pen/yQjRXy)给了我们一个全功能、动画的手风琴组件。因为我们从一个 HTML 元素开始，默认情况下它为我们提供了核心功能，所以用户不需要运行 JavaScript 来访问隐藏的内容。

[https://codepen.io/lowmess/embed/yQjRXy?height=600&default-tab=result&embed-version=2](https://codepen.io/lowmess/embed/yQjRXy?height=600&default-tab=result&embed-version=2)

## 额外学分

尽管我们创建的 accordion 很棒，但我们只讨论了基本功能。我们有几种方法可以进一步更改或改进功能，包括但不限于:

### 提高可达性

因为`<details>`元素是固定在平台上的，所以默认情况下它应该是可访问的。但是，这只适用于实际支持该元素的浏览器。如果您的用户来自不支持该元素的浏览器，那么应该考虑可访问性(例如，添加`tabindex`和`aria-`属性)。

### 处理窗口大小调整

我们用来将手风琴内容移入和移出视图的垂直扩展动画流畅而自然，但有一个问题:它没有响应能力。因为我们只计算一次元素的高度，如果元素宽度变化到内容流到新行的程度，动画就会中断。一个理想的解决方案会考虑到这一点，我们可以通过多种方式做到这一点。

### 增加交互目标尺寸

我们在`<details>`元素上设置了容器填充，让手风琴空间的内容能够呼吸。然而，这意味着要打开手风琴，你必须点击或轻触`<summary>`内容。独立设置`<summary>`和内容容器上的填充应该会使手风琴更容易使用。

### 动画转体

动画转体来补充内容动画将带来另一个层次的波兰到我们的手风琴。额外加分:让动画发生在交互上(而不是依赖于`[open]`属性)。

### 强制内容在非屏幕环境中显示

在基于屏幕的环境中，手风琴可以带来很多东西，但是在非屏幕环境中(比如打印或使用屏幕阅读器时)，它们也会使内容不可访问。内容应该在所有环境中都可以访问。

### 一次只打开一个项目

开玩笑的。如果[你需要一架手风琴](https://www.nngroup.com/articles/accordions-complex-content/)，你可能[不应该这样做](https://www.smashingmagazine.com/2017/06/designing-perfect-accordion-checklist/#designing-interaction-for-the-accordion)。因为这不是用户直接请求的动作，所以它会导致挫败感。

### 将 Accordion 封装成可重用的组件

我们可能应该抽象掉所有这些标记，以确保一致性并提高 DX。像 React 和 Vue 这样的框架提供了一种简单的方法来做到这一点，但是在大多数模板语言中也应该是可行的。