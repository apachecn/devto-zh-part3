# 使用多个 CSS 主题

> 原文：<https://dev.to/wendell_adriel/working-with-multiple-css-themes-5aej>

## 代码共享

我有一个名为 **[CodeShare](https://codeshare.com.br)** 的项目，用葡萄牙语创建免费的高质量内容，让更多巴西人有机会学习英语。

所以当我在那里写一篇文章时，我也会在这里发表，但是用英语(当然)，所以如果你想查看这篇文章的原始版本(葡萄牙语)，你可以在这里查看！

## 简介

在本系列的第一篇文章 中，我们看到了变量是如何在 CSS 上工作的，以及我们如何使用它们来为我们的项目创建主题。在本文中，我们将实践这一点，创建一些简单的主题，并看看如何在它们之间转换。

## 创建默认主题

正如我们之前看到的，`:root`选择器可以用来存储主题的全局变量。为了给我们的应用程序创建一个默认主题，我们将在这个选择器中定义一些将被我们的元素使用的变量:

```
:root {
    --global-bg-color: #fff;
    --global-font-family: Verdana, sans-serif;
    --global-text-color: #222;
    --global-link-color: blue;
    --global-link-hover: lightblue;
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们定义了一些变量，这些变量将被用作主题的基本值。这样，我们就可以使用这些值来设计元素的样式。因为我们只是在做一个例子，我们拥有的变量数量很少，但是在一个大项目中，这个列表可能会更大。现在，让我们使用我们的变量:

```
html {
    background: var(--global-bg-color);
    font-family: var(--global-font-family);
    color: var(--global-text-color);
}

a {
    color: var(--global-link-color);
    text-decoration: none;
    font-weight: 700;
}

a:hover {
    color: var(--global-link-hover);
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们使用了变量来控制背景和文本颜色、字体以及链接的颜色。如果在我们的项目中，我们想要定制这些值中的任何一个，我们只需要改变我们想要的变量值，所有使用它的元素都会更新，非常简单。现在让我们创建一些元素来检查我们的主题:

```
<!DOCTYPE html>
<html>
    <head>
        Tematização com CSS
        <meta charset="UTF-8" />

        <style>
            <!-- STYLE HERE -->
        </style>
    </head>

    <body>
        <h1>Tematização com variáveis CSS</h1>
        <p>
            Exemplo para ser usado em um artigo na
            <a href="https://codeshare.com.br" target="_blank">CodeShare</a>
        </p>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/wyr0q30wzk](https://codesandbox.io/embed/wyr0q30wzk)

## 创建夜间模式主题

现在我们已经有了默认的主题，我们可以为我们的应用程序创建一个新的主题。很多网站都有**【夜间模式】**来方便夜间阅读。让我们为此创建一个主题。

有一些处理主题的方法，但是我最喜欢的是在 **HTML** 上使用`data-`属性。这些`data-`属性是一个约定，用于定义标签的自定义属性，并标识我们正在使用的主题，我们将创建一个`data-theme`属性。具有该属性的所有元素及其子元素都将被修改。因为我们的新主题是夜晚模式，我们称之为 ***黑暗*** 。

因为我们的元素已经在使用我们创建的变量，为了创建一个新的主题，我们只需要更新那些我们需要修改的值，这样我们修改的值将覆盖其他的值，而我们没有修改的值将从`:root`元素继承。我们将使用 **CSS** 上的属性选择器来应用我们的主题。因为我们的主题是一个**全局主题**，我们将它链接到`<html>`标签:

```
html[data-theme='dark'] {
  --global-bg-color: #444;
  --global-text-color: #ddd;
  --global-link-color: yellow;
  --global-link-hover: lightyellow;
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们改变了背景、文本和链接的颜色。现在我们只需要将`data-theme="dark"`属性放入我们的`<html>`标签中就可以看到结果:

[https://codesandbox.io/embed/mp42xzmnp](https://codesandbox.io/embed/mp42xzmnp)

## 让用户切换主题

现在我们为我们的应用程序创建了两个主题，如果我们让用户在它们之间切换会很有趣，对吗？这不是一件很难做到的事情，让我们创建这个选项。首先，让我们从我们的`<html>`标签中删除`data-theme="dark"`属性，并创建一个元素，让用户在点击时切换主题。

```
<!DOCTYPE html>
<html>
    ...
    <body>
        <span id="theme-toggle" class="theme-toggle"></span>
        ...
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们来设计这个元素的样式，为此我们将创建两个新的变量。我们将创建这个元素作为一个固定的元素，这样用户可以随时切换主题。当我们的*主题被激活时，我们也将为这些变量分配不同的值，使得这个元素也将更新它的外观:* 

```
:root {
    ...
    --global-theme-toggle-bg: #ccc;
    --global-theme-toggle-content: '🌞 THEME';
}

html[data-theme='dark'] {
    ...
    --global-theme-toggle-bg: #000;
    --global-theme-toggle-content: '🌝 THEME';
}

.theme-toggle {
    position: fixed;
    right: 20px;
    top: 20px;
    background: var(--global-theme-toggle-bg);
    padding: 10px;
    border-radius: 5px;
    font-size: 0.8rem;
    font-weight: 700;
    cursor: pointer;
}

.theme-toggle::after {
    content: var(--global-theme-toggle-content);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经完成了组件的可视部分的设置，让我们添加所需的动作，使我们的主题在元素被点击时切换:

```
function toggleTheme () {
    const htmlTag = document.getElementsByTagName("html")[0]
    if (htmlTag.hasAttribute("data-theme")) {
        htmlTag.removeAttribute("data-theme")
        return
    }

    htmlTag.setAttribute("data-theme", "dark")
}

document
    .getElementById("theme-toggle")
    .addEventListener("click", toggleTheme); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们创建了一个函数，它将检查`<html>`标签是否有一个`data-theme`属性，如果有，它将删除它，如果没有，它将添加这个属性的值 ***暗*** 来激活我们的夜间模式主题。之后，我们在元素中创建一个侦听器，当它被单击时，它将运行我们刚刚创建的函数:

[https://codesandbox.io/embed/m45wj6xz68](https://codesandbox.io/embed/m45wj6xz68)

## 改善主题切换

恭喜我们！我们已经让我们的用户可以切换我们的应用程序的主题，但我们仍然有一些小细节，我们可以改善。例如，当我们切换主题时，更新是以一种非常突然的方式进行的，以一种更平滑的方式进行更新会很有趣，我们可以使用 **CSS** 中的`transition`规则轻松地做到这一点，看看有多简单:

```
html {
    ...
    transition: all 1s;
}

a {
    ...
    transition: all 1s;
} 
```

Enter fullscreen mode Exit fullscreen mode

完成了！！！看看现在主题之间的过渡是如何变得更加平滑和悦目的！！！但是我们仍然可以改进其他的东西。当用户进入我们的应用程序，改变主题并离开时，我们希望当他回来时，网站将加载最后选择的主题，为此我们将使用`localStorage`来存储这些信息。我们需要对我们的`toggleTheme`函数做一点小小的改变，并创建一个额外的函数来帮助我们:

```
function toggleTheme () {
    const htmlTag = document.getElementsByTagName('html')[0]
    if (htmlTag.hasAttribute('data-theme')) {
        htmlTag.removeAttribute('data-theme')
        return window.localStorage.removeItem("site-theme")
    }

    htmlTag.setAttribute('data-theme', 'dark')
    window.localStorage.setItem("site-theme", "dark")
}

function applyInitialTheme () {
    const theme = window.localStorage.getItem("site-theme")
    if (theme !== null) {
        const htmlTag = document.getElementsByTagName("html")[0]
        htmlTag.setAttribute("data-theme", theme)
    }
}

applyInitialTheme();

document
    .getElementById("theme-toggle")
    .addEventListener("click", toggleTheme); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，现在我们可以为我们的用户提供一个特殊的体验！！！

## 在特定区域应用主题

我们在上面看到了如何创建两个**全局主题**并在它们之间切换，这意味着它们作为一个整体应用到我们的应用程序中，但我们也可以将主题应用到我们应用程序中的特定区域。为了说明这一点，让我们创建一个只改变字体的主题，并将其应用于一些元素:

```
[data-theme='comic'] {
    --global-font-family: 'Comic Sans MS';
}

p, span {
    font-family: var(--global-font-family);
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<body>
    <span data-theme="comic" id="theme-toggle" class="theme-toggle"></span>
    <h1>Tematização com variáveis CSS</h1>
    <p>
        Exemplo para ser usado em um artigo na
        <a href="https://codeshare.com.br" target="_blank">CodeShare</a>
    </p>

    <p data-theme="comic">
        Exemplo de um tema aplicado em partes específicas da página! Esse texto
        tem o tema "comic"!
    </p>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/nnqvpvmx1m](https://codesandbox.io/embed/nnqvpvmx1m)

## 结论

在上面的例子中，我们使用了三个简单的主题，但是在更大的项目中，主题的复杂性会更大。考虑到这一点，我创建了一个名为 **CSS 主题管理器**的库，帮助管理基于 CSS 变量的主题，检查一下，如果你觉得有趣，就在你的项目中使用它！

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)/[CSS-主题-管理器](https://github.com/WendellAdriel/css-theme-manager)

### 零依赖库，轻松管理你的应用程序的 CSS 主题

<article class="markdown-body entry-content container-lg" itemprop="text">

# CSS 主题管理器

[![license](img/e752ba75a25296dcb4eb95cc686e4a38.png) ](https://github.com/WendellAdriel/css-theme-manager/blob/master/LICENSE) [ ![All Contributors](img/978c1092a51de6094a8f8a354af886db.png)](https://github.com/WendellAdriel/css-theme-manager#contributors)

[![version](img/659369b5cd81df1f46ad62f765aec3d2.png)](https://www.npmjs.com/package/css-theme-manager)[![downloads](img/1435a44ebde6317a776fce4610b1c9fa.png)](http://npmcharts.com/compare/css-theme-manager)[![size](img/e9fee69b188476d5c463bd216820ec89.png)](https://unpkg.com/css-theme-manager/dist/css-theme-manager.js)[![gzip size](img/e10051a736024950bb551b2a7d52aef4.png)T11】](https://unpkg.com/css-theme-manager/dist/css-theme-manager.js)

[![Watch on GitHub](img/d53481700c39efa64965f146a370168d.png)](https://github.com/WendellAdriel/css-theme-manager/watchers)[![Star on GitHub](img/2fdce3aaf5280b8950c0b6717d9d5622.png)](https://github.com/WendellAdriel/css-theme-manager/stargazers)[![Tweet](img/8f121b74f53d5b8215c3ad2417c3c267.png)](https://twitter.com/intent/tweet?text=Check%20out%20css-theme-manager!%20https://github.com/WendellAdriel/css-theme-manager%20%F0%9F%91%8D)

零依赖库，轻松管理你的应用程序的 CSS 主题

## 如何使用

安装软件包

```
npm install css-theme-manager --save
// or with yarn
yarn add css-theme-manager 
```

导入并初始化带有默认主题的 CSS 主题管理器`theme`是一个对象，以你的变量名作为键，变量值作为值。

```
import CSSThemeManager from 'css-theme-manager'

const themeManager = new CSSThemeManager({
  'bg-color': '#fff',
  'text-color': 'darkblue',
  'featured-font': 'Verdana, sans-serif'
})
```

Enter fullscreen mode Exit fullscreen mode

这将在`:root`选择器中创建并插入给定变量。用这个 **CSS 主题管理器**创建的所有变量都有一个`csstm-`前缀。上面的代码将导致:

```
:root {
    --csstm-bg-color: '#fff';
    --csstm-text-color: 'darkblue';
    --csstm-featured-font: 'Verdana, sans-serif';
}
```

Enter fullscreen mode Exit fullscreen mode

检查下面的 **API 参考**来检查所有你可以…

</article>

[View on GitHub](https://github.com/WendellAdriel/css-theme-manager)

我希望你喜欢这篇文章，如果你喜欢，不要忘了评论并与你的朋友分享这篇文章！！！再见！😎*