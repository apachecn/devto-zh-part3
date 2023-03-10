# 如何用 CSS 变量创建主题

> 原文：<https://dev.to/browserlondon/how-to-theme-with-css-variables-31ho>

不久前，我们写了一篇关于使用 Sass maps 和一点点 T2 BEM 来处理创建 CSS 主题的棘手前端任务的流行文章，而不需要与特殊性做斗争。

然而，三年过去了，我们所拥有的 CSS 工具已经取得了巨大的进步，现在有了一种更加灵活、经得起未来考验的方法来解决这个问题。

## 输入 CSS 变量

你可能也听说过这些被称为 CSS 自定义属性。顾名思义，它们允许您存储对值的引用，并在以后使用它们:

首先，我们存储一个值:

```
:root {
  --green-bright: #27efa1;
} 
```

…然后我们利用它:

```
background-color: var(--green-bright); 
```

在这一点上，CSS 变量看起来和 Sass 中的变量没有太大的不同，但是有一个非常明显的区别——CSS 变量值可以被重新赋值。

有了这些知识，我们可以做一些真正有趣的事情。例如，假设您想要主题化一个单独的组件——我将使用一个包含内容的通用部分的例子。

首先，我们设置一些变量:

```
:root {
  --sectionBG: var(--white);
  --sectionText: var(--grey-dark);
  --linkColor: var(--green-bright);
  --titleColor: var(--green-bright);
} 
```

然后，利用这些变量构建我们的默认部分样式:

```
section {
  background-color: var(--sectionBG);
  color: var(--sectionText);
  padding: 7rem 0;

  h2 {
    color: var(--titleColor);
  }

  a {
    color: var(--linkColor);
  }

  .btn {
    &:hover {
      background-color: var(--linkColor);
      border-color: var(--linkColor);
      color: var(--sectionText);
    }
  }
} 
```

现在，当我们想创建一个替代主题时，我们可以使用 BEM 修改器类来重新分配这些变量:

```
.section--grey {
  --sectionBG: var(--grey-dark);
  --sectionText: var(--grey-light);
  --titleColor: var(--white);
} 
```

因此，几乎没有额外的 CSS，我们已经为我们的部分创建了一个全新的主题。酷！

## 进一步外推 CSS 变量

记住这个技巧，很容易想象我们可以如何进一步扩展这个想法——例如，我们可以创建一组通用变量，如`--theme-primary`和`--theme-accent`,通过仔细规划和使用级联，我们可以通过重新分配它们来重新主题化整个应用程序。

你也可以通过在`head`中注入一个`style`标签来逐页创建不同的主题，这将在不同的页面上重新分配这些变量——我们在 Browser London 网站上做了类似的事情。这是非常好的，因为这意味着你不需要提前预定义主题——它们可以由用户生成并存储在数据库中(同样，这也是我们在网站上使用的)。

我最喜欢这种方法的一点是，它包含了 CSS 的一些核心特性，而不是反对它们。这样做不仅有利于减少代码的大小和复杂性，而且有助于 UI 的一致性，因为组件之间的联系更加紧密，而不是孤立的块。另一个有用的优势是，因为 CSS 变量是浏览器固有的，所以您可以在开发工具中动态地试验它们。我发现这对于测试我的调色板中哪种颜色在特定的场景下效果最好非常有用。

[![Css variables example](img/88bcdb346ff1ac6760da0f5a7952c89b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--amIbnLf7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2018/12/CSS-theming-1-copy-300x160.png)

## 要考虑的事情

你可能知道我是这种技术的忠实粉丝，但是和所有现代 CSS 一样，有一些事情需要考虑。其中最主要的是前端开发人员的好朋友——浏览器支持。

在撰写本文时，全球范围内超过 87.5%的浏览器支持 CSS 变量，这确实很好，但仍有 12.5%的浏览器未得到考虑(如你所料，包括所有版本的 Internet Explorer)。鉴于我们很可能将它们用于我们应用程序的基础部分，而不是[渐进增强](https://www.browserlondon.com/blog/2017/03/14/performance-benefits-progressive-enhancement/)，我们需要一个好的方法来处理这个问题。

目前，我在构建过程中将 [PostCSS](https://postcss.org/) 与 [postcss-custom-properties 插件](https://github.com/postcss/postcss-custom-properties)一起使用，这基本上是在变量用法之上添加了一个新的 CSS 属性，并带有该变量的编译值。

[![Css variables](img/818a6510219d802e1b3a9f7bda350fd1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5ncpAzv---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2018/12/CSS-theming-2-copy-300x78.png)

这似乎是一个相当好的解决方案，但仍然不理想，我确实遇到过这样的情况，我多次重新分配了一个变量，插件却把它编译成了错误的值。在实践中，这导致了像按钮这样的东西在 IE 中与其他浏览器的颜色不同。这取决于你来决定这样的事情对你的产品有多重要，而且肯定有一个反驳的论点，也许我一开始就把事情弄得太复杂了，以至于这种事情发生了。

[![Css variables guide](img/deddbaf402a8135dc784bd521b242091.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xpD38Ajf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2018/12/CSS-theming-3-copy-300x86.png)

除了这些小问题，我认为这是一个拥抱现代 CSS 和简化我们的造型过程的好方法。虽然本文仅限于主题化，但是您可以找到更多自定义属性的用例，希望您会喜欢它们提供给您的灵活性。

* * *

帖子[如何用 CSS 变量](https://www.browserlondon.com/blog/2019/01/15/css-variables-theming/)主题化首先出现在[浏览器伦敦](https://www.browserlondon.com)上。