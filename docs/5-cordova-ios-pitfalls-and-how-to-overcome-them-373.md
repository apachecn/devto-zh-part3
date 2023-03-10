# 5 科尔多瓦 iOS 陷阱-以及如何克服它们

> 原文：<https://dev.to/johnnymakestuff/5-cordova-ios-pitfalls-and-how-to-overcome-them-373>

> 这是我的[博客](https://www.learningsomethingnew.com/5-cordova-i-os-pitfalls-and-how-to-overcome-them)的一篇交叉文章

我最近用 VueJS + Cordova 重新编写了我们的用于教授 GOT 语言多斯拉克语的[原生 android 应用程序](https://play.google.com/store/apps/details?id=com.cognitusapps.flamingo.dothraki)，并将其发布在[应用程序商店](https://itunes.apple.com/us/app/flamingo-dothraki/id1460164128)
(希望得到反馈！)

我遇到了一些 iOS 特有的怪癖，任何制作 cordova-ios 应用程序的人都可能不得不面对。

## 1。快速点击

在 iOS 中使用的 WebView cordova 上，每次点击事件都有 300 毫秒的延迟。如果听之任之，这将是一次非常令人讨厌的经历。这个问题的解决方法很简单:

```
cordova plugin add cordova-plugin-fastclick 
```

> 请注意，在 fastclick 的自述文件中有一条注释，称从 2015 年起不再需要它。好吧，这是 2019 年，现在依然如此...

不带快速点击:
[https://www.youtube.com/embed/6AfxsVET0V8](https://www.youtube.com/embed/6AfxsVET0V8)

带快捷键:
[https://www.youtube.com/embed/jX8J3tcit9E](https://www.youtube.com/embed/jX8J3tcit9E)

## 2。过度滚动

在 iOS 浏览器中，当你滚动的内容超过页面内容时，就会出现这种反弹效应。在网页上看起来还可以，但是在你的 SPA 中，你不希望你的内容被白色背景弹出来(见下面的例子)。

这就尴尬了:
[https://www.youtube.com/embed/fU8ggNYmKGY](https://www.youtube.com/embed/fU8ggNYmKGY)

幸运的是，这也是一个非常简单的修复，只需将以下内容添加到 config.xml 中(为什么这不是默认设置？？)

```
<platform name="ios">
    <preference name="DisallowOverscroll" value="true" />
</platform> 
```

## 3。滚动加速度

在 iOS 中，原生滚动具有平滑的加速和减速行为。如果你的应用程序中有一个列表，你会希望滚动的行为是一样的，而不是像默认的自动溢出那样平滑。

坏:

```
{
  overflow-y: auto;
} 
```

好:

```
{
  overflow-y: scroll;
  -webkit-overflow-scrolling: touch; 
} 
```

## 4。将 GPU 用于动画

ios 中的 cordova WebView 无法流畅地处理某些 css 过渡。这将导致您的应用程序具有糟糕的感知性能。
因此，如果你想从左向右滑动某些东西，动画制作`transform` css 属性而不是`left`将使用设备 GPU，并产生平滑的类似本机的动画。

```
.slide-fade-enter-active {
  transition: all .3s ease;
}
.slide-fade-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-to
/* Notice the use of translateX instead of left/right */ {
  transform: translateX(10px);
  opacity: 0;
} 
```

> 来自 [VueJS 文档](https://vuejs.org/v2/guide/transitions.html) :
> 的例子如果你使用像 Vuetify 这样的 UI 框架，动画已经使用了，所以你被覆盖了

## 5。状态栏颜色:

默认情况下，状态栏保持白色，这通常会看起来很奇怪，除非你的应用程序也主要是白色的。所以要改一下:

```
cordova plugin add cordova-plugin-statusbar 
```

并在 config.xml 中添加 ios 平台下:(替换成你 app 的主色)

```
<preference name="StatusBarOverlaysWebView" value="false" />
<preference name="StatusBarBackgroundColor" value="#353334" /> 
```

## 就是这样

现在你已经为应用商店做好了准备，没有人会知道你的应用是在浏览器中运行的。我希望如此。

Dothras check!