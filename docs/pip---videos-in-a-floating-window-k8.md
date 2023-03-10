# PiP -浮动窗口中的视频

> 原文：<https://dev.to/mahmoudelmahdi/pip---videos-in-a-floating-window-k8>

我过去常常在一个浮动的迷你播放器中观看会议、DIY 教程、一篇新的技术评论，甚至是单口喜剧视频，该播放器在其他窗口顶部的角落播放，同时与其他任务进行交互，如编写代码、写文章或阅读 reddit、twitter 或黑客新闻。

据我所知，Chrome 和 Safari 都有非常棒的扩展来处理 YouTube 视频的这种事情。这可以用 JavaScript/Web API 来完成！

## 画中画 Web API

画中画(PiP)是桌面和移动操作系统中常见的平台级功能。画中画(PiP)允许用户在浮动窗口(总是在其他窗口之上)中**观看视频，这样他们就可以在与其他任务交互的同时关注他们正在观看的内容。即使用户代理不可见，该窗口也保持可见。**

该规范旨在允许网站通过向 API 公开以下属性集来启动和控制此行为:

*   当网站进入和退出画中画(浮动)模式时通知网站。
*   允许网站通过视频元素上的用户手势触发画中画。
*   允许网站知道画中画窗口的大小，并在它改变时通知网站。
*   允许网站退出画中画。
*   允许网站检查是否可以触发画中画。

## 用法

检查画中画是否支持和可用:

```
const isPiPAvailable = () => {
    return document.pictureInPictureEnabled || !videoElement.disablePictureInPicture;
}

isPiPAvailable() ? showPiPButton() : hidePiPButton(); 
```

Enter fullscreen mode Exit fullscreen mode

画中画 Web API 可能不被支持，所以我们必须检测这一点以提供渐进式增强。即使它受支持，也可能被用户关闭或被功能策略禁用。幸运的是，您可以使用新的布尔值`document.pictureInPictureEnabled`来确定这一点。

请求或存在画中画:

```
try {
    if (!document.pictureInPictureElement) {
        videoElement.requestPictureInPicture()
    } else {
        document.exitPictureInPicture();
    }
} catch(reason) {
    console.error(reason);
} 
```

Enter fullscreen mode Exit fullscreen mode

*   如果画中画支持为假，抛出一个`NotSupportedError`
*   如果不允许文档使用名为“画中画”的[策略控制功能](https://w3c.github.io/webappsec-feature-policy/#policy-controlled-feature)，抛出`SecurityError`
*   如果 disablePictureInPicture 属性出现在视频中，抛出一个`InvalidStateError`

监控视频画中画变化:

```
// Video entered Picture-in-Picture mode.
video.addEventListener('enterpictureinpicture', (event) => console.log(event))
// Video left Picture-in-Picture mode.
video.addEventListener('leavepictureinpicture', (event) => console.log(event)) 
```

Enter fullscreen mode Exit fullscreen mode

聆听画中画事件，而不是等待更新媒体播放器控件的承诺。视频可以随时进入和退出画中画(例如，用户点击某个浏览器上下文菜单或自动触发画中画)。用户可能已经从不同页面播放了画中画视频。

## MediaStream 视频支持

视频播放媒体流对象(如`getUserMedia()`、`getDisplayMedia()`、`canvas.captureStream()`)在 Chrome 71 中也支持画中画。这意味着你可以显示一个画中画窗口，包含用户的摄像头视频流，显示视频流，甚至是一个画布元素。请注意，视频元素不一定要附加到 DOM 才能进入画中画。

## 画中画演示

[https://www.instagram.com/p/BsJoT9LlU-4/embed/captioned/](https://www.instagram.com/p/BsJoT9LlU-4/embed/captioned/)