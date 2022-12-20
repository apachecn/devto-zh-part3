# 字体行业不想让你知道的秘密

> 原文：<https://dev.to/xowap/the-secret-that-the-fonts-industry-doesn-t-want-you-to-know-3efp>

最近我最喜欢的一个技巧是用`currentColor`作为几乎任何东西的调味品。很长一段时间都不为人知，但我很确定 [Lea Verou](http://lea.verou.me/) 是第一个使用并向世界展示它的人。

而 CSS 变量[他妈的牛逼](https://www.youtube.com/watch?v=UQRSaG1hQ20)，是 1。还不总是可能使用它们(哦*甜* IE 11)和 2。并不总是必要的。

我们来举个例子。还记得为什么每个人都喜欢图标字体，因为它们会自动获得字体的颜色吗？事实证明，您可以用 SVG 做同样的事情，并且有更好的控制。

假设你的代码是这样的:

```
<nav class="menu">
    <a class="item">
        <path d="..."/>
        JavaScript
    </a>
    <a class="item">
        <path d="..."/>
        Python
    </a>
    <a class="item">
        <path d="..."/>
        Sass
    </a>
</nav> 
```

Enter fullscreen mode Exit fullscreen mode

您希望文本前面的 SVG 图标随着文本的颜色而变化。你是做什么的？

```
<path d="..."/> 
```

Enter fullscreen mode Exit fullscreen mode

变成了

```
<path fill="currentColor" d="..."/> 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，你的 SVG 无论如何都有文本的颜色。这里有一个活生生的例子，不要忘记悬停按钮！

[https://jsfiddle.net/xowap/s8zxq5f1/27//embedded/result//dark](https://jsfiddle.net/xowap/s8zxq5f1/27//embedded/result//dark)

你有没有注意到 Python 的 logo 在悬停时没有完全变色？您不必将`currentColor`放入 SVG 中的每条路径。比使用字体自由多了！

这是一个简单的技巧，但我最近真的用了很多。你呢，用`currentColor`你能想到什么模式？