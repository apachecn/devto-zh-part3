# HTML 细节太棒了

> 原文：<https://dev.to/isfotis/html-details-is-awesome-1c20>

*原贴于[https://fotis.xyz/posts/details-is-awesome/](https://fotis.xyz/posts/details-is-awesome/)T3】*

前阵子，我看了[穆安丘](https://twitter.com/muanchiou)的一次演讲的幻灯片和笔记，题目是[《细节上的<>](https://github.com/muan/details-on-details)。这是关于如何使用 HTML 的`<details>`元素的精彩概述。

简而言之，`details`和它的配对`summary`，是一种实现本机可折叠部分的方法，不依赖于 Javascript。切换内容是 web 上最常见的操作之一，因此能够在没有脚本的情况下提供这种功能很有吸引力。然而，它不仅仅是可折叠的部分；你可以用它制作面板、菜单和其他富有想象力的东西！

我已经把这些知识藏在了心里。几周前，我在这个博客上实现了一个可折叠的网页提及部分:

[![A webmentions section, with a button to show all comments](img/30c3fe5b787ebd2aa66d9f08b4a05ebe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aixljSFJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hdi65xwmq03zb4wapqob.jpg)

这似乎是一个很好的细节用例。我将获得切换的本机功能，然后可以对其进行样式化处理。让我们来看看如何实现它。

## 实现一个“显示全部”部分

### Markup

您将`<summary>`嵌套在`<details>`中。`<summary>`是显示为“切换按钮”的东西，不管该部分是否折叠。之后的任何内容都将被隐藏，只有在小部件打开时才会显示。

下面是我们评论区的初步尝试:

```
<details>
  <summary>
    Show all (2)
  </summary>
  <ol>
    <li>I am a comment</li>
    <li>I am another comment.</li>
  </ol>
</details> 
```

[https://codepen.io/fpapado/embed/bZbbPr?height=600&default-tab=result&embed-version=2](https://codepen.io/fpapado/embed/bZbbPr?height=600&default-tab=result&embed-version=2)

### 造型

关于造型细节，还有一些额外的事情需要了解。

箭头的样式是用`summary`上的`list-style`属性，除了在 Chrome 中使用`::-webkit-details-marker`。你可以考虑重置它们，然后依靠一个统一的`::before`选择器。

当摘要打开时，添加`open`属性。您可以将它查询为`details[open]`来进一步设计容器的样式。[MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/details)上的演示在实践中展示了 open 属性。

我还发现，在 Safari 上，`summary`元素不能是 flexbox 容器，即使是在 CSS 中定义的。我想在“显示全部”文本旁边显示作者图像。我在 summary 中添加了一个额外的 div 包装器，作为 flexbox 包装器。

在我们的细节中添加更多的样式后，它看起来可能是这样的:

```
<details class="details-reset">
  <summary class="summary fg-highlight cursor-default">
    <div class="flex-wrapper-for-safari">
      <span>Show all (2)</span>
      <div class="author-list">
        <div class="author">
          <img src="https://fotis.xyz/img/logo.png" alt="" class="author-image" />
        </div>
        <div class="author">
          <img src="https://fotis.xyz/img/logo.png" alt="" class="author-image" />
        </div>
      </div>
    </div>
  </summary>

  <ol>
    <li>I am a comment</li>
    <li>I am another comment</li>
  </ol>
</details> 
```

```
:root {
  --highlight: #da0993;
}

/**
  * Styles and resets for the <details> element.
  * @see https://github.com/primer/primer/blob/master/modules/primer-buttons/lib/button.scss#L206-L213
*/

/* Remove marker added by the display: list-item browser default */
.details-reset > summary {
  list-style: none;
}

/* Remove marker added by details polyfill */
.details-reset > summary::before {
  display: none;
}

/* Remove marker added by Chrome */
.details-reset > summary::-webkit-details-marker {
  display: none;
}

/* Other styles */
.flex-wrapper-for-safari {
  display: flex;
  align-items: center;
}

.fg-highlight {
  color: var(--highlight);
}

.cursor-default {
  cursor: default;
}

.author-list {
  display: flex;
  margin: 1rem;
}

.author {
  width: 2rem;
  height: 2rem;
}

.author-image {
  max-width: 100%;
  border-radius: 100%;
  background-color: gray;
} 
```

[https://codepen.io/fpapado/embed/JzPPqQ?height=600&default-tab=result&embed-version=2](https://codepen.io/fpapado/embed/JzPPqQ?height=600&default-tab=result&embed-version=2)

### 聚焦启示

实际上，我们的部分摘要有两个不同的元素；“显示全部”按钮，以及评论者的个人资料图片。就输入法和高亮显示而言，整个摘要是可交互的。这从一开始就是有意义的，也是使用语义 HTML 的一个好处。

我认为我们可以做得更好一点，通过加强重点，使它更清楚地突出行动。如果用户(通过键盘或其他方式)关注该部分，我只想突出显示“全部显示”部分。这种“嵌套焦点”模式是我从 Heydon Pickering 的文章“可折叠部分”中摘录的。

```
<details class="details-reset">
  <summary class="nested-focus fg-highlight cursor-default">
    <div class="flex-wrapper-for-safari">
      <span class="nested-focus-target">Show all (2)</span>
      <div class="author-list">
        <div class="author">
          <img src="https://fotis.xyz/img/logo.png" alt="" class="author-image" />
        </div>
        <div class="author">
          <img src="https://fotis.xyz/img/logo.png" alt="" class="author-image" />
        </div>
      </div>
    </div>
  </summary>

  <ol>
    <li>I am a comment</li>
    <li>I am another comment</li>
  </ol>
</details> 
```

```
:root {
  --highlight: #da0993;
}

.nested-focus:focus {
  /* NOTE: The only good reason to hide an outline is if you enhance it */
  outline: none;
}

.nested-focus:focus .nested-focus-target {
  outline: 2px solid var(--highlight)
}

/** The rest is the same as above */ 
```

[https://codepen.io/fpapado/embed/mobbZE?height=600&default-tab=result&embed-version=2](https://codepen.io/fpapado/embed/mobbZE?height=600&default-tab=result&embed-version=2)

其他要考虑的事情是`user-select`和`cursor`。`user-select`尤其是一个悬而未决的问题，所以我把它原封不动地留了下来。我确实给了`summary`一个`cursor: default`，尽管肯定有不同的方式来区分优先级。

## 聚合填充

详细信息的 [polyfill 很棒。它的体积很小(不到 1.5kB)，并且很好地覆盖了 IE 和 Edge。这也是分层聚合填充的一个很好的例子，其中部分功能是根据支持的内容聚合填充的。给源代码看一下，从中我学到了一堆。](https://github.com/javan/details-element-polyfill)

## 可达性

Scott O'Hara 研究了 details 元素在各种屏幕阅读器和浏览器组合中的使用。我强烈建议你阅读这篇文章以获取更多的信息和数据。总结一下，屏幕阅读器支持是好的，有一些边缘情况悬而未决。

(我很乐意在我自己研究过这个问题，并检查了 Scott 提到的 bug 的状态之后，在这个问题上做更多的补充)。

除了 Scott 的注释之外，我还想指出一些关于可折叠部分的事情。

### 避免改变显示文本

你可能会注意到，在上面的例子中，我避免了在展开时更改“显示全部”文本。您可以用 Javascript，甚至是一些依赖于`open`属性的 CSS 来做到这一点。但是要小心！在切换按钮中，不改变文本是很常见的，因为状态已经通过`aria-expanded`传递了([海登·皮克林写了关于切换按钮的文章](https://inclusive-components.design/toggle-button/))。我认为带有 details 元素的可折叠部分应该遵循同样的思想，因为状态是以本机方式传递的。

如果我确实想改变显示文本，我会尝试总是向屏幕阅读器显示相同的文本，并与切换按钮保持对等。

```
<details>
  <summary>
    <!-- This is exposed to AT -->
    <span class="visually-hidden">Show all (2)</span>
    <!-- These will be hidden from AT -->
    <span aria-hidden="true" class="summary-open">Hide all</span>
    <span aria-hidden="true" class="summary-closed">Show all (2)</span>
  </summary>
  <ul>
    <li>I am a comment</li>
    <li>I am another comment.</li>
  </ul>
</details> 
```

```
/* Hide visually, but keep in the tree for AT */
.visually-hidden {
  position: absolute !important;
  height: 1px; width: 1px;
  overflow: hidden;
  clip: rect(1px 1px 1px 1px);
  clip: rect(1px, 1px, 1px, 1px);
}

details .summary-open {
  display: none;
}
details[open] .summary-open {
  display: inline;
}
details .summary-closed {
  display: inline;
}
details[open] .summary-closed {
  display: none;
} 
```

然而，记住 ARIA 用法的[规则，评估这个特性的价值，我不会实现它。如果“全部显示”在视觉上持续存在，这没什么大不了的，我喜欢这种统一的呈现方式。此外，箭头已经是一个很好的指示器，所以如果你需要这个特性，那么考虑不要删除它。](https://www.w3.org/TR/using-aria/)

### 避免`summary`中的互动内容

[总的来说互动内容是规范上的开放式问题](https://github.com/whatwg/html/issues/2272)。我也怀疑这样做的必要性。比方说，我不会把一个`button`放在一个`button`里面，同样，我也不会在 summary 里面嵌套一些交互的东西。您的里程数可能会有所不同，如果您有这个用例，这可能是一个返工小部件的机会。

## 包装完毕

细节元素是 HTML 的重要组成部分。它提供了许多不依赖于 JS 的普通功能。谈到弹性，这一点很重要。在请求失败或浏览器负载过重的情况下(比如当合并服务器呈现的标记时)，能够不依赖于主脚本来折叠部分是非常有价值的。

Github 还有更多[有趣的细节使用，比如模态和菜单。它有几个自己的风格挂钩，甚至还有一些 UX 问题要探索。polyfill 的内容丰富，可访问性的故事也相当可靠。](https://github.com/muan/details-on-details#githubs-details-dependent-custom-elements)

你尝试过在你的项目中使用细节元素吗？你是怎么找到它的？让我知道！

## 更多资源

*   [慕安的演讲和笔记](https://github.com/muan/details-on-details#githubs-details-dependent-custom-elements)让我走上这条路。它提供了简明的解释和指导，非常适合做书签和参考，比如说，在写文章的时候:)
*   关于细节的 MDN 指南有一个很好的总结(没有双关语)
*   [详细规格](https://html.spec.whatwg.org/multipage/interactive-elements.html#the-details-element)
*   [详细的 poly fill](https://github.com/javan/details-element-polyfill)
*   Heydon Pickering 的 Inclusive Components 有一章解释了可折叠部分提供的各种[启示。他还详细介绍了如何用 Javascript 实现一个。读一读吧，这是好东西。](https://inclusive-components.design/collapsible-sections)