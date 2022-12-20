# 我能向有苹果和 iPhone 的人借点时间吗？Safari 调试问题。

> 原文：<https://dev.to/jackharner/can-i-borrow-some-time-from-someone-with-a-mac-iphone-safari-debugging-question-33o4>

嗨，戴夫，

据我所知，我正在试图解决这个只发生在 iOS Safari 中的奇怪错误。请见附件 Gif:

[![](img/7f09d20cb181cd599766c386cb567f91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TjjHtGrD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thumbs.gfycat.com/EmbarrassedThinAsianpiedstarling-size_restricted.gif)

我没有苹果电脑，所以没有 Safari devTools 我就像在黑暗中拍摄。最奇怪的是，当在横向查看时，菜单像预期的那样工作，但当你翻回到纵向时，它会停留一秒钟，然后再翻回到移动样式。

据我所知，这与`overflow-x: scroll;`有关，但不在苹果电脑上，很难说。

[有问题的导航](https://shoolu.com)

任何建议或帮助将不胜感激！

编辑以澄清:

我的问题是，当你点击一个项目。mainNav-item)在主导航(。mainNav)下拉菜单(。mainNav-subMenu)正在显示，但没有溢出。JS 正在努力将 is-open 类添加到。main nav-子菜单，因为(从 gif 上很难看出)下拉菜单的阴影正在显示。

更奇怪的是，当你从纵向模式切换到横向模式时，这个问题就消失了。主导航-子菜单按预期显示。

我拥有的唯一一个手机特有的菜单 SCSS 是:

```
// MobileStyle mixin to add max-width media query
@include mobileStyles() {

    nav.mainNav {
        width: 100vw;

        .mainNav-list {
            overflow-x: scroll;
            flex-wrap: nowrap;
        }

        .mainNav-item {
            flex: 1 0 auto;
        }
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

当我拿掉 overflow-x: scroll 时，菜单会像预期的那样显示，但是现在让页面比屏幕宽。