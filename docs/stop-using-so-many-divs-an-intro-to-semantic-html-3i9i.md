# 停止使用这么多 div！语义 HTML 介绍

> 原文：<https://dev.to/kenbellows/stop-using-so-many-divs-an-intro-to-semantic-html-3i9i>

# div 被打出

我们都爱我们的标签。它们已经存在了几十年，几十年来，当你需要为了造型或结构的目的将一些东西包装在一个块中时，它们一直是首选元素。浏览生产网站，看到这样的东西还是很常见的:

```
<div class="container" id="header">
    <div class="header header-main">Super duper best blog ever</div>
    <div class="site-navigation">
        <a href="/">Home</a>
        <a href="/about">About</a>
        <a href="/archive">Archive</a>
    </div>
</div>
<div class="container" id="main">
    <div class="article-header-level-1">
        Why you should buy more cheeses than you currently do
    </div>
    <div class="article-content">
        <div class="article-section">
            <div class="article-header-level-2">
                Part 1: Variety is spicy
            </div>
            <!-- cheesy content -->
        </div>
        <div class="article-section">
            <div class="article-header-level-2">
                Part 2: Cows are great
            </div>
            <!-- more cheesy content -->
        </div>
    </div>
</div>
<div class="container" id="footer">
    Contact us!
    <div class="contact-info">
        <p class="email">
            <a href="mailto:us@example.com">us@example.com</a>
        </p>
        <div class="street-address">
            <p>123 Main St., Suite 404</p>
            <p>Yourtown, AK, 12345</p>
            <p>United States of America</p>
        </div>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

呼，那是一大堆`<div>`而且，嘿，这很有效。我是说，大部分时候。它有你需要的结构，我相信当你设计好它的时候，它会看起来像你想要的样子。但是它有一些大问题:

*   **可访问性** -许多 a11y 工具相当智能，它们尽力解析页面的结构，帮助用户按照页面作者的意图浏览页面，并给用户提供简单的跳转点，让他们快速导航到他们感兴趣的页面部分。但是`<div>` s 并没有真正传递任何关于文档结构的有用信息。世界上最聪明的 a11y 工具仍然不是人类，不能指望它解析`class`和`id`属性，并识别世界各地的开发人员命名他们的块的所有怪异和疯狂的方式。我能认出`class="article-header-level-2"`是副标题，但机器人不能。(如果可以，把它从我的电脑里拿出来，我还没准备好迎接 AGI 革命。)

*   **可读性**——要阅读这段代码，你需要仔细扫描类名，从`<div class="..."></div>`样板文件中挑选出来。一旦你深入到标记的几个层次，跟踪哪个`</div>`结束标签和哪个`<div...>`开始标签就变得棘手了。你开始非常依赖 IDE 的特性，比如[给不同的缩进级别着色](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow)或者[突出显示匹配的标签](https://marketplace.visualstudio.com/items?itemName=vincaslt.highlight-matching-tag)来跟踪你在哪里，在较大的文档中，可能需要在这些特性之上进行大量的滚动。

*   一致性和标准——开始一份新工作或转移到一个新项目，不得不从头开始学习代码库中所有疯狂的标记惯例，这可能会令人沮丧。如果每个人都有一种标准化的方法来标记 web 文档中的公共结构，那么在不熟悉的代码库中浏览 HTML 文件并快速了解它应该表示什么将会容易得多。*要是有这样的标准就好了...*

# HTML5:这样的标准

[HTML5](https://en.wikipedia.org/wiki/HTML5) 并不新鲜。这是一种保守的说法；一份[初步工作草案](https://www.w3.org/TR/2008/WD-html5-20080122/)于 2008 年 1 月 *(11 年前！)*，并在 4 年前的 2014 年 10 月成为了[羽翼丰满的 W3C 推荐](https://www.w3.org/TR/2014/REC-html5-20141028/)。所以，好像，已经有一段时间了。

HTML5 的主要进步之一是引入了一组标准化的语义元素**。术语“语义”指的是一个词或一个事物的*意思*，所以“语义元素”是设计用来以一种更有**意义**的方式标记文档结构的元素，这种方式可以清楚地表明它们是什么*代表*，它们在文档中有什么用途。重要的是，由于它们是标准化的，这些元素以每个人都可以使用和理解的方式定义了文档，包括机器人。**

 **我认为 HTML5 规范本身在`<div>`元素的定义下很好地总结了这个问题:

> 注意:
> 强烈建议作者将 div 元素视为万不得已的元素，因为没有其他元素是合适的。使用更合适的元素而不是 div 元素可以让读者更容易访问，让作者更容易维护。
> —[https://www . w3 . org/TR/html 5/grouping-content . html # the-div-element](https://www.w3.org/TR/html5/grouping-content.html#the-div-element)

我将语义块元素分为两类:**初级结构**和**内容指标**。这些不是标准条款或任何东西；我只是为这篇文章编的。但我认为这种区别已经足够有用了。🤷‍♂️

# 一级结构

有一个超级常见的模式，可以在整个互联网的网站、教程、甚至 CSS 库中找到，而且理由很充分。我们经常将一个页面的最顶层分成三个区域:**页眉**、**主**和**页脚**，然后根据需要将这些区域分成**节**。我在上面的例子中包含了这一点来证明这一点:

```
<div class="container" id="header">...</div>
<div class="container" id="main">
    ...
    <div class="article-section">...</div>
    ...
