# 如何用 CSS 创建非画布导航菜单

> 原文：<https://dev.to/adyngom/how-to-create-an-off-canvas-navigation-menu-with-css-1o99>

通过像手机上的脸书这样的应用程序，离画布菜单可以节省宝贵的屏幕空间，只在需要时切换导航到视图中。我们将深入探讨一种使用一些被忽略的 CSS 属性来实现这种效果的方法。

[https://www.youtube.com/embed/lqrCpg2a-24](https://www.youtube.com/embed/lqrCpg2a-24)

### 目标和挑战

1.  通过隐藏导航创建额外的屏幕空间
2.  通过点击离开画布图标并将其滑动到位，轻松访问菜单
3.  只有 CSS 将被用于效果
4.  尽可能支持多种浏览器

### 标记

从包装器 div 开始，我们将使用一些新的 HTML5 元素来托管两个主要元素:导航和内容

```
<div class="wrapper"> 
 <aside> 
  <nav> 
   <ul> 
    <li>nav_1</li>
    <li>nav_2</li>
    <li>nav_3</li>
    <li>nav_4</li>
    <li>nav_5</li>
   </ul> 
  </nav> 
 </aside> 
</div> 
```

然后，我们将主要内容放在一个标签
中

```
<section>
  <div class="container">
    <div class="row">
      <h2>Lorem ipsum dolor sit amet.</h2>
      <p>Lorem ipsum......</p>
    </div>
    <!--- add as many rows needed --->
  </div>
</section> 
```

容器 div 并不是真正必要的，它是我编写响应站点的习惯的结果，我们会做一点——只要不超出范围就行。

### CSS

我将 CSS 保持内联以保持简洁，但它可以很容易地成为外部脚本的一部分。

```
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
html {
  font-size: 63.5%;
  font-family: "Roboto Slab", serif;
  height: 100%;
}
body {
  font-size: 1.6rem;
  line-height: 2.8rem;
  height: 100%;
}
.wrapper {
  height: 100%;
}
.container {
  margin: 0 15px;
}
.row {
  padding: -15px;
  margin: 15px 0;
}
.row:before,
.row:after {
  content: "";
  display: table;
}
nav li {
  padding: 8px;
} 
```

现在我们已经有了初始的样式，让我们来关注两个主要的元素。
因此，对于侧边栏，我们将设置一个初始宽度为 40% 的**，将其向左浮动，并将其放置在屏幕之外，左边距为-40%** 的**，我们还将添加一个绝对位置，以避免其堆叠在主内容上。** 

```
aside {
  background-color: #333;
  color: #fff;
  width: 40%;
  margin-left: -40%;
  float: left;
  height: 100%;
  position: absolute;
} 
```

现在，对于包含主要内容的 section 元素，我们将把它向右浮动，宽度和高度的**为 100%，右边距为 0** 。

```
section {
  width: 100%;
  height: 100%;
  float: right;
  margin-right: 0;
} 
```

### 切换开关

现在让我们在包装器 div 之前的标记顶部添加一个复选框。

```
<input type="checkbox" id="offcanvas" class="toggle" />
<div class="wrapper"><!--- wrapper content here ---></div> 
```

我们给出了它和 **offcanvas** 的 id，稍后标签将使用它们来触发切换。

现在我们基本上想对输入的选中状态做出反应，当**选中**时将导航显示出来，当**取消选中**时再次隐藏。

我们将在 CSS 中使用相邻的选择器，首先将 side 元素作为目标

```
.toggle:checked + .wrapper > aside {
  margin-left: 0;
} 
```

和
的截面元素相同

```
.toggle:checked + .wrapper > section {
  margin-right: -40%;
} 
```

### 把它变漂亮

隐藏和显示导航的主要目标已经解决了。我们将通过添加臭名昭著的“汉堡”按钮使它变得更好，并使过渡平滑。

让我们添加一个包含按钮并触发切换的标签。在 section 元素的顶部让添加这个代码:

```
<div class="container">
  <div class="row">
    <label for="offcanvas" class="toggler">
      <span class="navicon"></span>
    </label>
  </div>
</div> 
```

属性的标签**的值为 **offcanvas** ，与我们输入按钮的 id 相同。**

这允许我们从标签代码存在的页面上的任何地方触发切换。现在让我们添加一些样式

```
.toggler {
  display: inline-block;
  cursor: pointer;
}
.navicon {
  width: 28px;
  height: 28px;
  background: url("navicon.png") no-repeat;
  background-size: cover;
  display: block;
} 
```

我们现在可以隐藏输入按钮

```
.toggle { display: none; } 
```

### 大受欢迎

当导航在视图中时，再次隐藏它的唯一方法是精确地点击菜单按钮。

这在桌面屏幕上可能没问题，但在较小的分辨率上可能会有挑战。

我们将使整个内容在该状态下可点击，以缓解这一问题。

所以回到 html 的第一个标签之前我们会再加一个

```
<section>
  <label for="offcanvas" class="biglabel"></label>
  <!--- section content --->
</section> 
```

我们给它一个**大标签**的类。在我们的 CSS 中，当导航被隐藏时，我们将隐藏它，并让它作为一个大按钮覆盖整个内容。

```
.toggle,
.biglabel {
  display: none;
}
.biglabel {
  width: 100%;
  height: 100%;
  position: absolute;
  cursor: pointer;
}
.toggle:checked + .wrapper > section .biglabel {
  display: block;
} 
```

当我们刷新浏览器时，我们可以看到，当导航在视图中时，整个内容变成可点击的。完美！！差不多吧。

### 像太空人一样平稳

最后一步是处理过渡，使导航很好地滑入。

使用 CSS3 过渡使它变得非常容易。我们将针对侧边栏和主要内容

```
aside,
section {
  -webkit-transition: margin 0.5s ease-in-out;
  -moz-transition: margin 0.5s ease-in-out;
  -ms-transition: margin 0.5s ease-in-out;
  -o-transition: margin 0.5s ease-in-out;
  transition: margin 0.5s ease-in-out;
} 
```

瞧，瞧！！我们的菜单现在可以很好地显示内容，离开时不会发出任何声音。

希望你发现它很有用，并且和我一样喜欢它。请在评论中分享并让我知道你的想法。

干杯