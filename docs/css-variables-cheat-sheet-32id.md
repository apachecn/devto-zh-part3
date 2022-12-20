# CSS 变量备忘单

> 原文：<https://dev.to/proticm/css-variables-cheat-sheet-32id>

> 最初发布于[devi conduct](https://www.devinduct.com/blogpost/10/css-variables-cheat-sheet)。

# 简介

CSS 变量是一个非常有用的特性，它们使我们能够做我们用 SASS 补偿的事情。它们由 CSS 作者定义，包含特定的值，可以在项目中的任何需要的地方重用。它们使用自定义符号，并通过使用`var`函数来访问。

# 申报

CSS 变量可以在任何元素上声明，我们这样声明:

```
div {
    --primary-color: green;
} 
```

通过使用`var`函数:
来访问它们

```
div {
    color: var(--primary-color);
} 
```

# 一个示例用例

让我们定义一个用例，在这个用例中，我们为 HTML 定义以下 CSS 规则:

```
.card-wrapper {
    padding: 1rem;
    display: flex;
    flex-direction: column
}
.card {
    border: 1px solid #0068d8;
    margin-bottom: 0.5rem
}
.card-title {
    padding: 1rem;
    background-color: #007bff;
    color: #fff
}
.card-body {
    padding: 1rem;
    background-color: #76b8ff;
    color: #fff
} 
```

并将它们应用到下面的 HTML:

```
<section class="card-wrapper">
    <article class="card">
        <header class="card-title">
            Card 1
        </header>
        <section class="card-body">
            Lorem ipsum dolor sit amet...
        </section>
    </article>
    <article class="card">
        <header class="card-title">
            Card 2
        </header>
        <section class="card-body">
            Lorem ipsum dolor sit amet...
        </section>
    </article>
    <article class="card">
        <header class="card-title">
            Card 3
        </header>
        <section class="card-body">
            Lorem ipsum dolor sit amet...
        </section>
    </article>
</section> 
```

你能找出重复的地方吗？在分析了我们的 CSS 之后，我们得出结论，以下属性是重复的:

1.  填料
2.  颜色

因此，我们可以利用新的 CSS 特性。我们可以使用 CSS 变量来定义元素的填充和颜色。通过在一个`:root`伪类上声明它们，我们可以克服这个问题，并在任何需要的地方重用`padding`和`color`。我们更新后的 CSS 是这样的:

```
:root {
    --primary-color: #fff;
    --primary-padding: 1rem
}

.card-wrapper {
    padding: var(--primary-padding);
    display: flex;
    flex-direction: column
}
.card {
    border: 1px solid #0068d8;
    margin-bottom: 0.5rem
}
.card-title {
    padding: var(--primary-padding);
    background-color: #007bff;
    color: var(--primary-color)
}
.card-body {
    padding: var(--primary-padding);
    background-color: #76b8ff;
    color: var(--primary-color)
} 
```

HTML 保持不变，在浏览器中打开页面后，结果应该是一样的。

# 继承

CSS 变量可以像其他 CSS 属性一样继承它的值。如果在元素上没有为其设置值，则使用其父元素的值。让我们更新我们的 CSS 来反映这一点:

```
:root {
    --primary-color: #fff;
    --primary-padding: 1rem
}

.card-wrapper {
    padding: var(--primary-padding);
    display: flex;
    flex-direction: column
}
.card {
    border: 1px solid #0068d8;
    margin-bottom: 0.5rem
}
.card-title {
    --primary-padding: 1.5rem;
    --primary-color: red;
    padding: var(--primary-padding);
    background-color: #007bff;
    color: var(--primary-color)
}   
.card-body {
    padding: var(--primary-padding);
    background-color: #76b8ff;
    color: var(--primary-color)
} 
```

现在，如果我们在浏览器中运行页面，结果应该是每个卡片标题的`padding`和`color`分别被设置为`1.5rem`和`red`，而不是在`:root`伪类上定义的值。这意味着除了 card header 之外，所有元素都从父元素(在本例中是`:root`伪类)继承了它们的颜色和填充。

# 默认(回退)值

CSS 变量支持默认值或后备值，这意味着如果自定义 CSS 变量失败，我们可以定义我们想要使用的值。这是通过将第二个参数传递给`var`函数来实现的。当 CSS 变量未定义时，给定的值将用作替代。让我们更新 CSS 来反映这一点:

```
:root {
    /*--primary-color: #fff; a fallback value is used instead of this variable*/
    --primary-padding: 1rem
}

.card-wrapper {
    padding: var(--primary-padding, 1rem);
    display: flex;
    flex-direction: column
}
.card {
    border: 1px solid #0068d8;
    margin-bottom: 0.5rem
}
.card-title {
    --primary-padding: 1.5rem;
    --primary-color: red;
    padding: var(--primary-padding, 1rem);
    background-color: #007bff;
    color: var(--primary-color, #fff)
}   
.card-body {
    padding: var(--primary-padding, 1rem);
    background-color: #76b8ff;
    color: var(--primary-color, #fff)
} 
```

输出应该是相同的，因为我们已经将`white`颜色定义为回退值。稍微摆弄一下看看效果。

# 无效值

如果 CSS 变量定义了一个无效的值，浏览器将为给定的属性设置初始值或父值。

首先，它将检查属性是否是可继承的，如果是，它将从父元素(如果元素有父元素的话)获取值。如果元素没有父元素，将使用属性的默认初始值。让我们更新卡片包装器和标题类来反映这种行为:

```
...

.card-wrapper {
    padding: var(--primary-padding);
    display: flex;
    flex-direction: column;
    color: orange /* assign a new color here */
}
.card-title {
    --primary-padding: 1.5rem;
    --primary-color: 1px; /* assign an invalid value here */
    padding: var(--primary-padding, 1rem);
    background-color: #007bff;
    color: var(--primary-color, #fff)
}

... 
```

现在，如果我们在浏览器中运行页面，卡片标题的颜色将变为`orange`

# 动态用法(JavaScript)

CSS 变量可以从 JavaScript 中动态访问。我们可以像对任何其他属性一样获取或设置它的值:

```
const el = document.querySelector(':root');

// get the value
const padding = el.style.getPropertyValue('--primary-padding');

// set the value
el.style.setProperty('--primary-padding', '3rem'); 
```

# 出现

在下一篇关于 CSS 变量的文章中，我将介绍一个更有用的例子，并创建一个简单的主题切换器。请务必查看[我的博客](https://www.devinduct.com/):)上的更新

# 进一步阅读

查看[浏览器兼容性](https://caniuse.com/#search=css%20variables)
查看这篇[文章](https://medium.freecodecamp.org/everything-you-need-to-know-about-css-variables-c74d922ea855)