# JavaScript 和可访问性:手风琴

> 原文：<https://dev.to/lkopacz/javascript-and-accessibility-accordions-5ecd>

*最初发布于[www.a11ywithlindsey.com](https://www.a11ywithlindsey.com/blog/javascript-accessibility-accordions)。*

当我第一次写关于 [JavaScript 和可访问性](https://www.a11ywithlindsey.com/blog/a11y-js-seemingly-unconventional-romance)的帖子时，我承诺我会把它做成一个系列。我决定用我的 [patreon](https://www.patreon.com/a11ywithlindsey) 来投票决定我的下一篇博文是什么。这个话题赢了，我终于有更多的时间来写 JavaScript 了！

所以这个话题我将深入探讨如何让手风琴变得无障碍！我们的重点是:

*   用键盘访问手风琴
*   屏幕阅读器支持

## HTML 结构

我做了一些关于 HTML 结构的研究。我读了 [a11y 项目](https://a11yproject.com/patterns#accordions-and-tabs)到[斯科特奥哈拉的手风琴代码](https://scottaohara.github.io/a11y_accordions/)的链接。我还读了[唐关于咏叹调的 take-controls](https://www.heydonworks.com/article/aria-controls-is-poop)-TL；何博士认为它们是便便。我无法逃避阅读 WAI-ARIA 手风琴的例子，因为他们设定了很多标准。我的希望是，有了关于理想的所有信息，我可以帮助讨论为什么这里的一切都很重要。很容易不知所措，我就是来帮忙的！

因此，如果你读了我的帖子 [3 个提高键盘可访问性的简单技巧](https://www.a11ywithlindsey.com/blog/3-simple-tips-improve-keyboard-accessibility)，你可能会想起我对语义 HTML 的热爱。

> 如果您需要 JavaScript 来实现可访问性，语义 HTML 会让您的工作变得更加容易。

我发现的许多例子都为折叠标题使用了语义按钮元素。然后示例使用 div 标签作为兄弟标签。下面是我的代码是如何开始的:

[https://codepen.io/littlekope0903/embed/RmRVvN?height=600&default-tab=result&embed-version=2](https://codepen.io/littlekope0903/embed/RmRVvN?height=600&default-tab=result&embed-version=2)

### 添加咏叹调属性

我在之前的一篇文章中写道 ARIA 不是语义 HTML 的替代品。新出现的 HTML 特性一直在取代 ARIA。在理想的情况下，我会使用[细节元素](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/details)。不幸的是，根据[浏览器兼容性章节](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/details#Browser_compatibility)，不支持 Edge 和 IE11。在浏览器支持得到改善之前，我会坚持用“老式”的方法。我会根据我们的需要加上艾瑞亚。我很期待看到兼容性扩展到 Edge！

首先，我将向 div 添加一些`aria-hidden`属性，以指示 accordion 内容的**状态**。如果折叠的元素是**关闭的**，我们希望对屏幕阅读器隐藏该内容。你能想象通读自己不感兴趣的内容会有多烦吗？

```
- <div id="accordion-section-1"> + <div id="accordion-section-1" aria-hidden="true"> ...
...
- <div id="accordion-section-2"> + <div id="accordion-section-2" aria-hidden="true"> ...
...
- <div id="accordion-section-3"> + <div id="accordion-section-3" aria-hidden="true"> 
```

我们要做的下一件事是确保按钮有一个`aria-expanded`属性。当我们在按钮上时，它会告诉我们某个东西是展开的还是折叠的。

```
- <button id="accordion-open-1"> + <button id="accordion-open-1" aria-expanded="false"> ...
...
- <button id="accordion-open-2"> + <button id="accordion-open-2" aria-expanded="false"> ...
...
- <button id="accordion-open-3"> + <button id="accordion-open-3" aria-expanded="false"> 
```

对我来说，咏叹调越少越好。我将把它留在这里，在以后的章节中使用 JavaScript 来切换 ARIA 属性的状态。

[https://codepen.io/littlekope0903/embed/RmRLqw?height=600&default-tab=result&embed-version=2](https://codepen.io/littlekope0903/embed/RmRLqw?height=600&default-tab=result&embed-version=2)

## 增加一些造型

我不会过多关注 CSS 细节。如果你需要 CSS 资源，阿里·斯皮特尔的帖子 [CSS:从零到英雄](https://dev.to/aspittel/css-from-zero-to-hero-3o16)和艾玛·韦德金德的 [CSS 特异性](https://dev.to/emmawedekind/css-specificity-1kca)帖子都很棒。

首先，我为 div 和按钮添加了类。

```
- <button id="accordion-open-1" aria-expanded="false"> + <button id="accordion-open-1" class="accordion__button" aria-expanded="false">
    Section 1
  </button>
- <div id="accordion-section-1" aria-hidden="true"> + <div id="accordion-section-1" class="accordion__section" aria-hidden="true"> 
```

然后我给按钮添加一些样式。我和 SCSS 一起写了这支笔。

[https://codepen.io/littlekope0903/embed/NVrway?height=600&default-tab=result&embed-version=2](https://codepen.io/littlekope0903/embed/NVrway?height=600&default-tab=result&embed-version=2)

(快速注意:对于 iframe 上的三角形，我使用了 CSS tricks 中的 [CSS 三角形文章](https://css-tricks.com/snippets/css/css-triangle/)。)

我想明确地指出**这段代码:** 

```
.accordion {
  // previous styling
  &__button.expanded {
    background: $purple;
    color: $lavendar;
  }
} 
```

我想指定按钮打开时的样子。我喜欢它如何吸引你的眼睛和注意力到开放部分。现在我看到了它们的大致外观，我将添加折叠它们的样式。此外，我增加了一些开放的风格。

```
 &__section {
    border-left: 1px solid $purple;
    border-right: 1px solid $purple;
    padding: 1rem;
    background: $lavendar;
+   max-height: 0vh;
+   overflow: hidden;
+   padding: 0;
  }

+ &__section.open {
+   max-height: 100vh;
+   overflow: auto;
+   padding: 1.25em;
+   visibility: visible;
+ } 
```

最后，让我们为按钮添加一些焦点和悬停样式:

```
 $purple: #6505cc;
+ $dark-purple: #310363;
  $lavendar: #eedbff; 
```

```
 &__button {
    position: relative;
    display: block;
    padding: 0.5rem 1rem;
    width: 100%;
    text-align: left;
    border: none;
    color: $purple;
    font-size: 1rem;
    background: $lavendar;

+   &:focus,
+   &:hover {
+     background: $dark-purple;
+     color: $lavendar;
+
+     &::after {
+       border-top-color: $lavendar;
+     }
+   } 
```

[https://codepen.io/littlekope0903/embed/mYEYWg?height=600&default-tab=result&embed-version=2](https://codepen.io/littlekope0903/embed/mYEYWg?height=600&default-tab=result&embed-version=2)

快速提示:您可以通过添加`.accordion__button[aria-expanded="true"]`或`.accordion__section[aria-hidden="false"]`来添加样式。然而，我个人倾向于将类用于样式而不是属性。不同的人用不同的笔画！

## JavaScript 切换

现在让我们开始以一种容易理解的方式切换手风琴的有趣部分。首先，我想抓取所有的`.section__button`元素。

```
const accordionButtons = document.querySelectorAll('.accordion__button') 
```

然后我想遍历 JavaScript 返回的 HTML 集合的每个元素。

```
accordionButtons.forEach(button => console.log(button))
// returns <button id="accordion-open-1" class="accordion__button" aria-expanded="false">
//    Section 1
//  </button>
//  <button id="accordion-open-2" class="accordion__button" aria-expanded="false">
//    Section 2
//  </button>
//  <button id="accordion-open-3" class="accordion__button" aria-expanded="false">
//    Section 3
//  </button> 
```

然后对于其中的每一项，我想切换类的开始和结束，以达到视觉样式的目的。如果您还记得我们之前添加的`.open`和`.expanded`类，这里是我们切换它们的地方。我将使用相互匹配的 id 中的数字来获取该按钮的相应部分。

```
accordionButtons.forEach(button => {
  // This gets the number for the class.
  // e.g. id="accordion-open-1" would be "1"
  const number = button
    .getAttribute('id')
    .split('-')
    .pop()

  // This gets the matching ID. e.g. the
  // section id="accordion-section-1" that is underneath the button
  const associatedSection = document.getElementById(
    `accordion-section-${number}`
  )
}) 
```

现在回调和相关部分中有了当前值`button`。现在我们可以开始切换课程了！

```
button.addEventListener('click', () => {
  button.classList.toggle('expanded')
  associatedSection.classList.toggle('open')
}) 
```

切换课程并不是我们想要做的。我们还想切换 aria 属性。在前一节中，aria 属性将**状态**传达给屏幕阅读器。更改类显示了视觉用户的情况，而不是屏幕阅读器的情况。接下来，我检查按钮是否包含这些元素之一中的类。如果是的话，我将把状态换成`aria-hidden`和`aria-expanded`。

```
button.addEventListener('click', () => {
  button.classList.toggle('expanded')
  associatedSection.classList.toggle('open')

+ if (button.classList.contains('expanded')) {
+   console.log('open?')
+ } }) 
```

条件在我们设置类之后触发，如果类已经展开，它就是打开的！所以这就是我们想要使用状态并传达它是开放的。

```
button.addEventListener('click', () => {
  button.classList.toggle('expanded')
  associatedSection.classList.toggle('open')

  if (button.classList.contains('expanded')) {
    button.setAttribute('aria-expanded', true)
    associatedSection.setAttribute('aria-hidden', false)
  } else {
    button.setAttribute('aria-expanded', false)
    associatedSection.setAttribute('aria-hidden', true)
  }
}) 
```

现在我们可以用空格键或回车键打开和关闭手风琴了！

[https://codepen.io/littlekope0903/embed/qGNGyQ?height=600&default-tab=result&embed-version=2](https://codepen.io/littlekope0903/embed/qGNGyQ?height=600&default-tab=result&embed-version=2)

当我在没有打开的情况下浏览手风琴的标题时，他们不会在部分中阅读它们。这是好事！当我打开它的时候，我能够阅读它。

[![Neil Patrick Harris sitting in the driver's seat of a car, making a large grin. Then he nods his head and puts his thumb up.](img/6efc15461e713d305106736756975b98.png)](https://i.giphy.com/media/vtVpHbnPi9TLa/giphy.gif)

## 进行性增强

现在，我知道我们有多依赖 JavaScript 加载，尤其是我们使用的所有框架。现在我们知道了功能，让我们稍微重构一下代码。目标是确保如果没有启用 JavaScript 或者用户有连接问题，任何人都可以访问 accordion。

我最后想说的是

*   默认情况下，保持所有可折叠部分打开(向 HTML 部分添加一个`.open`类)
*   JavaScript 加载后，删除“open”类。
*   用 JavaScript 添加所有 aria 属性，并将其从 HTML 中移除

我想分别从我的按钮和部分中删除`aria-expanded="false"`和`aria-hidden="true"`。我还想将`open`类添加到 html 中，这样它在默认情况下是可视打开的。

```
- <button id="accordion-open-1" class="accordion__button" aria-expanded="false"> + <button id="accordion-open-1" class="accordion__button">
    Section 1
  </button>
- <div id="accordion-section-1" class="accordion__section" aria-hidden="true"> + <div id="accordion-section-1" class="accordion__section open"> 
```

我想设置那些属性，并在`accordionButtons`的 forEach 循环中删除那个类。

```
accordionButtons.forEach(button => { + button.setAttribute('aria-expanded', false);
  const expanded = button.getAttribute('aria-expanded'); 
```

然后我想创建一个`accordionsSections`变量并做两件事:

*   设置`aria-hidden`属性
*   移除`.open`类。

```
const accordionSections = document.querySelectorAll('.accordion__section');

accordionSections.forEach(section =>  {
  section.setAttribute('aria-hidden', true)
  section.classList.remove('open')
}) 
```

我们完了！请记住，我们没有删除任何其他代码或事件侦听器。我们只是用 JavaScript 添加了所有这些属性。

[https://codepen.io/littlekope0903/embed/qGRQvg?height=600&default-tab=result&embed-version=2](https://codepen.io/littlekope0903/embed/qGRQvg?height=600&default-tab=result&embed-version=2)

## 结论

你觉得这个帖子怎么样？对你有帮助吗？你对`<details>`元素感到兴奋吗？请在[推特](https://twitter.com/LittleKope/)上告诉我你的想法！还有，我现在有一个[帕特里翁](https://www.patreon.com/a11ywithlindsey)！如果你喜欢我的作品，考虑成为赞助人。如果你认捐了 5 美元或更多，你将能够对未来的博客文章进行投票！干杯！祝你一周愉快！**