</div>
<div class="container" id="footer">...</div> 
```

Enter fullscreen mode Exit fullscreen mode

我已经看到(并使用)这种模式几十年了，以这种方式构造文档非常有意义，既提高了 HTML 的可读性，又简化了 CSS 中页面的样式。header 和 footer 元素还使得 PHP 或 Rails/ERB 等语言中的部分模板更容易使用，因为您可以在整个站点中包含通用的页眉和页脚部分:

```
<?php include 'header.php'; ?>

<div id="main">...</div>

<?php include 'footer.php'; ?> 
```

Enter fullscreen mode Exit fullscreen mode

所以事情是这样的:每个人都同意这是一个很好的模式。这包括 WHATWG 和 W3C 的人员，他们将该模式标准化为 HTML5 中的四个新元素，并有非常清晰的名称:`<header>`、`<main>`、`<footer>`和`<section>`。

### 书立:`<header>`和`<footer>`

`<header>`和`<footer>`元素基本上是一对双胞胎:它们在规范中的定义非常相似，并且遵循相同的规则，它们被允许在哪里使用，唯一的区别是它们的语义目的:头在事物的开始，尾在事物的结尾。我所说的“东西”不仅仅是指页面的`<body>`:这一对元素被设计用于文档的任何部分，它代表一大块有明确开始和结束的内容。这可以包括表格、文章、文章的部分、社交媒体网站上的帖子、卡片等。

页眉和页脚在语义上附加到最近的[“分段根”](https://www.w3.org/TR/html5/sections.html#sectioning-roots)或[“分段内容”](https://www.w3.org/TR/html5/dom.html#sectioning-content-2)元素。这些是像`<body>`、`<blockquote>`、`<section>`、`<td>`、`<aside>`和许多其他的东西；如果您想要完整的列表，请单击上面的链接。辅助技术可以使用这些元素和其他元素来生成文档的轮廓，这可以帮助用户更容易地浏览文档。每个分段根/内容不应有一个以上的`<header>`或`<footer>`。(每样一个都可以，但不要两个一样的。)

最后要注意的是，`<header>` s 经常为它们的上下文持有标题元素(`<h1>` - `<h6>`)。这不是必须的，但是可以帮助将其他与标题相关的元素分组，比如链接、图片或副标题，并且可以帮助保持一致的结构，即使标题是`<header>`中的唯一元素。

### 好东西:`<main>`

第三个主要区域元素`<main>`是特殊的。该规格说明了关于`<main>`的两件非常重要的事情:

> 文档的主要内容区域包括该文档特有的内容，但不包括在一组文档中重复出现的内容，如站点导航链接、版权信息、站点徽标和横幅以及搜索表单(除非文档或应用程序的主要功能是搜索表单)。
> —[https://www . w3 . org/TR/html 5/grouping-content . html # element def-main](https://www.w3.org/TR/html5/grouping-content.html#elementdef-main)

所以`<main>`是你放好东西的地方，是一个页面的重要部分，是用户来*这个页面特别是*的原因，而不是一般的你的网站。换句话说，*主要是*的内容。😯😲🤯

所有其他的东西，标识、搜索表单和导航等等，都可以放在`<body>`内的`<header>`或`<footer>`中，但在`<main>`之外。

> 一个文档中不能有一个以上的可见主元素。如果一个文档中存在不止一个主元素，那么必须使用隐藏属性隐藏所有其他的实例。
> —[https://www . w3 . org/TR/html 5/grouping-content . html # element def-main](https://www.w3.org/TR/html5/grouping-content.html#elementdef-main)

这是非常独特的。与`<header>`和`<footer>`(以及大多数其他块元素)不同，`<main>`不能在任意分节内容的整个页面中使用；它应该只使用一次。或者说，它可以在一个文档中多次使用，但一次只能看到一个`<main>`元素；所有其他的必须用`hidden`属性隐藏，这基本上就像 CSS 中的`display: none;`一样。仔细想想，这是一个非常有用的预加载应用视图的模式:创建一个新的`<main hidden>`，获取一些用户接下来可能会查看的内容(例如，一个系列中的下一篇文章，幻灯片中的下一张幻灯片，等等)。)，当用户点击链接/按钮加载该视图时，通过切换两者上的`hidden`属性，用预加载的视图替换当前的`<main>`。

在继续之前，让我们暂停一下，回顾一下上面的例子。如果我们使用`<header>`、`<main>`和`<footer>`作为文章的主要结构，它看起来会是这样的:

```
<header>
    <h1>Super duper best blog ever</h1>
    ...
