# 网格 vs. Flex:“简单”促销空间的故事

> 原文：<https://dev.to/brob/grid-vs-flex-a-tale-of-a-simple-promo-space-p96>

我喜欢 CSS 中新的布局模式。Grid 和 Flexbox 都是令人惊叹的特性。他们各有各的位置。如果我告诉你，如果你用了“错误的”那个，你可以把你的 CSS 和 HTML 翻一倍？

让我们来看一看看似简单的促销网格。

[![Image of a promo space with one 50% item spanning 2 row, 1 at the top right spanning 50% and 1 row and 2 spanning 25% and 1 row](img/221b7fd687f6d16d54a1e38091f20db9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JQB1rJKn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bryanlrobinson.cimg/simple-promo-space.jpg)

在本例中，我们有 4 个促销商品。这源于我在一家机构工作的经历。每个客户都希望他们的页面顶部有一个旋转器。我们会使用像这样的不对称宣传空间，将多个行动号召整合到一个区域。这将允许我们避开我们知道不会转换的传送带。

给用户一种设计的层次感是很重要的。为此，我们用不同的尺寸来表示重要性。

这个设计是用 Photoshop 做的。这在设计程序中很容易实现。

当时，我们仍在 Flex 中进行主要布局。事实证明，这在 Flex 中仍然相当复杂。

## Flexbox 所需的代码

它需要大量的附加标记和相当多的特定于标记和显示的额外 CSS。换句话说，代码臃肿且不灵活。

```
<div class="promos">
    <div class="left-column">
        <a href="#" class="promo">Promo Space 1</a>
    </div>
    <div class="right-column">
        <a href="#" class="promo">Promo Space 2</a>
        <div class="columns">
            <a href="#" class="promo">Promo space 3</a>
            <a href="#" class="promo">Promo space 4</a>
        </div>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
.promo {
    background-image: url(https://images.unsplash.com/photo-1552297166-e1e2a9f945d4?ixlib=rb-1.2.1&q=85&fm=jpg&crop=entropy&cs=srgb&ixid=eyJhcHBfaWQiOjE0NTg5fQ);
    background-repeat: no-repeat;
    background-size: cover;
    background-position: center center;
    background-color: lightblue;
    background-blend-mode: overlay;
}
.promos {
    display: flex;
    justify-content: space-between;
}
.promo {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 30vh;
}
.column {
    width: calc(50vw - .5rem);
    display: flex;
    flex-direction: column;
}
.columns {
    display: flex;
    justify-content: space-between;
}
.columns > .promo {
    flex: 1;
}
.columns > .promo:first-child {
    margin-right: 1rem;  
}
.right-column > .promo {
    margin-bottom: 1rem;
}
.left-column .promo {
    height: 100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的行数是 12 行 HTML 和 40 行基于布局的 CSS。

## 网格所需的代码

让我们来看看让这个布局与 Grid 一起工作需要什么。

```
<div class="promos">
    <a href="#" class="promo">Promo Space 1</a>
    <a href="#" class="promo">Promo Space 2</a>
    <a href="#" class="promo">Promo space 3</a>
    <a href="#" class="promo">Promo space 4</a>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

没有不必要的嵌套。一个集装箱里只有四个促销空间。这是这种布局的理想标记量。

因为 Grid 允许我们指定我们的布局并将我们的内容放入布局中，所以我们省去了许多需要额外标记的麻烦。

HTML 的行数是六。正好是我们的 flex 示例的一半行数。我们的 CSS 行数是 20。正好是我们 CSS 行数的一半！

在这种情况下，我们使用 grid-gap 来保持我们的空白，使用 grid-template-columns 来指定我们的列大小，使用 grid-template-areas 来定义我们可以放置内容的区域。

```
.promos {
    display: grid;
    grid-template-columns: 2fr 1fr 1fr;
    grid-template-areas: "main second second"
                         "main third  fourth";
    grid-auto-rows: minmax(20vh, 1fr);
    grid-gap: 1rem;
}
.promo:first-child {
    grid-area: main;
}
.promo:nth-child(2) {
    grid-area: second;
}
.promo:nth-child(3) {
    grid-area: third;
}
.promo:nth-child(4) {
    grid-area: fourth;
} 
```

Enter fullscreen mode Exit fullscreen mode

每个宣传片都用 grid-area 属性获得自己的命名区域，如此而已。不需要更多代码。这不是黑客。这不是布局技巧与附加标记的结合。这是作为网络一等公民的布局。

## 结论

这两个版本都可以。Flexbox 在支持方面仍有优势。不过，想想这个。如果一切都相同，那么减少一半的代码更有利于长期维护。关键在于为工作选择合适的工具。

这是一个做网页设计工作的好时机。以前不可能的事情现在变成了可能。以前非常困难的事情现在变得惊人的简单。

有哪些设计模式比它们应该的更令人讨厌，网格让事情变得超级简单。

### 实用 CSS 网格——了解这场网页设计革命！

无论您是 CSS Grid 的新手还是玩家，找到这种新布局机制的实际例子是了解它的威力的最好方法。在下面注册两个小时专门为您提供的实用网格知识！

现在就开始学习吧！