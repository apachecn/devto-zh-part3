# 最小 Javascript 的汉堡菜单

> 原文：<https://dev.to/jacobmparis/hamburger-menu-in-minimal-javascript-12an>

几分钟前，我发布了一个用纯 CSS 构建汉堡菜单的教程。
[https://dev.to/jacobmparis/hamburger-menu-in-pure-css-mga](https://dev.to/jacobmparis/hamburger-menu-in-pure-css-mga)

虽然这是一个有趣的实验，而且在实现之后还有很多东西要学，但事实是，在任何生产环境中，你都已经在网站的很大一部分中使用 javascript 了。

在这种环境下，通过用 javascript 驱动菜单动画，而不是依赖复选框和 CSS 同级选择器，您可以获得更干净的代码。

[https://codepen.io/anon/pen/YbKjxo](https://codepen.io/anon/pen/YbKjxo)

```
<button id="sidebar__trigger" class="sidebar__button">CLOSE</button>

<ul id="sidebar" class="sidebar">
    <li>Home Page</li>
    <li>Example 1</li>
    <li>Example 2</li>
    <li>Example 3</li>
    <ul>
        <li>Example 1</li>
        <li>Example 2</li>
        <li>Example 3</li>
    </ul>
    <li>Example 1</li>
    <li>Example 2</li>
    <li>Example 3</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个带 ID 的基本按钮，这样我们可以在 javascript 中引用它，出于同样的原因，我们还需要一个带 ID 的侧边栏。

```
.sidebar {
  background: #333;
  color: white;
  max-width: 200px;
  transition: transform 0.5s;

  &.isClosed {
    transform: translateX(-100%);
  }

  &__button {
    width: 300px;
    border: 1px solid #ddf;
    padding: 1rem;
    border-radius: 0.25rem;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的风格都是在 SCSS 写的，所以我们可以利用嵌套。这节省了大量重复的代码，但要求我们在将 CSS 提供给浏览器之前对其进行预编译。

`&`指的是父选择器，所以一旦它编译出了`.isClosed`类，它将看起来像这样:

```
.sidebar.isClosed {
  transform: translateX(-100%);
} 
```

Enter fullscreen mode Exit fullscreen mode

这个类将侧边栏向左平移它的宽度。添加类，侧栏隐藏。移除类，侧栏出现。简单！

```
const sidebar = document.getElementById("sidebar");
const sidebarTrigger = document.getElementById("sidebar__trigger"); 
```

Enter fullscreen mode Exit fullscreen mode

我们的第一步是选择这两个元素，这样我们就可以在 Javascript 中使用它们。

```
sidebarTrigger.addEventListener('click', () => {
    if(sidebar.classList.contains('isClosed')) {
        sidebar.classList.remove('isClosed');
        sidebarTrigger.innerText = "CLOSE";
    } else {
        sidebar.classList.add('isClosed');
        sidebarTrigger.innerText = "OPEN";
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里我们为`click`事件添加了一个事件监听器。每次我们点击`sidebarTrigger`，我们在这里指定的功能就会运行。

如果侧边栏有`.isClosed`类，移除它并将按钮文本改为`CLOSE`。否则，添加它并将按钮文本更改为`OPEN`。

就是这样！CSS 处理所有的动画，javascript 要做的就是切换一个类。

[https://codepen.io/anon/pen/YbKjxo](https://codepen.io/anon/pen/YbKjxo)