</header>
<main>
    <h2>Why you should buy more cheeses than you currently do</h2>
    ...
</main>
<footer>
    Contact us!
    <div class="contact-info">this.is.us@example.com</div>
</footer> 
```

Enter fullscreen mode Exit fullscreen mode

已经好多了！但是还有很多工作要做。

### 分解一下:`<section>`

这样我们就有了页面的基本轮廓:页眉、页脚和主要内容区域。现在是时候添加一些甜蜜的内容了。

通常你会想把你的内容分成**个部分**，尤其是像这篇文章这样的大量文本内容，因为没有人喜欢阅读密不透风的文本。

<figure>

[![Wall of text](img/4914fc59d652bbffb09c1141c989b718.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ddAwodiE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/qkpL6AB.jpg)

<figcaption>Nobody likes a wall of text</figcaption>

</figure>

这就是`<section>`的用武之地。这个从规则上来说是最简单的:从结构上来说，它基本上只是一个具有特殊语义的`<div>`。一个`<section>`开始一个新的“切片内容”区域，因此它可以有自己的`<header>`和/或`<footer>`。

那么，一辆`<section>`和一辆普通的老款`<div>`有什么区别，你应该在什么时候使用它们？好吧，请允许我再次引用规范:

> 注意:
> `<section>`元素不是一个通用的容器元素。当一个元素只是为了样式的目的或者为了方便脚本编写而需要时，我们鼓励作者使用 [`<div>`](https://www.w3.org/TR/html5/grouping-content.html#elementdef-div) 元素来代替。一般规则是，`<section>`元素只有在元素的内容明确列在文档的[大纲](https://www.w3.org/TR/html5/sections.html#outline)中时才是合适的。
> —[https://www . w3 . org/TR/html 5/sections . html # the-section-element](https://www.w3.org/TR/html5/sections.html#the-section-element)

你知道，顺便提一下，HTML5 规范实际上非常易读。这是目前可读性更强的规格之一。每次我为了快速回答而浏览它的时候，我不可避免地会学到一些意想不到的有用的东西，尤其是当我开始点击链接的时候。有时间试试吧！

简而言之，如果您想在目录中列出文档的这一部分，请使用`<section>`。如果没有，用一个`<div>`或者别的什么。

# 内容指标

好了，我们的页面有了一个坚实的结构。我们已经明确标记了页面的主要内容区域，并且标注了页眉、页脚和节，而不是到处都是。但是对于我们的文档来说，肯定有更多的语义。

让我们来谈谈 HTML5 中添加的一些元素，它们传达的是内容语义而不是 T2 结构。

### 全舍邦:`<article>`

`<article>`元素用来表示一个完全独立的内容区域，可以从你的页面中取出放到另一个页面中，但它本身仍然有意义。这可能是一篇文字文章或博客帖子，但也可以用于社交媒体帖子，如推文或脸书墙帖子。

[html 5 规范推荐](https://www.w3.org/TR/html5/sections.html#elementdef-article)文章总是有一个标题来标识它是什么，最好使用一个标题元素(`<h1>` - `<h6>`)。一个`<article>`也可以有`<header>`、`<footer>`和`<section>`元素，所以你真的可以用它来嵌入一个完整的文档片段，并在另一个页面中包含它需要的所有结构。

回到上面的例子，让我们用一个`<article>`和一些我们讨论过的其他元素重写`class="article-*"`元素。

```
<article>
    <header>
        <h1>Why you should buy more cheeses than you currently do</h1>
    </header>
    <section>
        <header>
            <h2>Part 1: Variety is spicy</h2>
        </header>
        <!-- cheesy content -->
    </section>
    <section>
        <header>
            <h2>Part 2: Cows are great</h2>
        </header>
        <!-- more cheesy content -->
    </section>
