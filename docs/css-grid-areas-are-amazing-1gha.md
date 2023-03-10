# CSS 网格区域是惊人的

> 原文：<https://dev.to/kenbellows/css-grid-areas-are-amazing-1gha>

当我第一次了解 CSS 网格布局以及它给我们 CSS 开发人员带来的新功能时，我立刻兴奋起来。我对摆脱 Bootstrap 或基础网格所需的所有非语义容器层的前景感到兴奋，用纯语义的 HTML 文档代替它们，并将布局完全留给 CSS，就像它一直应该做的那样。

但是当我和其他对语义标记不感兴趣的开发人员交谈时，我经常得到的回答是，“我已经知道如何使用做一个网格系统，我现在真的不想学习一个全新的东西...我真的会在这里得到一些实际的东西吗？或者我会回到我开始的地方，但是是在一辆不同颜色的车里？我能用 CSS Grid 做些什么，而我已经不能用一个基本的网格系统做了？

今天我想用两个词来回答这个问题:*网格区域*。

# 轨迹和线条

在 CSS 网格布局中，一个网格由**轨迹**和**线**组成。“轨道”是行或列的通称。正如你可能已经猜到的，“线”是轨道之间的边界。我们用 [`grid-template-rows`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-template-rows) 和 [`grid-template-columns`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-template-columns) 来定义网格，然后用 [`grid-row`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-row) 和 [`grid-column`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-column) 将网格项目附加到网格中。

