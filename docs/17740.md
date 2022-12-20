# 易于理解和表达的 HTML 语义

> 原文：<https://dev.to/ananyaneogi/accessible-and-expressive-html-semantics-43h1>

编写语义和可访问的代码可能看起来很耗时，尤其是如果你开始制作可访问的网站。然而从长远来看，好处是令人难以置信的。编写适当的语义 HTML 有助于各种类型的用户正确地消费内容，并通过您的网站导航，无论他们通过什么媒介进行交互。

在我们开始讨论语义之前，访问屏幕阅读器的最佳方式是使用 [Chrome Vox](https://chrome.google.com/webstore/detail/chromevox/kgejglhpjiefppelpmljglcjbhoiplfn) 。对于屏幕阅读器来说，这是测试网页可访问性的好方法。

我们也可以使用[语音合成 API](https://developer.mozilla.org/en-US/docs/Web/API/Window/speechSynthesis) 。
使用这个 API 进行简单的文本到语音的输出很简单。

```
var to_speak = document.querySelector('main').textContent;

function speak() {
  window.speechSynthesis.speak(new SpeechSynthesisUtterance(to_speak));
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们开始吧！

## 文本层次语义

### 1。措辞强调

在 HTML 中，我们可以使用一些不同的元素来达到这个目的-
`strong`、`bold`、`em`、`i`。但是什么时候用哪个呢？

当文本非常重要时，应该使用`strong`元素。相反，`b`元素应该用于样式目的，并不意味着概述的文本比它周围的文本更重要。

```
<!-- of strong importance -->
<strong>Pay attention: All classes are suspended for today</strong>

<!-- only adding visual differentiation -->
This recipe needs some <b>salt</b> and <b>pepper</b> 
```

Enter fullscreen mode Exit fullscreen mode

`em`元素强调文本的重要性，而`i`元素主要用于对话或会话中的不同语气。`i`元素也可以用来表达语言的变化。

```
<!-- emphasis on the prominence -->
I <em>really</em> want to go the movies

<!-- setting a different tone -->
I like her certain <i hreflang="fr">je ne sais quoi</i> 
```

Enter fullscreen mode Exit fullscreen mode

### 2。将 HTML 标签扩展为可访问

`abbr`标签用于我们陈述缩写时，如

```
<abbr title="British Broadcasting Corporation">BBC</abbr> has headquarters in London, United Kingdom. 
```

Enter fullscreen mode Exit fullscreen mode

对于可视化浏览器来说，当鼠标悬停在上面时会提供一个工具提示，但是屏幕阅读器会忽略标题属性。那么我们如何让它变得容易理解呢？

我们可以提供一个`aria-label`属性-

```
 <abbr title="British Broadcasting Corporation" aria-label="British Broadcasting Corporation">BBC</abbr> has headquarters in London, United Kingdom. 
```

Enter fullscreen mode Exit fullscreen mode

或者，我们可以提供仅对屏幕阅读器可见的附加信息-

```
<!-- screen reader specific styles -->

.sr-only {
    position: absolute;
     width: 1px; 
    height: 1px; 
    padding: 0; 
    margin: -1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0); 
    border: 0; 
}

<abbr title="British Broadcasting Corporation">
    <span class="sr-only">British Broadcasting Corporation</span>BBC
</abbr> has headquarters in London, United Kingdom. 
```

Enter fullscreen mode Exit fullscreen mode

这种方法可以扩展到其他有用的 HTML 元素，比如`mark`、`del`、`ins`。

`mark`元素用于突出显示文本的一部分，但屏幕阅读器在读出时没有任何区别。解决它的一个方法是使用上面的方法-

```
The current world population is <mark><span class="sr-only">highlight begins</span>7.7 billion<span>highlight ends<span/></mark> as of December 2018</mark> 
```

Enter fullscreen mode Exit fullscreen mode

这里给出了另一个让`mark`更易访问的有用方法:[让你的标记更易访问的简短说明](https://developer.paciellogroup.com/blog/2017/12/short-note-on-making-your-mark-more-accessible/)

`ins`和`del`元素分别用于声明从文档中插入和删除文本。

```
His son is <del>6</del> <ins>7</ins> years old 
```

Enter fullscreen mode Exit fullscreen mode

屏幕阅读器阅读这些标签没有任何不同，所以为了使它更容易访问，我们需要添加更多的上下文。

```
His son is <del><span class="sr-only">deleted</span>6</del> <ins><span class="sr-only">inserted</span>7</ins> years old 
```

Enter fullscreen mode Exit fullscreen mode

*注意:*与`del`和`ins`一起使用的附加属性是`cite`和`datetime`，其中`cite`提供了一个机器可读的源，为添加/删除提供参考，`datetime`提供了添加/删除发生的时间。

### 3。对屏幕阅读器隐藏内容以减少认知负荷

我们有时会添加一些东西来增加视觉上的吸引力，但是当视觉感官没有发挥作用时，这些东西可能就无关紧要了。对于前面这个有`ins`和`del`的例子，我们最好对屏幕阅读器隐藏被删除的文本，因为它可能在视觉上相关，但在其他方面无关。

```
His son is <del aria-hidden="true">6</del> <ins>7</ins> years old 
```

Enter fullscreen mode Exit fullscreen mode

主要的收获是，你真的需要留意哪些信息可能与你的用户相关。我们还需要给屏幕阅读器用户多少额外的信息，我们应该去掉多少。

* * *

## 超链接语义

网络上充斥着*这里*、*点击这里*的文本链接，这并没有给用户多少上下文。最好避免在超链接中使用这样的文本，而是给用户提供完整的上下文。

```
I am currently reading <a href="https://addyosmani.com/resources/essentialjsdesignpatterns/book/"><cite>Essential JS Design Patterns</cite></a>. You will find a lot of useful tips in it. 
```

Enter fullscreen mode Exit fullscreen mode

### 超链接属性

属性可用于提供所链接内容类型的上下文。

```
<a href="video.mp4" type="video/mp4">Download Video</a> 
```

Enter fullscreen mode Exit fullscreen mode

`download`属性为浏览器提供了更多的上下文，`href`中提到的文件是要下载的，而不是显示的。

```
<a href="video.mp4" type="video/mp4" download>Download Video</a> 
```

Enter fullscreen mode Exit fullscreen mode

如果链接中的文本语言不同于文档语言，我们可以提供`hreflang`属性来说明这一点。

```
<a href="..." hreflang="fr">La vie en rose</a> 
```

Enter fullscreen mode Exit fullscreen mode

链接属性列表可以在[微格式网站](http://microformats.org/wiki/existing-rel-values)上

超链接不仅可以用来链接到其他网页，我们还可以使用它们来链接到同一网页上的元素。例如，如果我们引用前面提到的一些图形或表格数据，我们可以使用`a`标签将它们链接起来，以向用户提供我们正在讨论的内容的上下文。我们可以通过将`id`放入您的*【可链接】*内容来实现这一点。

```
<figure id="figure-2">...</figure>

This phenomena was explained in <a href="figure-2">Figure 2</a> 
```

Enter fullscreen mode Exit fullscreen mode

网页内链接的另一个常见用例是“跳转到主要内容”链接。这些不仅对屏幕阅读器用户有用，对键盘用户也有用。

你可以在这里找到[如何创建跳转到主要内容的链接](https://accessibility.oit.ncsu.edu/it-accessibility-at-nc-state/developers/accessibility-handbook/mouse-and-keyboard-events/skip-to-main-content/)

* * *

## 创建清晰的导航

1.  通过键盘导航的常见方式是按`tab`键从一个链接到另一个链接。因此，如前所述，这使得在`a`标签中添加适当的文本变得更加重要。

2.  屏幕阅读器有一种使用标题来概述文档结构的方法，所以总是在标题中加入该部分内容的简明摘要。通过使用标题`h1` - `h6`创建一个合适的大纲，你可以帮助用户更好地理解你的页面结构和各个部分之间的关系。
    另一个小技巧是在你的标题上添加`id`，这样我们就可以用`a`标签将它们链接起来，即使对于视力正常的用户来说，这些标签也可以用作导航地标。

3.  如果我们想让用户在通过键盘导航时登陆到除链接之外的某些部分，我们可以将`tabindex = 0`放入该元素。通过这样做，我们将该特定部分添加到 tab 键顺序中。

4.  另一种导航方式是通过地标角色。这包含在 ARIA 规范中，用于根据上下文定义页面的区域。我们使用`role`属性来实现这一点。

```
<div role="navigation">
  <ul>
    <li>
       <a href="/contact">Contact Me</a>
      <li/>
  </ul>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

许多地标角色都有相同的地标标签-

*   横幅- `header`
*   导航- `nav`
*   主- `main`
*   内容信息- `footer`
*   补充- `aside`
*   搜索

* * *

最后但同样重要的是，总是尝试使用更有意义的元素而不是`div`来组织你的页面。

*   `header`
*   `footer`
*   `nav`
*   `main`
*   `section`
*   `article`
*   `aside`

它们不仅提供了更多的上下文，还为各种用户提供了丰富的体验。

这件事到此为止。

我不是专家，这肯定不是创建可访问的 HTML 需要学习的全部内容，但是我相信这会给你一个正确的方向。

* * *

### 参考文献

1.  [WCAG 清单](https://webaim.org/standards/wcag/checklist)
2.  [只用键盘上网](https://www.smashingmagazine.com/2018/07/web-with-just-a-keyboard)
3.  [使用屏幕阅读器浏览一天的网页](https://www.smashingmagazine.com/2018/12/voiceover-screen-reader-web-apps/)