</article> 
```

Enter fullscreen mode Exit fullscreen mode

这不是比原著可读性强多了吗？同样，它不仅更容易阅读，对辅助技术也更有用；机器人并不总是能弄清楚你的具体类名模式，但它们可以遵循这种结构。

### 四处走动:`<nav>`

这个元素比其他元素更广为人知。`<nav>`旨在清楚地识别页面上的主要导航块，即帮助用户找到网站其余部分(如网站地图或标题中的链接列表)或当前页面(如目录)的链接组。

在我们上面的例子中，让我们对标题中的那组链接应用一个`<nav>`。

```
<nav>
    <a href="/">Home</a>
    <a href="/about">About</a>
    <a href="/archive">Archive</a>
</nav> 
```

Enter fullscreen mode Exit fullscreen mode

根本没有改变结构，但是你一眼就知道它是什么，而不需要阅读和处理`<div>`上的类名来找出它，更重要的是机器人也能找到它。

### 取得联系:`<address>`

我们要讨论的最后一个元素是`<address>`。该元素旨在显示联系信息，通常在主页`<footer>`中用于标记邮寄地址、电话号码、客户服务电子邮件地址等。为了生意。

有趣的是，如何在`<address>`元素中标记内容的规则是开放的。[该规范提到](https://www.w3.org/TR/html5/grouping-content.html#the-address-element)有几个其他规范解决了这个问题，提供这种粒度级别可能超出了 HTML 本身的范围。

一个常见的解决方案是 [RDFa](https://en.wikipedia.org/wiki/RDFa) ，也是 [W3C 规范](https://www.w3.org/TR/html-rdfa/)，它使用标签上的属性来标记数据的不同组成部分。下面是我们例子中的页脚用`<address>`元素和 RDFa:
标记后的样子

```
<footer>
    <section class="contact" vocab="http://schema.org/" typeof="LocalBusiness">
        <h2>Contact us!</h2>
        <address property="email">
            <a href="mailto:us@example.com">us@example.com</a>
        </address>
        <address property="address" typeof="PostalAddress">
            <p property="streetAddress">123 Main St., Suite 404</p>
            <p>
                <span property="addressLocality">Yourtown</span>,
                <span property="addressRegion">AK</span>,
                <span property="postalCode">12345</span>   
            </p>
            <p property="addressCountry">United States of America</p>
        </address>
    </section>
