# 如何修复 Safari 上弹出窗口的滚动

> 原文：<https://dev.to/snowleo208/how-to-fix-popups-scrolling-on-safari-3og6>

今天，我创建了一个奇特的弹出窗口，只是一个普通的窗口，用来打开一个简单注册表单的覆盖图。突然，有人用 iPhone 喊道:“嘿，这在我的 iPhone 上不起作用！”

不工作？我一看他的屏幕，是带 Safari 的 iPhone。很明显，滚动条不仅滚动了弹出框，还滚动了 HTML 正文！

## 情况

我是安卓用户，只能用安卓手机查。桌面版的 IE、Chrome、Firefox 和 Android 版的 Chrome/Firefox 的弹出窗口看起来都不错。

头疼的问题来了，卷轴在 Safari 上不起作用。只有 Safari！当然，您无法在 Windows 或 Android 上重现这种行为。

我的 HTML markdown 是这样的:

```
<body class="no-scroll">
    <div class="overlay hide">
        <div class="popup" id="popup">
            <!-- Lots of contents here -->
        </div>
    </div>
</body> 
```

而我的 CSS 是这样的:

```
.c-overlay {
    position: fixed;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(25, 25, 25, 0.4);
    transition: ease 300ms;
    z-index: 1030;
}

.popup {
    position: relative;
    width: 100%
    height: 100%;
    max-width: 800px;
}

.no-scroll {
    overflow: hidden !important;
} 
```

看起来很正常。最后经过试错，知道这是 Safari 的[独占行为，或者 WebKit 浏览器。](https://bugs.webkit.org/show_bug.cgi?id=153852)

* * *

## 初试:添加 webkit 特定的 css

首先，我尝试将这个添加到`.popup`和 body:

```
overflow-y: scroll;
-webkit-overflow-scrolling: touch; 
```

这不起作用，身体在不停地滚动！但这个 CSS 确实给我在 Safari 中的页面增加了滚动动力。

* * *

## 第二次尝试:在主体上添加标签

一些评论说，他们通过在 body 中添加这个标签来解决这个问题。对我来说没用。身体不停的滚动，滚动，滚动。

```
<body class="no-scroll" ontouchstart=""> 
```

* * *

## 最终答案:正文-卷轴-锁定

我从 [Stackoverflow](https://stackoverflow.com/questions/41594997/ios-10-safari-prevent-scrolling-behind-a-fixed-overlay-and-maintain-scroll-posi) 找到这个包，它似乎有 Safari 的变通办法，可以在 touchstart 和 touchmove 时停止奇怪的行为。

将最小化的 JavaScript 标签粘贴到 HTML 和:

```
const targetElement = document.getElementById("popup"); //only popup can scroll

//put this when popup opens, to stop body scrolling
bodyScrollLock.disableBodyScroll(targetElement);

//put this when close popup and show scrollbar in body
bodyScrollLock.enableBodyScroll(targetElement); 
```

它终于解决了我的问题！大家都很开心。

作为 Android 用户很难在 Safari 上测试，但认识到这个问题是好事。希望这个指南能帮助到有需要的人。:)