# 定制 Reveal.js 超越创建个性化主题

> 原文：<https://dev.to/huijing/customising-revealjs-beyond-creating-a-personalised-theme-2i2b>

当我开始准备我将在 2019 年发表的几场演讲时，我意识到我 2019 年的第一场演讲(在 CSSConf China)将以我用 [Reveal.js](https://github.com/hakimel/reveal.js/) 搭建的第 50 个<sup>T1】幻灯片结束。自从我第一次开始演讲，我就一直使用这个演讲框架，[写了当时的基础知识](https://www.chenhuijing.com/blog/revealjs-and-github-pages/)。</sup>

经过 50 次迭代后，我的幻灯片现在都被大量定制了，所以我想写下我对它们做的一些事情会很好。和他们一起。随便啦。

在那篇早期的文章中，我加入了一个非常简短的段落来创建你自己的主题。尽管 Reveal.js 在过去 3 年中已经更新了多次，但基本要点仍然没有改变。我仍然袖手旁观我最初的建议，删除捆绑主题，并定制关闭`simple.scss`。

从 3.7.0 版开始，`css`文件夹看起来像这样:

```
css/
|-- print/
| `-- …
|-- theme/
| |-- source/
| | `-- …
| `-- template/
| |-- mixins.scss
| |-- settings.scss
| `-- theme.scss
`-- reveal.scss 
```

Enter fullscreen mode Exit fullscreen mode

有许多预设的主题文件，我建议使用`simple.scss`作为你自定义主题的基础。在`template`文件夹中有 3 个`scss`文件，包含导入到`source`文件夹中主题样式表的基本样式和变量。

请注意，我描述的是这个框架提供的开箱即用的结构。你完全可以随意修改，但是你*可能*需要修改`Gruntfile.js`，因为那是处理源文件编译的。

这是我的自定义主题文件的样子:

```
/**
 * Custom theme for Reveal.js presentations.
 * Copyright (C) 2018 Chen Hui Jing, https://www.chenhuijing.com/
 */

// Default mixins and settings -----------------
@import '../template/mixins';
@import '../template/settings';
// ---------------------------------------------

// Include theme-specific fonts
@import url('../../lib/font/magnetic-pro/magnetic-pro.css');
@import url('../../lib/font/magnetic-pro-black/magnetic-pro-black.css');

// Theme template ------------------------------
@import '../template/theme';

// ---------------------------------------------
// Customised styles for this presentation
.reveal {
  …
} 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，演示文稿本身加载两个样式表，`reveal.css`和`THEME.css`，后者是从`source`文件夹中的`.scss`文件生成的定制主题文件。这些样式表在`index.html`文件中被引用，比如:

```
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    Your awesome presentation
    <meta name="description" content="A short description of what the talk is about">

    <link rel="stylesheet" href="css/reveal.css">
    <link rel="stylesheet" href="css/theme/jing.css">
    <link rel="stylesheet" href="lib/css/zenburn.css"> 
```

Enter fullscreen mode Exit fullscreen mode

如果您不喜欢 Sass，那么您可以添加自己的自定义 CSS 文件并直接引用它。Reveal.js 是高度可定制的。

## 创建自定义布局

现在基本的设置已经有所介绍，我将继续介绍一些我在演讲中喜欢做的有趣的东西。默认情况下，幻灯片上的内容在页面上居中，但是现在我们有了网格，在页面上放置项目变得容易多了。

受演示软件的启发，演示软件通常提供许多模板来选择不同类型的幻灯片，我们可以创建一些布局，并通过 CSS 类来应用。例如，我有一个两列布局的布局类，如下所示:

```
.l-double {
  display: grid;
  grid-template-columns: 1fr 1fr;
} 
```

Enter fullscreen mode Exit fullscreen mode

对于在我的幻灯片中多次出现的模式，我有几个其他的布局类，也有一些例子，我在单个幻灯片上用内联 CSS 进行一次性调整。

对我来说，我的幻灯片是一种工具，可以在我演讲时帮助我表达我的观点，所以我经常认为它们是一次性的。我能为一次性布局编写合适的 CSS 类吗？当然可以。但是我宁愿花更多的时间去写内容本身。但这只是我。你做你的。

我的另一个布局类是针对一行中的多个项目:

```
.l-multiple {
  display: flex;
  justify-content: space-around;
} 
```

Enter fullscreen mode Exit fullscreen mode

我想借此机会强调一下 Blink 中一个打开时间最长的错误， [Chrome 没有为自动高度伸缩容器](https://bugs.chromium.org/p/chromium/issues/detail?id=721123)中的伸缩图像保留纵横比，这确实让我很恼火。几年前我开始学习 Flexbox，当时 Chrome 仍然是我的主要浏览器，**学到了错误的东西**，因为我认为这个错误是意料之中的行为。

不是的。

<figcaption>火狐 vs Chrome`flex: 1`</figcaption>

[![](img/5db9256cead3e4c3be8678c9f41b25c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TPOCCcYi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/revealjs/flex-bug-640.jpg)

无论如何，我已经学会了通过直接查看规范来仔细检查奇怪的行为，然后跨浏览器检查，看谁在相应地实现它。我现在的主要浏览器是 Firefox。只是把它放在那里。🤷

<figcaption>火狐 vs Chrome`flex: auto`</figcaption>

[![](img/420331cb60f9f19e18dd57d0411bc329.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fzahCQFV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/revealjs/flex-bug2-640.jpg)

查看上述 bug 的最佳方式是在 Firefox(正确)、Chrome(不正确)、Safari(不正确)和 Edge(正确)中加载 Jen Simmon 的 flex 速记示例页面进行比较。

## 黑客攻击了`<style>`标签

最后，我还想在我的演示中展示代码示例，尽管我尽最大努力保持代码简短，以大字体显示并适当突出语法。有时，为了更好地说明代码的效果，我会将代码与一张图片或者更好的是代码所做事情的视频剪辑结合起来。

使用像 Reveal.js 这样的 HTML 框架的一个优点是能够向幻灯片本身添加一些实时编码。在看过来自[的乌娜·克拉韦茨](https://codepen.io/una/pen/Wjvdqm)的幻灯片后，我从她的[那里“借用”了这个技巧。](https://una.im/)

至少对我来说，没有什么比在幻灯片上看到代码变化的效果更好的了。在翻阅了 Una 的代码(她非常友好地把它放在 Codepen 上)后，我发现它涉及到了在`style`标签上使用`contenteditable`属性。

```
<style contenteditable="true">
  /* Put CSS here and watch your page update as you type */
</style> 
```

Enter fullscreen mode Exit fullscreen mode

通过一些额外的样式，我们可以将上面的标记制作成一个可编辑的代码块，将有效的 CSS 规则应用到页面上。有些人可能会提高这样做的有效性，但我认为这是一个灰色地带。

当前的 HTML 规范规定,`style`元素只能在两种上下文中使用，其中需要元数据内容，或者在作为`<head>`元素的子元素的`<noscript>`元素中使用。换句话说，`<style>`标签应该只出现在页面的`<head>`中。

但是，在 [HTML5.2 规范](https://www.w3.org/TR/html52/document-metadata.html#the-style-element)中，又指定了一个上下文:在主体中，流内容被期望的地方。所以官方来说，这种方法现在是无效的 HTML，但是将来会有效。

唯一需要的样式是生成一个框的`display`值(基本上是除了`none`以外的任何有效值)。但是再修饰一下可能是个好主意，所以让我们加入一些颜色和填充物。

```
style {
  display: block;
  background-color: #2d2d2d;
  color: #ccc;
  padding: 1em;
  white-space: pre;
  width: 100%;
  overflow: auto;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 在演示幻灯片上实时编码 CSS

到目前为止，我已经在我的幻灯片上使用了两种实时编码布局，一种是双面板的，一种是三面板的。双面板将包括我想在左边的目标标记，和应用在右边的 CSS。该布局的标记如下所示:

```
<div class="livecode livecode-2p">
  <div class="result"></div>
  <div class="code"><style class="code-editor" contenteditable="true"></style></div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

对于三面板:

```
<div class="livecode livecode-3p">
  <pre class="markup"><code></code></pre>
  <div class="result"></div>
  <div class="code"><style class="code-editor" contenteditable="true"></style></div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

Reveal.js 自带语法高亮库， [highlight.js](https://highlightjs.org/) ，默认启用。所以只要你加载了`zenburn.css`，任何带有`<pre>`和`<code>`的东西都会被应用样式。

我的大多数风格都与布局有关。我总是在 Firefox 中运行我的幻灯片，所以我不介意使用许多新的 CSS 属性。尽管在上台前先检查一遍整副牌是个好主意，只是为了确保东西没有坏掉。世事难料。

```
.livecode {
  display: grid;
  grid-gap: 0.5em;
  margin: 0;
  padding: 0;

  .result {
    max-height: 100%;
    overflow-y: scroll;
    width: 100%;
    border: 1px dashed $headingColor;
  }

  .code {
    text-align: left;
    width: 100%;
    font-family: 'Dank Mono', monospace;
    font-size: 75%;
    color: #efdcbc;
    background-color: #3f3f3f;
    padding: 0.5em;
    border-radius: 0.25em;
    overflow-y: scroll;
  }
}

.code-editor {
  display: block;
  height: 100%;
  white-space: pre-wrap;
} 
```

Enter fullscreen mode Exit fullscreen mode

我正在使用 Grid，所以`.livecode`类设置了它，然后我有两个不同的类分别为每个布局指定网格和项目位置。

```
.livecode-2p {
  grid-template-columns: 50% 50%;
  grid-template-rows: 1fr;
  max-height: 65vh;
}

.livecode-3p {
  grid-template-columns: 50% 50%;
  grid-template-rows: max-content 1fr;
  grid-template-areas: 'a b'
                       'a c';
  max-height: 70vh;

  .markup {
    grid-area: b;
  }

  .result {
    grid-area: a;
  }

  .code {
    grid-area: c;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

根据您尝试演示的示例，可能有额外的样式调整和覆盖来确保您的示例按预期工作，因为 Reveal.js 本身有相当多的样式。但是如果你要在舞台上现场编码 CSS，我会假设层叠对你来说不是问题。

## 包装完毕

我在演讲后收到了一些反馈，认为能够实时看到我正在解释的 CSS 的效果有助于理解，所以我想分享一下我是如何制作幻灯片的。我开始尝试的另一件事是完全抛弃幻灯片，用 Firefox DevTools 做演示。

我们会看到结果如何😬。祝我的朋友们快乐！