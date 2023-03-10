# 用 CSS 变量创建一个暗/亮模式开关

> 原文：<https://dev.to/ananyaneogi/create-a-dark-light-mode-switch-with-css-variables-34l8>

给你的用户一种定制界面的方式是用户体验的巨大胜利。在这里，我们将为用户提供一个简单的开关来切换黑暗模式和光明模式，我们还将尝试记住他们的偏好，为未来的访问。

让我们开始吧！

如果您还没有自己的网站来添加这项功能，请使用这个演示网站来跟进。

[https://codepen.io/ananyaneogi/embed/LvWaZG?height=600&default-tab=html,result&embed-version=2](https://codepen.io/ananyaneogi/embed/LvWaZG?height=600&default-tab=html,result&embed-version=2)

# 添加 CSS

我们将添加 CSS 自定义属性，也称为 CSS 变量，我们可以在整个文档中引用和修改它们。如果您想了解更多关于自定义属性的信息，您可以在 MDN 上阅读。
这里是 tldr 版本-

> 自定义属性(有时称为 CSS 变量或级联变量)是由 CSS 作者定义的实体，包含将在整个文档中重用的特定值。它们是使用自定义属性符号设置的(例如-main-color:black；)并使用 var()函数访问(例如，color:var(-main-color)；)

首先，我们将把我们的轻量或默认模式 css 变量添加到`:root`伪类中。它与文档树中的根元素匹配，通常是`<html>`标签。我们将使用`:root`,因为我们想在全球范围内利用这些变量。

```
:root {
    --primary-color: #302AE6;
    --secondary-color: #536390;
    --font-color: #424242;
    --bg-color: #fff;
    --heading-color: #292922;
} 
```

Enter fullscreen mode Exit fullscreen mode

其次，我们将添加我们的黑暗模式 css 变量。

```
[data-theme="dark"] {
    --primary-color: #9A97F3;
    --secondary-color: #818cab;
    --font-color: #e1e1ff;
    --bg-color: #161625;
    --heading-color: #818cab;
} 
```

Enter fullscreen mode Exit fullscreen mode

什么是`[data-theme="dark"]`？这意味着我们引用了一个名为`data-theme`的`data`属性，其值为“黑暗”。过一会儿我们会发现它的用处。

然后，我们可以在样式表中引用这些变量，比如 so-

```
body {
    background-color: var(--bg-color);
    color: var(--font-color);

    /*other styles*/
    .....
}

h1 {
    color: var(--secondary-color);

    /*other styles*/
    .....
}

a {
    color: var(--primary-color);

    /*other styles*/
    .....
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

* * *

# 添加 HTML“切换切换标记”

这本质上只是一个复选框，但是我们将添加一些额外的标记，像一个切换开关。我参考了这支笔的样式。

```
<div class="theme-switch-wrapper">
    <label class="theme-switch" for="checkbox">
        <input type="checkbox" id="checkbox" />
        <div class="slider round"></div>
  </label>
  <em>Enable Dark Mode!</em>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
/*Simple css to style it like a toggle switch*/
.theme-switch-wrapper {
  display: flex;
  align-items: center;

  em {
    margin-left: 10px;
    font-size: 1rem;
  }
}
.theme-switch {
  display: inline-block;
  height: 34px;
  position: relative;
  width: 60px;
}

.theme-switch input {
  display:none;
}

.slider {
  background-color: #ccc;
  bottom: 0;
  cursor: pointer;
  left: 0;
  position: absolute;
  right: 0;
  top: 0;
  transition: .4s;
}

.slider:before {
  background-color: #fff;
  bottom: 4px;
  content: "";
  height: 26px;
  left: 4px;
  position: absolute;
  transition: .4s;
  width: 26px;
}

input:checked + .slider {
  background-color: #66bb6a;
}

input:checked + .slider:before {
  transform: translateX(26px);
}

.slider.round {
  border-radius: 34px;
}

.slider.round:before {
  border-radius: 50%;
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

* * *

# 添加 JavaScript

最后一部分是添加一点 JavaScript 来将它们结合在一起。我们手头有三项任务-

*   添加事件处理程序来相应地处理 toggle-switch 的选中/取消选中事件
*   存储用户偏好以备将来访问
*   在网站加载时检查保存的用户偏好(如果有)

#### 添加事件处理程序

```
 const toggleSwitch = document.querySelector('.theme-switch input[type="checkbox"]');

function switchTheme(e) {
    if (e.target.checked) {
        document.documentElement.setAttribute('data-theme', 'dark');
    }
    else {
        document.documentElement.setAttribute('data-theme', 'light');
    }    
}

toggleSwitch.addEventListener('change', switchTheme, false); 
```

Enter fullscreen mode Exit fullscreen mode

记住，我们在上面的 CSS 中引用的`data-theme`属性，这是它被添加到我们的根元素的地方。

* * *

#### 存储用户偏好以备将来访问

我们将使用浏览器的本地存储来存储用户偏好。
让我们修改我们的`switchTheme`函数-

```
function switchTheme(e) {
    if (e.target.checked) {
        document.documentElement.setAttribute('data-theme', 'dark');
        localStorage.setItem('theme', 'dark'); //add this
    }
    else {
        document.documentElement.setAttribute('data-theme', 'light');
        localStorage.setItem('theme', 'light'); //add this
    }    
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 在网站加载时检查保存的用户偏好(如果有)

我们将检查主题偏好是否已保存，如果是，那么我们将相应地-
-设置我们的`data-theme`属性
-选中/取消选中我们的切换开关复选框

```
 const currentTheme = localStorage.getItem('theme') ? localStorage.getItem('theme') : null;

if (currentTheme) {
    document.documentElement.setAttribute('data-theme', currentTheme);

    if (currentTheme === 'dark') {
        toggleSwitch.checked = true;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！查看下面的完整演示。
[https://codepen.io/ananyaneogi/embed/zXZyMP?height=600&default-tab=result&embed-version=2](https://codepen.io/ananyaneogi/embed/zXZyMP?height=600&default-tab=result&embed-version=2)
我最近把这个加到了[我的网站](https://ananyaneogi.com)，所以也来看看吧，*也许！*

* * *

* * *

**亲提示:如何决定配色方案？**
我的建议是保持你的原色或品牌色的光谱不变，并从中生成一个调色板。在黑暗模式下，使用调色板中最暗的颜色作为背景色，较亮的颜色作为字体颜色。我用[这个色调生成器](https://javisperez.github.io/tailwindcolorshades/#/)来生成我的调色板。
[![](img/7b9fd70e23052f1618dbc68065d03f3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YW7VLWlt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o9foe9ifszb9cfw8gxr4.png) 
即使你最终没有使用生成的确切颜色，这仍然是一个很好的开始。甚至我最终微调了我最终使用的颜色。

* * *

# 资源

1.  [5 分钟学会 CSS 变量](https://medium.freecodecamp.org/learn-css-variables-in-5-minutes-80cf63b4025d)