</footer> 
```

Enter fullscreen mode Exit fullscreen mode

不可否认，RDFa 有点冗长，但是它对于标记数据来说非常方便。如果您有兴趣了解更多关于 RDFa 的知识，这里有几个链接:

*   [W3C 的 RDFa 初级读本](https://www.w3.org/TR/rdfa-primer/)
*   [schema.org 上的模式描述和链接](https://schema.org/docs/schemas.html)
*   [上面使用的`LocalBusiness`模式](https://schema.org/LocalBusiness)

# 结论

好了，我们已经讨论了很多，并且我们已经看到了很多应用于我们的例子中的点点滴滴。但是让我们把它们放在一起，看看它是什么样子的。

```
<header>
    <h1>Super duper best blog ever</h1>
    <nav>
        <a href="/">Home</a>
        <a href="/about">About</a>
        <a href="/archive">Archive</a>
    </nav>
</header>
<main>
    <article>
    <header>
        <h1>Why you should buy more cheeses than you currently do</h1>
    </header>
    <section>
        <header>
            <h2>Part 1: Variety is spicy</h2>
        </header>
        <!-- cheesy content -->
    </section>
    <section>
        <header>
            <h2>Part 2: Cows are great</h2>
        </header>
        <!-- more cheesy content -->
    </section>
</article>
</main>
<footer>
    <section class="contact" vocab="http://schema.org/" typeof="LocalBusiness">
        <h2>Contact us!</h2>
        <address property="email">
            <a href="mailto:us@example.com">us@example.com</a>
        </address>
        <address property="address" typeof="PostalAddress">
            <p property="streetAddress">123 Main St., Suite 404</p>
            <p>
                <span property="addressLocality">Yourtown</span>,
                <span property="addressRegion">AK</span>,
                <span property="postalCode">12345</span>   
            </p>
            <p property="addressCountry">United States of America</p>
        </address>
    </section>
</footer> 
```

Enter fullscreen mode Exit fullscreen mode

如果你问我，我会说这比原来的例子可读性强 100 倍，而且在搜索引擎优化和可访问性方面也更有效 100 倍。

这些绝不是 HTML 中仅有的语义元素。还有很多其他的元素可以帮助你标记和组织你的文本内容，嵌入媒体等等。如果你喜欢这本书，并想深入了解，可以看看下面的几本书。你可能认识一些:

*   [T2`<aside>`](https://www.w3.org/TR/html5/sections.html#elementdef-aside)
*   [T2`<blockquote>`](https://www.w3.org/TR/html5/grouping-content.html#the-blockquote-element)
*   [T2`<cite>`](https://www.w3.org/TR/html5/textlevel-semantics.html#elementdef-cite)
*   [T2`<code>`](https://www.w3.org/TR/html5/textlevel-semantics.html#elementdef-code)
*   [T2`<data>`](https://www.w3.org/TR/html5/textlevel-semantics.html#elementdef-data)
*   [T2`<del>`](https://www.w3.org/TR/html5/edits.html#elementdef-del)
*   [T2`<figure>`](https://www.w3.org/TR/html5/grouping-content.html#elementdef-figure)
*   [T2`<ins>`](https://www.w3.org/TR/html5/edits.html#elementdef-ins)
*   [T2`<time>`](https://www.w3.org/TR/html5/textlevel-semantics.html#the-time-element)
*   [T2`<var>`](https://www.w3.org/TR/html5/textlevel-semantics.html#elementdef-var)

这只是一个开始！就像我说的，当你开始阅读 HTML 规范时，很难停下来。这是一种非常丰富的语言，我认为人们经常低估它。**