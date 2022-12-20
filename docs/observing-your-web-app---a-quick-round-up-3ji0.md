# 观察你的网络应用——快速总结

> 原文：<https://dev.to/ananyaneogi/observing-your-web-app---a-quick-round-up-3ji0>

作为 web 开发人员，我们编写大量代码来检测 web 应用程序中的变化，并根据这些变化进行操作。今天，网络上有很多 API 可以*“观察”*你的网络应用程序的许多类型的变化，并让你基于这些变化执行操作。

这里有一个快速的总结-

## 1。通过事件监听器观察 DOM

使用`addEventListener`检测变化的最经典方法。这可以用来监听本地和自定义事件。

```
window.addEventListener('scroll', e => { ... });  // user scrolls the page.

el.addEventListener('focus', e => { ... });  // el is focused.

img.addEventListener('load', e => { ... });  // img is done loading.

el.addEventListener('custom-event', e => { ... });  // catch custom event fired on el. 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 2。检查 DOM 树修改

[MutationObserver](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver) 接口提供了监视 DOM 树变化的能力。

```
var target = document.querySelector('#div_id');

var observer = new MutationObserver(mutations => {
    mutations.forEach(mutation => {
        console.log(mutation.type);
    });
});

var config = { 
    attributes: true, 
    childList: true, 
    characterData: true 
} 
observer.observe(target, config); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 3。检查元素何时进入视图

[交叉点观察器 API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) 提供了一种简单的方法来观察和注册回调以在页面上的元素进入视图时触发。

```
const images = document.querySelectorAll('.animate');

observer = new IntersectionObserver(entries => {
  entries.forEach(entry => {
    if (entry.intersectionRatio > 0) {
      console.log('in the view');
    } else {
      console.log('out of view');
    }
  });
});

images.forEach(image => {
  observer.observe(image);
}); 
```

Enter fullscreen mode Exit fullscreen mode

使用这个 API，我们可以触发动画，延迟加载图像等。，当它们进入视野时。

* * *

## 4。检查 DOM 中的元素何时调整大小

ResizeObserver 允许元素在大小改变时得到通知。

```
const myObserver = new ResizeObserver(entries => {
  entries.forEach(entry => {
    console.log('width', entry.contentRect.width);
    console.log('height', entry.contentRect.height);
  });
});

const el = document.querySelector('.some-element');
myObserver.observe(el); 
```

Enter fullscreen mode Exit fullscreen mode

对于每个条目，我们得到一个具有属性- `contentRect`和`target`的对象。目标是 DOM 元素本身，contentRect 是具有以下属性的对象:`width`、`height`、`x`、`y`、`top`、`bottom`、`left`、`right`。

* * *

## 5。检查您的 web 应用程序是否处于全屏模式

使用[全屏 API](https://developer.mozilla.org/en-US/docs/Web/API/Fullscreen_API) 使这成为可能。

```
var el = document.getElementById("myvideo");
if (el.requestFullscreen) {
  el.requestFullscreen();
} 
```

Enter fullscreen mode Exit fullscreen mode

不仅仅是检测全屏模式，这个 API 还允许关闭和打开全屏模式。

* * *

## 6。检查选项卡是否在焦点上

```
document.addEventListener('visibilitychange', e => {
    if(document.hidden) {
        // .... do something
    } else {
        // .... dome something else
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

即使这不是一个新的 API，而是一种技术，我仍然把它包括在列表中。一个用例是将用户的注意力吸引回被放弃的选项卡。

* * *

## 7。检查设备方向的变化

[屏幕方向 API](https://www.w3.org/TR/screen-orientation/) 使这成为可能。

```
 window.addEventListener('orientationchange', e => {
  console.log(screen.orientation.angle, screen.orientatio.type)
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以检查当前方向是否与特定方向匹配。

```
const media = window.matchMedia('(orientation: portrait)');
media.addListener(mql => console.log(mql.matches)); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 8。检查网络状态和变化

[网络信息 API](https://developer.mozilla.org/en-US/docs/Web/API/Network_Information_API) 提供关于用户网络连接的信息。利用这些信息，有条件地加载图像、视频、字体和其他资源是可能的。

```
 navigator.connection.addEventListener('change', e => {
  console.log(navigator.connection.effectiveType, navigator.connection.downlink);
}); 
```

Enter fullscreen mode Exit fullscreen mode

除了上面提到的`effectiveType`和`downlink`属性外，还提供了`downlinkMax`、`rtt`、`saveData`、`type`。

* * *

## 9。检查设备电池的状态

这可以通过使用[电池状态 API 来实现。](https://www.w3.org/TR/battery-status/)

```
 navigator.getBattery().then(battery => {
  console.log(
    battery.level * 100 + '%',
    battery.charging,
    battery.chargingTime,
    battery.dischargingTime
    ); 
}); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 10。检查你的网络应用程序何时使用废弃的 API 或点击[浏览器干预](https://github.com/WICG/interventions)

[报告 API](https://w3c.github.io/reporting/) 使得
成为可能

```
const observer = new ReportingObserver((reports, observer) => {
  reports.map(report => {
    console.log(report);
    // ..... send report to analytics etc..
  });
}, {buffered: true});

observer.observe(); 
```

Enter fullscreen mode Exit fullscreen mode

我相信，目前,`ReportingObserver`只在最新的 Chrome 中发货。

* * *

## 11。检查您的 web 应用程序的性能

[PerformanceObserver](https://developers.google.com/web/updates/2016/06/performance-observer) 接口用于观察性能测量事件，并在浏览器的性能时间线中记录新的性能条目时得到通知。

```
const observer = new window.PerformanceObserver(list => {
    list.getEntries().forEach(({name, startTime}) => {
        console.log({name, startTime});
    });
});
observer.observe({
    entryTypes: [{'paint', 'resource', 'mark', 'measure'}]
}); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

web 上有这么多优秀的 API 供我们使用，这真是令人惊讶。使用这些，我们可以创建具有更好性能和更好用户体验的 web 应用程序。

**注意**:在使用现代 API 之前，请务必检查浏览器支持。

* * *

## 有用的资源

1.  [观看 2018 年最佳网页特写对决](https://www.youtube.com/watch?v=adgI8-W1VjE)
2.  [使用交叉点观察器惰性加载图像以提高性能](https://scotch.io/tutorials/lazy-loading-images-for-performance-using-intersection-observer)
3.  [利用网络信息 API 改善响应网站](https://www.sitepoint.com/use-network-information-api-improve-responsive-websites/)
4.  [调整观察者样本的大小](https://googlechrome.github.io/samples/resizeobserver/)
5.  [性能观察器和绘画计时 API](https://jmperezperez.com/paint-timing-api/)