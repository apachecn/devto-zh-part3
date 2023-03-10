# 在网络上实现画中画

> 原文：<https://dev.to/ananyaneogi/implement-picture-in-picture-on-the-web-17g8>

我最喜欢在网上做的事情之一是实现和测试新的和即将推出的功能。今天我们要实现画中画。

## 什么是画中画？

根据 [W3C 画中画规范-](https://wicg.github.io/picture-in-picture/)

> 该规范旨在提供 API，以允许网站创建一个总是位于其他窗口顶部的浮动视频窗口，以便用户可以在与其他内容站点或设备上的应用程序交互的同时继续消费媒体。

在 PiP(画中画)模式下，视频包含在一个独立的*迷你*窗口中，该窗口始终位于其他窗口的顶部。即使用户代理不可见，该窗口也保持可见。

* * *

## 如何实现画中画？

HTML -

```
<video id="videoElement" controls="true" src="demo.mp4"></video>

<!-- button will be used to toggle the PiP mode -->
<button id="togglePipButton">Toggle Picture-in-Picture Mode!</button> 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript -

### 1。点击`togglePipButton`按钮元素调用`requestPictureInPicture()`。

`requestPictureInPicture()`回报承诺。
当承诺完成时，浏览器会将视频缩小成一个迷你窗口，用户可以在这个窗口中移动并定位在其他窗口上。

```
let video = document.getElementById('videoElement');
let togglePipButton = document.getElementById('togglePipButton');

togglePipButton.addEventListener('click', async function (event) {
    togglePipButton.disabled = true; //disable toggle button while the event occurs
    try {
        // If there is no element in Picture-in-Picture yet, request for it
        if (video !== document.pictureInPictureElement) {
            await video.requestPictureInPicture();
        }
        // If Picture-in-Picture already exists, exit the mode
        else {
            await document.exitPictureInPicture();
        }

    } catch (error) {
        console.log(`Oh Horror! ${error}`);
    } finally {
        togglePipButton.disabled = false; //enable toggle button after the event
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 2。检查画中画事件更改

```
let video = document.getElementById('videoElement');
video.addEventListener('enterpictureinpicture', function (event) {
    console.log('Entered PiP');
    pipWindow = event.pictureInPictureWindow;
    console.log(`Window size -  \n Width: ${pipWindow.width} \n Height: ${pipWindow.height}`); // get the width and height of PiP window
});

video.addEventListener('leavepictureinpicture', function (event) {
    console.log('Left PiP');
    togglePipButton.disabled = false;
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们甚至可以通过添加一个`resize`事件处理程序，根据画中画窗口大小的变化来更新视频大小。这将有助于根据用户观看视频的窗口提供正确的视频质量。

### 3。始终检查功能支持

```
if ('pictureInPictureEnabled' in document) {
    showPipButton();

    // loadedmetadata event occurs when meta data for the specified audio/video has been loaded.Meta data might consists of: duration, dimensions etc.
    video.addEventListener('loadedmetadata', showPipButton);

    // emptied event is fired when the media has become empty, e.g. media has already been loaded or partially loaded.
    video.addEventListener('emptied', showPipButton);
} else {
    console.log('The Picture-in-Picture Web API is not available.');
    togglePipButton.hidden = true;
}

// Enable/disable toggle button depending on whether PiP availability.
function showPipButton() {
    togglePipButton.disabled = (video.readyState === 0) || !document.pictureInPictureEnabled || video.disablePictureInPicture;
}
// video.readyState === 0 means Video metadata have not been loaded yet 
// !document.pictureInPictureEnabled means if Pip is not available
// video.disablePictureInPicture means disablePictureInPicture attribute is present in the video which will result in requestPictureInPicture() rejected promise. 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！您的 web 应用程序现在可以使用画中画了！

* * *

*奖金！*为此做了一个快速演示，[来看看吧！](https://ananyaneogi.github.io/picture-in-picture-demo/)

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [阿纳亚诺吉](https://github.com/ananyaneogi) / [画中画演示](https://github.com/ananyaneogi/picture-in-picture-demo)

### 上传任何视频，并以画中画模式播放

<article class="markdown-body entry-content container-lg" itemprop="text">

# 画中画演示<g-emoji class="g-emoji" alias="tada" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f389.png">🎉</g-emoji>

从您的计算机添加任何视频，并以画中画模式播放视频！

[立即观看演示！](https://ananyaneogi.github.io/picture-in-picture-demo/)

</article>

[View on GitHub](https://github.com/ananyaneogi/picture-in-picture-demo)

目前该 API 只支持在`HTMLVideoElement`上使用，但它声称将来可以扩展。
检查[can use stats](https://caniuse.com/#feat=picture-in-picture)的浏览器支持。因为这是一个渐进的增强，所以不管支持，你今天可以在你现有的应用程序上使用它！耶！

* * *

## 参考文献

1.  [W3C 画中画规范](https://wicg.github.io/picture-in-picture/)
2.  [打造现代网络媒体体验(Chrome Dev Summit 2018)](https://www.youtube.com/watch?v=iTC3mfe0DwE)