# Flexbox 101 号

> 原文：<https://dev.to/christopherkade/flexbox-101-4hl6>

Flexbox 是当今 UI 开发世界的必备工具，因为它可以让您在创纪录的时间内创建响应迅速且优雅的布局🙆‍♂️

我们一次看一个具体的例子，构建下面的 [UI](https://codepen.io/christopherkade/pen/MdyQJE) 包含一个导航条和一个卡片列表。

在每一步中，我们都将学习基本的 Flexbox 概念，让您有一个正确的开始，所以请随意创建您自己的 [codepen](https://codepen.io/) 来尝试它们。

## 用导航条学习基础知识

我们的 HTML 看起来会像这样:

```
<nav>
  <a href="#">
    <img src="https://image.flaticon.com/icons/svg/145/145867.svg"/>
  </a>
  <a href="#">Home</a>
  <a href="#">Blog</a>
</nav> 
```

Enter fullscreen mode Exit fullscreen mode

左边有一个图标，右边有两个链接，非常标准的东西。

这个 navbar 会用到三个 Flexbox 概念:`display`、`justify-content`和`align-items`，我们就来一一介绍一下。

### 显示

```
nav {
  display: flex;
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 Flexbox 时，您要输入的第一件事。它使得我们的`nav`的每个孩子都可以利用 flex 上下文。

我们现在可以开始移动容器周围的子容器了。

### 自圆其说-内容

```
nav {
  display: flex;
  justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly;
} 
```

Enter fullscreen mode Exit fullscreen mode

它决定了我们在主轴上放置容器元素的位置。每一个都是不言自明的，但是值得一试。

在我们的例子中，我们希望我们的项目在导航栏的右边(末端):

```
nav {
  display: flex;
  justify-content: flex-end;
} 
```

Enter fullscreen mode Exit fullscreen mode

“但是克里斯，通过使用值`flex-end` **，所有的东西**都到了我的容器的末尾，甚至是图标！”，你是绝对正确的，我们希望我们的图标在左边，而其他一切都在右边。为此，我们可以使用一个非常巧妙的技巧，我们将图标的`margin-right`设置为 auto，告诉我们的布局自动将图标放在最左边，就像这样。

```
/* Target the first link in our navbar (the icon)  */
nav > a:first-child {
  margin-right: auto;    
} 
```

Enter fullscreen mode Exit fullscreen mode

一切都开始变得更好看了，尽管我们仍然需要将图标垂直居中。

### 对齐-项目

```
nav {
  align-items: stretch | flex-start | flex-end | center | baseline;
} 
```

Enter fullscreen mode Exit fullscreen mode

它决定了容器的子容器在横轴上的位置(横轴垂直于`justify-content`使用的轴)。

在我们的例子中，我们希望垂直对齐我们的导航栏项目，就像这样:

```
nav {
  display: flex;
  justify-content: flex-end;
  align-items: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们有了一个完全成熟的 navbar，并且我们学习了 Flexbox 的三个基本概念！🎉

### 奖励:伸缩方向

尽管我们在这里没有使用它，但我认为它值得一提`flex-direction`。

```
.container {
  flex-direction: row | row-reverse | column | column-reverse;
} 
```

Enter fullscreen mode Exit fullscreen mode

就像它的名字一样，它决定了容器的主轴，可以把它看作是创建一个水平(默认)或垂直布局。

试试吧！您将会看到，将其定义为`column`将会得到预期的结果:垂直布局。

## 再往前一点用卡片列表

好了，你可能已经看到了之前分享的[示例 UI](https://codepen.io/christopherkade/pen/MdyQJE) ，我们现在将致力于创建这个卡片布局，并使其具有响应性(由于 Flexbox，这变得非常容易)。

下面是我们的 HTML 的样子:

```
<article class="card-container">

  <div class="card">
    <a class="card-link" href="#">       
      <time>May 4, 2019 | 3 min read</time>
      <span>
        <h2 class="card-title">My awesome card</h2>
        <p class="card-description">...with a description !</p>
      </span>
    </a>
  </div>

  <!-- More cards below -->
</article> 
```

Enter fullscreen mode Exit fullscreen mode

我们将在**多行**展示大量卡片，这些卡片可以是文章(就像在【christopherkade.com】的[)、待办事项或任何你喜欢的东西。](https://christopherkade.com/blog)

要做到这一点，我们只需要一个新概念:`flex-wrap`。

### 柔性包装

它告诉我们的容器在需要的时候把它的项目包装成多行。默认情况下,`flex`会将每个项目放在同一行。

```
.card-container {
  flex-wrap: nowrap | wrap | wrap-reverse;
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们的`.card-container`现在看起来像下面这样:

```
.card-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

> 试着移动你的窗口大小，它会做出反应，并自动显示适合每一行的卡片数量。

## 等等，就这样？

是啊，Flexbox 入门并不难。当然还有更多的东西，但是这已经足够产生很酷的布局了。

如果你想更进一步，一定要看看 CSS-Trick 的[Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)完全指南，这是写这篇文章的主要灵感。

当然，如果你有任何关于 Flexbox、整个 CSS 或 Javascript 的问题，请不要犹豫，在 Twitter 上发给我 [@christo_kade](https://twitter.com/christo_kade) 😄