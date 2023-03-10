# 使用 CSS 变量进行主题化

> 原文：<https://dev.to/wendell_adriel/theming-with-css-variables-1o56>

## 代码共享

我有一个名为 **[CodeShare](https://codeshare.com.br)** 的项目，用葡萄牙语创建免费的高质量内容，让更多巴西人有机会学习英语。

因此，当我在那里写帖子时，我也将使用 **DEV.to** 在这里发帖，但当然是用英语。

## 简介

不管我们是在创建一个网站、一个管理页面还是一个应用程序，我们都需要提供一个稳定一致的设计。我们不能有一个软件，在每一页上我们使用不同的字体或所有颜色和形状的按钮。我们需要定义一个图案，不仅是因为视觉方面，而且当我们创建一个颜色和布局的图案时，用户可以学习并很快习惯我们的产品。

为了使这个过程更容易，一段时间以前，我们不得不请求 CSS 预处理程序的帮助，使用变量等功能，使我们的主题化工作变得更容易。很高兴我们不再需要它了，因为 CSS 已经让我们可以自然地处理变量，在本文中，我们将看到如何使用这个特性为我们的项目创建一个简单而高效的设计系统。

## 声明和使用变量

声明一个 CSS 变量非常简单。变量名称**必须以`--`开头**，并且*区分大小写*。

```
.my-class {
    --font-size: 1rem;
} 
```

Enter fullscreen mode Exit fullscreen mode

声明一个变量很容易，使用它也非常简单。我们只需要使用`var()`函数。这个函数有两个参数:第一个是我们想要获取其值的变量的名称，第二个是可选参数，如果没有找到作为第一个参数传递的给定变量，则有一个后备值。

```
.my-class p {
    font-size: var(--font-size, 0.8rem);
} 
```

Enter fullscreen mode Exit fullscreen mode

CSS 上的变量继承值，这意味着如果在给定元素中找不到值，将使用父元素的值。

## 两层主题化

如果在我们的项目中，我们将所有的变量放在一个地方(全局变量)，我们创建了一个真实的单一来源，但是我们失去了模块化我们的主题的能力，并且当一个开发人员需要定制一个组件时，他总是需要重写 CSS 规则。

另一方面，如果我们将变量分散在各处，我们将创建一个模块化的主题，但我们会失去组件之间的一致性，如果我们需要进行一般性的更改，我们将有很多工作要做。

为了解决这个问题，我们将考虑我们的主题有两层，我们将有**全局变量**和**模块变量**。这样我们就能得到两个世界的精华。

## 全局变量

全局变量是通用变量，将用于保持所有组件之间的一致性。全局变量的例子有字体、默认字体大小和调色板。在 CSS 上定义全局变量非常简单，我们使用`:root`选择器，并在其中定义变量。这不是强制性的，但是为了使识别一个变量是否是全局变量变得简单，我喜欢使用`global-`前缀，这样当我读代码的时候，我就可以知道某个值来自一个全局变量。

```
:root {
    --global-font-family: Verdana, sans-serif;
    --global-font-size: 0.8rem;
    --global-text-color: #222;
    --global-primary-title-size: 2rem;
    --global-color-primary: #88498f;
    --global-color-secondary: #779fa1;
    --global-color-warning: #e28413;
    --global-color-danger: #ff6542;
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们定义了一些变量，这些变量将在我们的项目中用作基值:字体、文本和主标题的字体大小、文本的字体颜色和一个有四种不同颜色的调色板。现在我们将使用刚刚定义的全局变量:

```
body {
    font-family: var(--global-font-family);
    font-size: var(--global-font-size);
    color: var(--global-text-color);
}

h1 {
    font-size: var(--global-primary-title-size);
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们定义了所有产品的默认字体、字体颜色和字体大小，我们还定义了主要标题的字体大小。如果我们需要在项目进行过程中改变这些值，我们只需要用新的值改变变量声明就可以了！这样，我们可以在产品中创建某种一致性，也可以为开发人员和设计人员创建一个共同的参考点，以一种快速简单的方式一起工作。

## 模块变量

这些变量的定义范围是有限的。每个模块变量**必须**使用全局变量的值来定义，我们还需要提供一个后备值，以防模块将在不提供全局变量的环境中使用，它不会中断。这不是强制性的，但是为了使识别变量是模块变量变得简单，我喜欢使用`module-`前缀，这样当我读代码的时候，我已经知道某个值来自于 **X** 模块的变量。让我们以一个按钮的模块为例:

```
:root {
    ...
    --global-border-radius-sm: 3px;
    --global-text-color-light: #fff;
}

.btn {
    --btn-border-radius: var(--global-border-radius-sm);
    --btn-text-color: var(--global-text-color-light);
    border-radius: var(--btn-border-radius, 5px);
    color: var(--btn-text-color, #ddd);
}

.btn-primary {
    --btn-primary-bg: var(--global-color-primary);
    --btn-primary-border: var(--global-color-primary);
    background-color: var(--btn-primary-bg, #605770);
    border-color: var(--btn-primary-bg, #605770);
}

.btn-secondary {
    --btn-secondary-bg: var(--global-color-secondary);
    --btn-secondart-border: var(--global-color-secondary);
    background-color: var(--btn-secondary-bg, #7fc29b);
    border-color: var(--btn-secondary-bg, #7fc29b);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 了解两层主题化

好了，现在我们已经看到了如何构建我们的主题，我们需要理解为什么这两层主题工作。我们构建主题的方式是，我们制作独立的模块，因为如果它们在我们有全局变量的上下文中使用，它们将从这些变量中继承值，但是如果它没有找到这些变量，我们也定义了将被使用的回退值。

我们也认识到改变是很容易做到的。例如，如果我们想在产品的任何地方改变原色，我们只需要改变`--global-color-primary`全局变量，我们所有的模块，包括 **btn** 模块都会受到影响。现在假设我们想改变所有按钮的字体颜色，我们可以改变`--global-text-color-light`变量，但这会影响所有其他模块，所以我们只需要改变`--btn-text-color`模块变量，它只会影响 **btn** 模块。

## 结论

我们看到了如何只用 CSS 为我们的项目创建一个一致的、适应性强的主题。使用这种两层主题化方法，我们为我们的开发人员和设计师团队创造了一个更好的环境和体验。您可以在我创建的 Codepen 中查看我们在本文中创建的代码示例:

[https://codepen.io/WendellAdriel/embed/QPxRjN?height=600&default-tab=result&embed-version=2](https://codepen.io/WendellAdriel/embed/QPxRjN?height=600&default-tab=result&embed-version=2)

我希望你喜欢这篇文章，如果你喜欢，不要忘了评论并与你的朋友分享这篇文章！！！再见！😎