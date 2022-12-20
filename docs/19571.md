# 将 CSS 重构为 Sass mixin

> 原文：<https://dev.to/brob/refactoring-css-into-a-sass-mixin-59m2>

我已经开始每周直播办公时间。在我的第一次努力中，我主要是在这个网站的代码上工作。我决定为文章创建一个新的推广风格，并意识到我需要重构我的一些 Sass。

当我在写一个新的 Sass mixin 时，我突然想到，可能有一些设计人员和开发人员还没有把普通的 CSS 转换成 Sass 函数。在本文中，我想展示如何将常用的 CSS 转换成一个干巴巴的(不要重复自己)Sass mixin。

[https://www.youtube.com/embed/6UxUuDZ_ujQ](https://www.youtube.com/embed/6UxUuDZ_ujQ)

## 启动 CSS

通常当我谈论 Sass mixins 时，我喜欢展示按钮上的电源。然而，这并不总是最适用的用例。在这种情况下，我们将从我的“[前 3 次使用::before 和::after](https://bryanlrobinson.com/blog/2018/08/07/top-3-uses-of-after-and-before-css-pseudo-elements/) ”中提取“有趣的边框”概念，并将其转换为可重用的 Sass 组件。

在这种情况下，我们需要用 CSS 影响四个主要元素。父元素、父元素内的一组通用(*)元素以及伪元素前的::和后的::元素。

```
.article-promo {
    position: relative;
    padding: 20px;
}
.article-promo > * {
    z-index: 100;
    position: relative;
}
.article-promo::before {
    background-color: white;
    content: "";
    display: block;
    position: absolute;
    top: 10px;
    left: 10px;
    width: calc(100% - 20px);
    height: calc(100% - 20px);
    z-index: 0;
}
.article-promo::after {
    background-image: linear-gradient(120deg, #eaee44, #33d0ff);
    content: "";
    display: block;
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 0;
    mix-blend-mode: screen;
} 
```

Enter fullscreen mode Exit fullscreen mode

看一下这段代码，你会发现每次我们想要做不同的渐变边框时，手工创建的工作量会很大。

## 第一步:识别潜在的 Sass 变量

开始使用 Sass 的最好理由之一是它的变量。在这种情况下，我们将使用变量为我们的边界创建可定制的属性。

我将从寻找我想在每个实现上改变的东西开始。

对于渐变边框，我想调整“边框大小”，开始颜色，结束颜色，原始背景颜色和渐变的方向。

让我们将这些值移到 CSS 顶部的变量中。

```
$border-size: 10px;
$original-background-color: white;  
$gradient-start-color: #eaee44;  
$gradient-end-color: #33d0ff;  
$gradient-direction: 120deg; 
```

Enter fullscreen mode Exit fullscreen mode

在我们最初使用静态值的地方，我们现在用变量来代替它们。

```
.article-promo::before {
    ... background-color: $original-background-color;
    top: $gradient-size;
    left: $gradient-size;
    width: calc(100% - #{$gradient-border-size * 2});
    height: calc(100% - #{$gradient-border-size * 2});
    ...
}

.article-promo::after {
     background-image: linear-gradient($gradient-direction, $gradient-start-color, $gradient-end-color);
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意本例中的`calc()`函数中的`#{}`语法。这是变量插值概念的一个用例。大多数语言都有这种形式。

就 Sass 而言，我们主要在 CSS 函数内部使用该语法。在这种情况下，Sass 看到了`calc()`函数，并打算将整个内容作为一个字符串编译到 CSS 中。`#{}`语法导致 Sass 暂停编译，将值转换成字符串。

## 第二步:用@extends 识别并重构重复代码

在我们的例子中，我们有两个绝对定位的伪元素。这些有四个相同的属性:`content`、`display`、`position`和`z-index`。

我们将使用 Sass 的`@extend`方法将其重构为一个简洁的编译 CSS。

旁注:为什么使用`@extend`而不是创建一个 mixin 并在这里使用`@include`？`@extend`将在 CSS 中为多个选择器创建一个规则集；`@include`将相同的规则集插入到编译后的 CSS 中。在 Sass 中扩展是降低编译的 CSS 的复杂性和大小的一个很好的方法。[这是关于延长](https://sass-lang.com/guide#topic-7)的文件。

我们将首先为我们的属性创建一个“虚拟”选择器。

```
%pseudo-properties {
    content: "";  
    display: block;  
    position: absolute;
    z-index: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

注意`%`在那里。那不是标准的 CSS。我们使用它，以便在调用它之前不生成 CSS。这使选择器的膨胀有所下降。

然后我们可以在我们的伪元素上用`@extend`方法引用这个假选择器。

```
.article-promo::before {  
   @extend %pseudo-properties;
    ...
}

.article-promo::after {  
   @extend %pseudo-properties;
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

编译后的 CSS 会是这样的:

```
.article-promo::before, .article-promo::after {
    content: "";
    display: block;
    position: absolute;
    z-index: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

您的定义块中用于`::before`或`::after`的其他代码稍后将被编译到它自己的选择器中。您可以通过这种方式将代码组织在一起，而不必担心代码膨胀。

## 第三步:嵌套你的选择器，以便于抽象

我们可以使用 Sass 的嵌套能力来稍微清理一下我们的代码。

这也将有助于我们将该功能扩展到除当前选择器之外的任何其他选择器。

```
.article-promo {
    position: relative;
    padding: $border-size * 2;
    > * {
        z-index: 100;
        position: relative;
    }
    &::before {
        @extend %pseudo-properties;
        background-color: $original-background-color;
        top: $gradient-size;
        left: $gradient-size;
        width: calc(100% - #{$gradient-border-size * 2});
        height: calc(100% - #{$gradient-border-size * 2});
    }
    &::after {
        @extend %pseudo-properties;
        background-image: linear-gradient($gradient-direction, $gradient-start-color, $gradient-end-color);
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        mix-blend-mode: screen;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 第四步:将边框功能移入 mixin

大招来了。我们将把我们写在主选择器中的所有东西都移到一个`@mixin`声明中。

我们不需要主选择器，因为那是我们应用混音的地方。我们不需要变量，因为它们将成为我们 mixin 中的参数。

```
@mixin gradient-border() {
    position: relative;
    padding: $border-size * 2;
    > * {
        z-index: 100;
        position: relative;
    }
    &::before {
        @extend %pseudo-properties;
        background-color: $original-background-color;
        top: $gradient-size;
        left: $gradient-size;
        width: calc(100% - #{$gradient-border-size * 2});
        height: calc(100% - #{$gradient-border-size * 2});
    }
    &::after {
        @extend %pseudo-properties;
        background-image: linear-gradient($gradient-direction, $gradient-start-color, $gradient-end-color);
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        mix-blend-mode: screen;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就像 JavaScript 中的函数定义一样，我们将变量传递到括号中。

您甚至可以指定默认值。在我的例子中，我保持了一致的边界和小的调整，所以我选择了默认值。

```
@mixin gradient-border($border-size: 10px, $original-background-color: white, $gradient-start-color: #eaee44, $gradient-end-color: #33d0ff, $gradient-direction: 120deg) {
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 第五步:在你需要的选择器中声明 mixin

最后，我们有一个可以使用的 mixin。我们将使用 Sass `@include`方法。这里有几个例子。

```
.article-promo {  
   @include gradient-border; // Gives all defaults
}
.big-border {
    @include gradient-border(50px); // If declared in order, you don't have to label your arguments
}
.red-gradient {
    @include gradient-border($gradient-start-color: red, $gradient-end-color: darkred);
}

.vertical-gradient {
    @include gradient-border($gradient-direction: to bottom);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 用此举其他例子

这个方向适用于任何你想要重构的 CSS。Sass 给了我们很多功能和灵活性。它可以是变得易于维护的东西，也可以是重新声明的变量和嵌套的噩梦。

创建可重用的组件是任何维护策略的核心。遵循这条路径，您可以在任何项目中创建组件。

1.  识别可重用组件
2.  找到需要定期更换的零件
3.  为了简单起见，嵌套选择器
4.  使用`@extend`让你编译的 CSS 更小

您将从哪个组件开始？