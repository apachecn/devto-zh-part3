# 创建用户控制的黑暗或光明模式

> 原文：<https://dev.to/hankchizljaw/create-a-user-controlled-dark-or-light-mode-4p1m>

自从[偏好配色方案媒体查询登陆](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme)以来，人们一直在为他们的网站推出黑暗主题，就像他们使用整个 React 库在他们的静态博客上制作汉堡菜单一样频繁。

很多时候，人们会正确地添加一个暗/亮开关，给用户一个选择。然而，人们经常会做设计中最危险的事情之一:如果用户的操作系统偏好黑暗模式，就做出假设并推出黑暗主题，然后就这样。

如果可以的话，在设计中不做假设是很重要的，所以我将向您展示一种快速简单的方法来默认用户的操作系统偏好，同时给他们一个切换按钮来手动更改他们的偏好。

## 我们在建造什么

[观看视频](https://res.cloudinary.com/andybelldesign/video/upload/v1559290559/misc/dark-light-mode-demo_cbfd3f.mp4)

视频显示，因为我的操作系统处于黑暗模式，演示默认遵守它。然后我使用开关来切换主题。然后由本地存储维护。

### 打基础

<small>*可以完全跳过这一段，由[在 CodePen](https://codepen.io/andybelldesign/pen/654b3b67255eb4a8d5860938685e4d1f) 上叉出基础代码。如果没有，继续添加下面的基本代码。*</small>

你需要一个 HTML 文件，一个 CSS 文件和一个 JavaScript 文件。我在 [Codepen](https://codepen.io) 中构建了这个来让我们所有人的事情变得更简单。

在您的 HTML 文件(或 CodePen 中的 HTML 面板)中，添加以下代码:

```
<main> <article> <h1>Dark mode should be a user preference—not presumed</h1> <p> Yes, by default, when a user has <code>@media (prefers-color-scheme: dark)</code> set, we should set a dark theme, but we should also provide a switch for if the dark them isn’t working out for them. </p> <p>This also benefits users that don’t have <code>@media (prefers-color-scheme: dark)</code> set because they get a toggle to choose, too.</p> </article> <div class="user-toggle"> <div role="status" class="[visually-hidden][js-mode-status]"></div> <button class="[toggle-button] [js-mode-toggle]"> <span class="[toggle-button__text] [js-mode-toggle-text]">Enable dark mode</span> <span class="toggle-button__icon" aria-hidden="true"></span> </button> </div></main> 
```

这就是我们需要的所有 HTML，而`<article>`元素只是作为一个演示。生活在`.user-toggle`里的一切都是重要的部分。

接下来，让我们添加一些 CSS。我们将首先添加我们的核心表示风格。将此粘贴到您的 CSS 文件(或 CodePen 上的 CSS 面板):

```
/* Presentational demo styles */body { font-family: sans-serif; padding: 2rem 1rem; line-height: 1.4; display: grid; place-items: center;}article { max-width: 75ch; margin: 0 auto;}article > * + * { margin-top: 1em;}h1 { font-size: 2.5rem; line-height: 1.1;}p { font-size: 1.2rem; opacity: 0.9;}code { font-weight: 700; font-size: 1.3em; white-space: pre;}.visually-hidden { display: block; height: 1px; width: 1px; overflow: hidden; clip: rect(1px 1px 1px 1px); clip: rect(1px, 1px, 1px, 1px); clip-path: inset(1px); white-space: nowrap; position: absolute;} 
```

酷——我们已经有了基本的 HTML 和 CSS。你可能还想添加这个我在演示中使用的[重置](https://codepen.io/andybelldesign/pen/Ygmwym.css)。

[CSS 自定义属性](https://developer.mozilla.org/en-US/docs/Web/CSS/--*)正在为我们做繁重的工作，所以接下来，让我们在`:root`级别定义一些，这是在`<html>`元素上。

```
:root { --color-mode: 'light'; --color-dark: #141414; --color-dark-alpha: rgba(0, 0, 0, 0.1); --color-light: #efefef; --color-light-alpha: rgba(255, 255, 255, 0.9); --icon-sun: url('data:image/svg+xml,\ \ <path d="M18 12c0-0.811-0.161-1.587-0.455-2.295-0.304-0.735-0.75-1.395-1.303-1.948-0.552-0.552-1.213-0.998-1.948-1.303-0.707-0.293-1.483-0.454-2.294-0.454s-1.587 0.161-2.295 0.455c-0.735 0.304-1.395 0.75-1.948 1.302s-0.998 1.213-1.302 1.948c-0.294 0.708-0.455 1.484-0.455 2.295s0.161 1.587 0.455 2.295c0.304 0.735 0.75 1.395 1.303 1.948 0.552 0.552 1.213 0.998 1.948 1.303 0.707 0.293 1.483 0.454 2.294 0.454s1.587-0.161 2.295-0.455c0.735-0.304 1.395-0.75 1.948-1.303s0.998-1.213 1.303-1.948c0.293-0.707 0.454-1.483 0.454-2.294zM16 12c0 0.544-0.108 1.060-0.303 1.529-0.202 0.489-0.5 0.929-0.869 1.299s-0.81 0.667-1.299 0.869c-0.469 0.195-0.985 0.303-1.529 0.303s-1.060-0.108-1.529-0.303c-0.489-0.202-0.929-0.5-1.299-0.869s-0.667-0.81-0.869-1.299c-0.195-0.469-0.303-0.985-0.303-1.529s0.108-1.060 0.303-1.529c0.202-0.489 0.5-0.929 0.869-1.299s0.81-0.667 1.299-0.869c0.469-0.195 0.985-0.303 1.529-0.303s1.060 0.108 1.529 0.303c0.489 0.202 0.929 0.5 1.299 0.869s0.667 0.81 0.869 1.299c0.195 0.469 0.303 0.985 0.303 1.529zM11 1v2c0 0.552 0.448 1 1 1s1-0.448 1-1v-2c0-0.552-0.448-1-1-1s-1 0.448-1 1zM11 21v2c0 0.552 0.448 1 1 1s1-0.448 1-1v-2c0-0.552-0.448-1-1-1s-1 0.448-1 1zM3.513 4.927l1.42 1.42c0.391 0.391 1.024 0.391 1.414 0s0.391-1.024 0-1.414l-1.42-1.42c-0.391-0.391-1.024-0.391-1.414 0s-0.391 1.024 0 1.414zM17.653 19.067l1.42 1.42c0.391 0.391 1.024 0.391 1.414 0s0.391-1.024 0-1.414l-1.42-1.42c-0.391-0.391-1.024-0.391-1.414 0s-0.391 1.024 0 1.414zM1 13h2c0.552 0 1-0.448 1-1s-0.448-1-1-1h-2c-0.552 0-1 0.448-1 1s0.448 1 1 1zM21 13h2c0.552 0 1-0.448 1-1s-0.448-1-1-1h-2c-0.552 0-1 0.448-1 1s0.448 1 1 1zM4.927 20.487l1.42-1.42c0.391-0.391 0.391-1.024 0-1.414s-1.024-0.391-1.414 0l-1.42 1.42c-0.391 0.391-0.391 1.024 0 1.414s1.024 0.391 1.414 0zM19.067 6.347l1.42-1.42c0.391-0.391 0.391-1.024 0-1.414s-1.024-0.391-1.414 0l-1.42 1.42c-0.391 0.391-0.391 1.024 0 1.414s1.024 0.391 1.414 0z"></path>\ '); --icon-sun-filter: invert(0.61) sepia(0.8) saturate(5) hue-rotate(0deg) brightness(0.92); --icon-moon: url('data:image/svg+xml,\ \ <path d="M21.996 12.882c0.022-0.233-0.038-0.476-0.188-0.681-0.325-0.446-0.951-0.544-1.397-0.219-0.95 0.693-2.059 1.086-3.188 1.162-0.696 0.047-1.399-0.027-2.077-0.226-0.656-0.192-1.29-0.501-1.874-0.932-0.655-0.484-1.181-1.074-1.575-1.729-0.409-0.68-0.676-1.432-0.792-2.206s-0.082-1.571 0.11-2.342c0.184-0.741 0.514-1.46 0.999-2.115 0.142-0.191 0.216-0.435 0.191-0.691-0.053-0.55-0.542-0.952-1.092-0.898-1.117 0.109-2.186 0.399-3.172 0.843-1.005 0.452-1.925 1.065-2.723 1.808-0.883 0.82-1.618 1.801-2.159 2.901-0.523 1.064-0.863 2.238-0.978 3.485-0.125 1.347 0.024 2.658 0.402 3.878 0.392 1.266 1.031 2.431 1.863 3.433s1.86 1.843 3.033 2.461c1.13 0.595 2.392 0.982 3.739 1.106s2.659-0.025 3.878-0.403c1.266-0.392 2.431-1.031 3.433-1.863s1.843-1.86 2.461-3.033c0.595-1.13 0.982-2.392 1.106-3.739zM19.567 14.674c-0.126 0.351-0.276 0.689-0.447 1.014-0.493 0.937-1.166 1.76-1.969 2.427s-1.735 1.178-2.747 1.491c-0.973 0.302-2.021 0.421-3.102 0.321s-2.089-0.41-2.99-0.884c-0.937-0.493-1.76-1.166-2.427-1.969s-1.178-1.735-1.491-2.747c-0.302-0.973-0.421-2.021-0.321-3.102 0.092-1 0.365-1.938 0.782-2.786 0.43-0.878 1.018-1.661 1.725-2.319 0.64-0.595 1.377-1.086 2.183-1.449 0.179-0.081 0.362-0.155 0.548-0.223-0.092 0.257-0.171 0.516-0.236 0.778-0.256 1.029-0.302 2.091-0.147 3.121s0.51 2.032 1.056 2.941c0.527 0.875 1.23 1.663 2.1 2.306 0.775 0.573 1.622 0.986 2.5 1.243 0.907 0.266 1.846 0.364 2.772 0.302 0.752-0.050 1.496-0.207 2.21-0.465z"></path>\ '); --icon-moon-filter: invert(0.75); --background: #efefef; --text-color: #141414; --button-icon: var(--icon-moon); --button-icon-filter: var(--icon-moon-filter); --button-background: var(--color-dark); --button-color: var(--color-light); --border-color: var(--color-dark-alpha);} 
```

这里有一大块，所以让我们把它分解一下:

*   我们定义了`--color-mode`变量，默认设置为`'light'`。这用于向 JavaScript 传达用户的偏好是什么
*   我们正在 CSS 中定义一些 SVG 图标(我知道，很酷，对吧？).我可能不会在制作网站上这样做，但这是一个非常酷的技巧，自从我在推特上看到 T2·利亚·维鲁在她的演讲中演示了这一技术，我真的很想试一试

剩下的就是不言自明的主题颜色和灯光主题的默认应用。

### 处理`prefers-color-scheme`媒体查询

我们现在已经有了所有的基础代码，所以让我们来处理用户对`prefers-color-scheme`的偏好。同时，我们还将处理 JavaScript 控制的首选项。

```
body { background: var(--background); color: var(--text-color); transition: background 500ms ease-in-out, color 200ms ease;}@media (prefers-color-scheme: dark) { :root { --color-mode: 'dark'; } :root:not([data-user-color-scheme]) { --background: var(--color-dark); --text-color: var(--color-light); --button-icon: var(--icon-sun); --button-icon-filter: var(--icon-sun-filter); --button-background: var(--color-light); --button-color: var(--color-dark); --border-color: var(--color-light-alpha); }}[data-user-color-scheme='dark'] { --background: var(--color-dark); --text-color: var(--color-light); --button-icon: var(--icon-sun); --button-icon-filter: var(--icon-sun-filter); --button-background: var(--color-light-alpha); --button-color: var(--color-dark); --border-color: var(--color-light-alpha);} 
```

让我们来看看一些关键点:

*   如果用户更喜欢黑暗模式，在媒体查询中，我们会进行相关的主题更改
*   在同一个媒体查询中，我们还设置了 JavaScript 可以挂接的`--color-mode`
*   注意如果`<html>` ( `:root`)元素没有`data-user-color-scheme`属性，CSS 是如何应用的。这是为了防止默认值覆盖用户首选项
*   在媒体查询下，我们使用这个属性选择器来查找值为`dark`的`data-user-color-scheme`属性。我们将在本教程的后面用 JavaScript 来设置它，但是请注意它是如何做与媒体查询相同的事情的。这可以用 Sass mixin 来重构，但是对于本教程，我们将使用温和的重复

### 添加切换样式

CSS 的最后一点是让切换按钮看起来很酷。我们互换了太阳和月亮的图标，并把它涂成与当前主题相反的颜色。

添加此 CSS:

```
.no-js .user-toggle { display: none;}.user-toggle { margin: 2rem auto 0 auto; padding-top: 2.5rem; max-width: 20rem; border-top: 1px solid var(--border-color); text-align: center;}.toggle-button { display: inline-flex; background: var(--button-background); color: var(--button-color); border: none; font: inherit; font-size: 1.5rem; padding: 0.5rem 1rem; transition: background 500ms ease-in-out, color 200ms ease;}.toggle-button__icon { background: var(--button-icon); width: 1.2em; height: 1.2em; margin: 0 0 0 0.5em; filter: var(--button-icon-filter); transform: translateY(1px); /* Optical adjustment */ transition: filter 500ms ease-in-out;}.toggle-button:focus { outline: 1px solid var(--button-background); outline-offset: 0.5rem;} 
```

大部分都是表示性的 CSS，但是有一些我需要解释:

*   默认情况下，因为`<html>`元素有一个类`no-js`，我们隐藏了`.user-toggle`元素，因为其中的所有内容都需要 JavaScript。这意味着，如果用户没有可用的 JavaScript，如果这是他们的系统偏好，他们仍然会得到黑暗模式——他们只是不能改变它
*   因为我们使用 CSS 来渲染图标，所以我们将使用`filter`来给它们着色。查看[这篇便捷的讲解文章](https://css-tricks.com/the-many-ways-to-change-an-svg-fill-on-hover-and-when-to-use-them/)了解其工作原理

现在，我们已经添加了所有的 CSS，让我们做 JavaScript 位！

### 添加交互

我们已经让我们的[最小可行体验](https://andy-bell.design/wrote/the-power-of-progressive-enhancement/)工作了，所以让我们连接一些 JavaScript 来增强它，并给用户更多的控制。

将以下内容添加到您的 JavaScript 文件(或 CodePen 中的 JS 面板):

```
document.documentElement.classList.remove('no-js'); 
```

我们从`<html>`元素中移除了`no-js`类，因为我们有 JavaScript 可用。您可能已经注意到用户切换控件突然出现了。

让我们建立一些常数。添加以下内容:

```
const STORAGE_KEY = 'user-color-scheme';const COLOR_MODE_KEY = '--color-mode';const modeToggleButton = document.querySelector('.js-mode-toggle');const modeToggleText = document.querySelector('.js-mode-toggle-text');const modeStatusElement = document.querySelector('.js-mode-status'); 
```

两个 shouty 常数如下:

*   `STORAGE_KEY`是我们用来在 [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) 中存储用户偏好的
*   `COLOR_MODE_KEY`是我们用来提取当前 CSS 自定义属性值的，`--color-mode`

本节的其余部分是我们获取相关的 HTML 元素，以备后用。

接下来，让我们添加一个函数来提取`--color-mode`值:

```
const getCSSCustomProp = propKey => { let response = getComputedStyle(document.documentElement).getPropertyValue(propKey); if (response.length) { response = response.replace(/\"/g, '').trim(); } return response;}; 
```

这是我们在[教程](https://andy-bell.design/wrote/get-css-custom-property-value-with-javascript/)中使用的函数的压缩版本，如果你对这个函数的工作原理感兴趣，这本书值得一读。本教程中的最终结果是一个字符串`'light'`或`'dark'`，这取决于媒体查询的情况。

接下来，让我们添加一个函数，该函数应用本地存储中设置的或者传入的用户偏好:

```
const applySetting = passedSetting => { let currentSetting = passedSetting || localStorage.getItem(STORAGE_KEY); if (currentSetting) { document.documentElement.setAttribute('data-user-color-scheme', currentSetting); setButtonLabelAndStatus(currentSetting); } else { setButtonLabelAndStatus(getCSSCustomProp(COLOR_MODE_KEY)); }}; 
```

这个函数有一个任务:它试图加载一个从本地存储传入或传出的设置作为后备——然后，根据设置是否被加载，该函数在必要时应用`data-user-color-scheme`属性。然后，它用另一个函数更新按钮标签和状态文本，我们现在将添加这个函数。

```
const setButtonLabelAndStatus = currentSetting => { modeToggleText.innerText = `Enable ${currentSetting === 'dark' ? 'light' : 'dark'} mode`; modeStatusElement.innerText = `Color mode is now "${currentSetting}"`;}; 
```

如你所见，我们根据当前的颜色模式来设置文本。因为我们将有一个`'dark'`或`'light'`值，要么来自本地存储*或*CSS 属性，我们可以安全地将按钮设置为相反的，并通过状态来宣布当前的颜色模式。

接下来，让我们添加切换颜色模式的代码:

```
const toggleSetting = () => { let currentSetting = localStorage.getItem(STORAGE_KEY); switch (currentSetting) { case null: currentSetting = getCSSCustomProp(COLOR_MODE_KEY) === 'dark' ? 'light' : 'dark'; break; case 'light': currentSetting = 'dark'; break; case 'dark': currentSetting = 'light'; break; } localStorage.setItem(STORAGE_KEY, currentSetting); return currentSetting;}; 
```

这个函数做的第一件事是尝试从本地存储器加载当前设置。然后它通过一个开关运行它得到的东西。如果找到了设置，我们就反转它。如果没有，我们从 CSS 加载，然后反过来。

这个函数做的最后一件事是返回它计算的设置。

我们在本教程中做的最后一件事是向按钮添加一个事件，然后应用默认设置，然后我们就完成了！

```
modeToggleButton.addEventListener('click', evt => { evt.preventDefault(); applySetting(toggleSetting());});applySetting(); 
```

在点击处理程序中，我们将由`toggleSetting()`返回的值直接传递给`applySetting()`。任务完成。我们默认运行`applySetting()`，以确保用户的偏好被应用到页面加载中。

这是最终工作系统的演示:

参见 [CodePen](https://codepen.io) 上安迪·贝尔( [@andybelldesign](https://codepen.io/andybelldesign) )的笔[piccalillissue # 10—用户控制黑暗模式](https://codepen.io/hankchizljaw/pen/163767d9126b8d5559cff3990ba5f826/)。

## 包装完毕

这个小教程让你朝着正确的方向前进，但是要让它在你的网站上加速还有很多工作要做。你需要做的最重要的事情是确保你的亮主题和暗主题都是完全可及的——特别是在色彩对比方面。有许多工具可以用来做这件事。我更喜欢用[来称呼](https://khan.github.io/tota11y/)。

你还可以进一步改进这个系统。几个快速的想法是:

*   可能通过`prefers-color-scheme`媒体查询观察用户暗/亮模式设置的变化，并相应地更新默认状态
*   重构 CSS 主题以避免重复。我个人会针对这种情况提出一些反驳