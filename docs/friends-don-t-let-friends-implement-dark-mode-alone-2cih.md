# 朋友不让朋友单独实现黑暗模式

> 原文：<https://dev.to/huijing/friends-don-t-let-friends-implement-dark-mode-alone-2cih>

我有一个朋友，魏，他基本上是 CSS 混合模式的专家，对吗？她想出了一个非常有趣的方法，用混合模式在她的网站上实现黑暗模式。然后[写了一下](https://dev.to/wgao19/night-mode-with-mix-blend-mode-difference-23lm)，因为分享就是关爱。

碰巧的是，我的网站的绿色主题用她描述的技术混合成了紫色，所以我想是时候给我的网站添加一个实验性的特性了。我还想强调的是，她在自己的网站上确实做得好 1000 倍。

如果你使用 React，你很幸运，因为她把它作为插件[发布了](https://github.com/wgao19/sun-moon-toggle)你也可以安装和添加一些混合模式的优点到你的网站上。但我显然不是一个用 React 的酷孩子。所以如果你在做类似的事情，也许不要做我做过的事？

## 它的要旨

这个特殊的技术包括在你的站点上覆盖一个`div`，它占据了整个视窗，与你的主背景颜色相同，还有一个`difference`的`mix-blend-mode`。那是一个可怕的句子，对不起。

混合模式通过“混合”公式将源元素的颜色与其背后的内容混合。每个不同的混合模式值实质上是根据不同的公式组合 2 种颜色。

[![Simplified diagram of how difference works for blend modes](img/a9dc0eb7a351fc11e6ab7bfc88fe6741.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NbRxzVVR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/blend-dark-mode/difference-640.png)

对于数学背后的直觉的深入解释，你应该[观看魏在](https://www.youtube.com/watch?v=iUoon3GneRA)的【T2 访谈】中谈论它。CSS #38 。我只是那个偷了她朋友代码的朋友，或者至少偷了一部分。

首先，我们需要一个`div`来让你的网站与之融合。这个`div`必须放在所有东西的上面，所以把它放在你的`body`元素里面以获得最佳效果。要让它覆盖整个视口，你可以这样做:

```
.blender {
  position: fixed;
  height: 100vh;
  width: 100vw;
  background-color: color('sugarcane');
  mix-blend-mode: difference;
  pointer-events: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

你应该看到你的网站颜色神奇地在你眼前反转。

[![Screenshot of Hui Jing's website comparing light mode on the left with dark mode on the right](img/261c350231ef31a09fe39b78ff952b99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yPniB1IT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/blend-dark-mode/light-dark-640.png)

## 但是等等，还有更多…

但这不是我想要的。至少，现在还没有。我们接下来需要的是一个开关。如果你没有提示，我的网站有点像《我的世界》，那么为什么不用《我的世界》的太阳和月亮呢？人们要么能看出来，要么看不出来。这两种方式都不重要。

我的开关实际上是一个带有加强标签的复选框。因为我将复选框用作状态跟踪器。你以后会明白的。理论上，它可以放在任何地方，因为它将被显式定位，但是我把我的放在全屏`div`的正下方。

```
<input type="checkbox" class="blend-checkbox" id="blendToggle">
<label for="blendToggle" class="blend-toggle"></label> 
```

Enter fullscreen mode Exit fullscreen mode

这将是一个圆形的东西，人们可以点击《我的世界》太阳作为背景图像，所以应用如下样式:

```
.blend-toggle {
  position: fixed;
  height: 3rem;
  width: 3rem;
  left: 1rem;
  bottom: 1rem;
  border-radius: 50%;
  background-image : url('/asseimg/sun.png');
  background-position: center;
  background-size: cover;
  z-index: 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

## …还有更多…

现在，我们可以用全屏`div`切换复选框，然后在复选框被选中时使用同级选择器激活`div`,不用 Javascript 就能处理这一切，但结果是为了保持页面之间的状态，即使是最简单的方法也需要 Javascript 的帮助。

这里我最常用的方法是使用`localStorage`来确定用户是否切换了黑暗模式。但是我仍然使用复选框状态来跟踪需要应用什么 CSS 类来使`div`成为“活动的”。

```
const blendCheckbox = document.getElementById('blendToggle')
const blender = document.getElementById('blender')
blendCheckbox.addEventListener('click', toggleBlend, false)

function toggleBlend(e) {
  if (e.target.checked) {
    localStorage.checked = true
    blender.classList.add('active')
  } else {
    localStorage.checked = ''
    blender.classList.remove('active')
  }
}

(function() {
  blendCheckbox.checked = localStorage.checked
  if (localStorage.checked) {
    blender.classList.add('active')
  } else {
    blender.classList.remove('active')
  }
})() 
```

Enter fullscreen mode Exit fullscreen mode

所以，当在桌面上看魏的网站时，它的全屏`div`从切换按钮后面看起来有点酷，所以我当然要偷那个。但是我只是基于视口宽度，所以任何大于`960px`的都会得到它，下面的所有都会得到淡入/淡出效果。

```
.blender {
  position: fixed;
  background-color: color('sugarcane');
  mix-blend-mode: difference;
  pointer-events: none;
}

@media screen and (max-width: 959px) {
  .blender {
    opacity: 0;
    height: 100vh;
    width: 100vw;
    transition: opacity 0.5s ease;
  }
}

@media screen and (min-width: 960px) {
  .blender {
    height: 3rem;
    width: 3rem;
    left: 1rem;
    bottom: 1rem;
    border-radius: 50%;
    left: calc(50% - 24rem);
    transition: transform 0.7s ease-out;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当切换被点击时，如果复选框被选中，那么一个`active`类被应用到全屏`div`。

```
@media screen and (max-width: 959px) {
  .blender.active {
    opacity: 1;
  }
}

@media screen and (min-width: 960px) {
  .blender.active {
    transform: scale(100);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## …永远都有更多

网站上的大部分内容可以安全地反转，但有些东西，比如图像或表情符号，最好保持原样，所以就有了`isolation`属性。将它设置为值`isolate`会将元素转换为堆叠上下文，不将其混合。

```
.blender.active ~ .blend-toggle {
  background-image : url('/asseimg/moon.png');
  isolation: isolate;
}

img,
.external-url::before,
.emoji {
  isolation: isolate;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还希望我们的切换可以通过键盘访问，所以添加一个`:focus`状态，并按照您喜欢的方式对其进行样式化:

```
.blend-checkbox:focus ~ label {
  outline: 5px auto -webkit-focus-ring-color;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要考虑所有不支持 CSS 混合模式的浏览器，比如 Chromium Edge 浏览器。

因此，将所有与混合模式相关的内容都包含在一个特性查询中，这些浏览器的用户不会知道。这是实验性的，所以他们没有遗漏任何东西。

```
@supports (mix-blend-mode: difference) {
  /* All the blender, toggle and whatever goes here */
} 
```

Enter fullscreen mode Exit fullscreen mode

## 为什么我的实现并不伟大

如果你在我的网站上尝试了这个实验性的特性，你会注意到在页面之间，由于 Javascript 检查`localStorage`应用相关 CSS 类的延迟，在黑暗模式之间有一个闪光。

这非常不理想。魏的网站很顺利，因为 React 正在处理引擎盖下的事情(至少我认为是这个原因，但我知道什么)。我的观点是，这在我的网站上行得通，但它肯定不漂亮。

不管怎样，实现起来很有趣，我会把它放在那里，只是为了惹恼那些不屑于页面之间闪烁光亮的人，如果他们碰巧切换到黑暗模式的话。因为我不是一个很好的人。

## 包装完毕

最近，Firefox 67 开始支持`prefers-color-scheme`，允许网站采用自己的风格来匹配用户对浅色或深色方案的偏好。Safari 也支持这一功能，Chrome 将于今年晚些时候推出。

这可能是黑暗模式向前发展的方式，但这个实验不是关于黑暗模式本身，而是关于混合模式。算是吧。

所以，是的，这是你的网站和内容。做你喜欢的事。

因为你值得。