[![Grid tracks and lines](img/568b0d103dd7069ba7eb0afd46f71fa7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---IqlsNhG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dwev3ij08w5xcamxe04t.png)

在许多情况下，也许是大多数情况下，你只需要轨迹和线条来定义你的网格。特别是对于具有动态内容的较小区域，如搜索结果的网格或朋友列表，您通常可以只使用`grid-template-columns`来定义您的列，使用 [`grid-auto-rows`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-auto-rows) 来声明新行应该有多高，并让 CSS Grid 奇妙的自动放置行为从那里为您填充网格。

[https://codepen.io/kenbellows/embed/ebeMgB?height=600&default-tab=result&embed-version=2](https://codepen.io/kenbellows/embed/ebeMgB?height=600&default-tab=result&embed-version=2)

但是有时候事情并不是那么动态的。有时你有一组预先定义好的、固定的条目要放在你的网格中，你确切地知道你想要它们去哪里，你只想给浏览器正确的答案，没有自动放置的魔力。这在使用网格布局网站的顶层结构时非常常见:页眉在这里，导航栏在这里，个人资料图片在那里，内容在这里，完成。

你仍然可以通过指定你的轨道来定义一切，固定块到行号，这是很常见的。例如，假设您正在编写一个简单的用户配置文件，其中包含四个部分:显示名称、头像、“关于”部分和用户发布的帖子列表。您可能会这样做:

```
<main>
  <h1 class="display-name">...</h1>
  <section class="avatar"><img src="..."></section>
  <section class="about">...</section>
  <section class="posts">...</section>
</main> 
```

Enter fullscreen mode Exit fullscreen mode

```
main {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
}
.display-name {
  grid-row: 1 / 2;
  grid-column: 3 / 6;
}
.avatar {
  grid-row: 1 / 3;
  grid-column: 1 / 3;
}
.about {
  grid-row: 3 / 4;
  grid-column: 1 / 3;
}
.posts {
  grid-row: 2 / 5;
  grid-column: 3 / 6;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/kenbellows/embed/jXazZq?height=600&default-tab=result&embed-version=2](https://codepen.io/kenbellows/embed/jXazZq?height=600&default-tab=result&embed-version=2)

网格容器的 [`grid-template-columns`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-template-columns) 规则定义了我们的列轨迹，网格项目的 [`grid-row`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-row) 和 [`grid-column`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-column) 规则指定了哪些网格线应该是每个项目的边界。

这绝对有效。但是这里有一些挫折:

*   盯着一堆数字会让你的网格难以可视化，我经常会花很多时间摆弄那些行号来让事情看起来正确。这并不是 CSS Grid 特有的问题，虽然这比处理填充、边距、浮动值和`flex-grow`要容易得多，但仍然值得注意。
*   如果我想在现有轨道或项目之间添加新的轨道或项目，该怎么办？我可能需要更新上面的一些行号。您可以使用`span`关键字来代替列和行结束值的硬行号，这样您就指定了要覆盖多少个轨道，而不是在哪个网格行停止，但这是一种折衷；有时候会有帮助，有时候会碍事。
*   上面有很多重复。与前一点相关，如果你只是想在左边添加一列，比如说，一个侧边栏，你必须找到所有的`grid-row: 1 / ...`实例，并将每个 1 更新为 2。这在更复杂的布局中会变得更糟。
*   带有改变事物的媒体查询断点的响应性布局可能有点冗长和混乱，因为您需要为每个断点指定所有这些值，这在某种程度上增加了可视化每个不同布局的问题。

谢天谢地，CSS 网格规范的作者给了我们一个惊人的特性来帮助解决所有这些问题:网格区域！

# 地区

一个**网格区域**是一个网格的矩形部分，每边由一条网格线(因此，4 条网格线，2 条水平，2 条垂直)界定。

使用这个特性的最简单(也是最不有趣)的方法就是将它简化为网格项目中的行和列。上面例子中的每一部分都可以简化为一个单独的`grid-area`规则:

```
/*
 grid-area: row-start / col-start / row-end / col-end;
*/

.about {
  grid-area: 3 / 1 / 5 / 3;
} 
```

Enter fullscreen mode Exit fullscreen mode

但这并没有真正解决我列出的任何问题；事实上，你可能会认为它更糟糕，因为它更难阅读。(哪个数字又代表`grid-row-end`？)

所以与其这样，不如让我给你看一个 CSS 网格特性，当我看到它的时候，它绝对让我大吃一惊: [`grid-template-areas`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-template-areas) ！

```
main {
  display: grid;
  grid-template-areas: 
    "avatar avatar  name  name  name"
    "avatar avatar  posts posts posts"
    "about  about   posts posts posts"
    ".      .       posts posts posts"
  ;
}
.display-name {
  grid-area: name;
}
.avatar {
  grid-area: avatar;
}
.about {
  grid-area: about;
}
.posts {
  grid-area: posts;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/kenbellows/embed/REjyxW?height=600&default-tab=result&embed-version=2](https://codepen.io/kenbellows/embed/REjyxW?height=600&default-tab=result&embed-version=2)

看那个。

对吗？

*对吗？？？*

我们没有定义行和列，而是使用[瑞秋安德鲁](https://rachelandrew.co.uk/)称之为[“ASCII 艺术”](https://gridbyexample.com/video/grid-template-areas/)的描述网格的方法，用命名的网格区域可视化地布置了整个网格*。每一行都被写成一个用双引号("..."括起来的字符串)，每个单元格都被赋予它所属的网格区域的名称。如果一个单元格不需要属于任何区域，只需要在那个点上放一个点(或者几个点，风格偏好)。单元格用空格分隔，多余的空格折叠起来，这使得网格的可视化格式非常容易。*

每个网格区域由所有以其命名的单元格定义。正如你可能猜到的，你的区域一定是**矩形**和**连续**；这里没有俄罗斯方块或虫洞。

一旦你定义了你的网格区域，你就可以通过设置一个项目的`grid-area`规则到你想要它去的区域的名称来附加你的网格项目到它们上面！

注意，这里没有`grid-template-columns`或`grid-template-rows`；默认情况下，曲目只是从 ASCII 艺术推断出来的！(*多酷啊？？*)也就是说，如果你愿意，你仍然可以使用那些规则；这通常仍然是确定规模所必需的。您也可以使用简写的 [`grid-template`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-template) 规则来一次定义所有三个规则。在这个简写中，您可以选择在每一行的描述后给它一个高度，然后如果您想要列的大小，您可以在最后一行后添加一个斜杠，并在末尾指定列的大小:

```
grid-template:
  "avatar avatar  name  name  name"   2rem
  "avatar avatar  posts posts posts"
  "about  about   posts posts posts"  6rem
  "...... ......  posts posts posts"
  / 2fr    10px    3fr   1fr   5vw
; 
```

Enter fullscreen mode Exit fullscreen mode

依我看，对于包含静态条目的网格布局来说，这比一长串的显式网格线更具可读性、可视化和最终可维护性。

# 响应性和语义

如果有人问我，我认为 CSS Grid 最令人兴奋的是什么，我会回答:CSS Grid 给 web 带来的最大好处是,**无需牺牲语义就能轻松实现响应。**

在上面的用户配置文件示例中，细心的读者可能已经注意到了一些奇怪的事情:在 HTML 中，各部分的顺序是:

*   显示名称
*   阿凡达
*   关于；在…各处 ；大约
*   邮件

但是在网格布局中，头像排在第一位，接下来是右边的显示名称、下面的“关于”部分和右下角的帖子。此外，帖子部分与头像和“关于”部分垂直重叠。

关键点是:HTML 标签的编写顺序在语义上是有意义的，但是网格布局在视觉上是有意义的。HTML 是按照我希望用户阅读信息的顺序编写的。这对于使用辅助技术的用户来说尤其重要，比如屏幕阅读器:CSS 在页面上移动东西的事实对元素的顺序没有影响，所以语义得到了保留。

这涵盖了语义方面，但是什么是响应性呢？好吧，考虑一下，如果用户将我们的档案布局缩小到移动大小，特别是在较小的屏幕上垂直放置，会发生什么。(也许你已经在移动设备上阅读这篇文章了，所以你不必去想象！)就不会好看了。因此，让我们在比如说`800px`处添加一个媒体查询断点，以重新安排我们的布局来反映上面列出的部分顺序。你准备好了吗？代码如下:

```
@media(max-width: 800px) {
  main {
    grid-template-areas:
        "name"
        "avatar"
        "about"
        "posts"
    ;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*轰。*就这样。这就够了。

和第一个布局一样直观。如果你想要第三种布局，可能是超宽屏幕的布局，有点疯狂，把头像移到左边第三列，也很简单:

```
@media(min-width: 1500px) {
  main {
    grid-template-areas:
        "avatar name  posts"
        "avatar about posts"
        ".      .     posts"
    ;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个演示，包括上面所有的三种布局，但是使用了 [`grid`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid) 简写规则，它重置像轨道大小这样的值。这避免了默认布局中的轨道大小被其他布局继承，从而导致问题，而不需要我在每个断点中显式地覆盖它们。

[https://codepen.io/kenbellows/embed/zyPaar?height=600&default-tab=result&embed-version=2](https://codepen.io/kenbellows/embed/zyPaar?height=600&default-tab=result&embed-version=2)

在所有这三种布局中，HTML 源代码的顺序都不会改变，所以使用屏幕阅读器(或者网络爬虫或屏幕抓取器)阅读的用户仍然可以按照首选的语义顺序获得页面上的信息。

# 结论

我希望你在这一点上支持我。我仍然听到来自那些还没有认真研究 CSS 网格的 web 开发人员的反对，他们说他们不想学习另一个新东西，它基本上与他们已经知道和使用的基于 float 或 Flexbox 的网格系统相同，或者，最令人震惊的是，它只是回到了基于表格的布局。

我希望我已经在这篇文章中展示了 CSS Grid 是多么强大，无论是就它可以产生的布局而言，还是就它可以多么容易地创建和维护灵活的、响应迅速的、漂亮的布局而言，都不会牺牲结构良好的、纤细的、语义纯粹的 HTML。

我想再次给瑞秋·安德鲁热烈的掌声。如果你不知道她的网站[gridbyexample.com](https://gridbyexample.com)，去看看吧。十年来，她一直亲自倡导 CSS 网格，她和[珍·西蒙斯](http://jensimmons.com)一起，通过她的视频和教程基本上教会了我网格。我会给瑞秋最后一句话:

> ![Rachel Andrew profile image](img/a8f6b9fd9267b82c01ca1acf1c8bd45a.png)瑞秋安德鲁@ rachelandruw![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)我要向下一个告诉我 CSS 网格布局与基于表格的设计相同的家伙挑战某种布局决斗。2018 年 2 月 11 日下午 16:23[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=962723731104727040)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=962723731104727040)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=962723731104727040)