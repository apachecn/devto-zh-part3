# 了解 iOS 中的 AVFoundation 和 MediaPlayer 框架

> 原文：<https://dev.to/onmyway133/understanding-avfoundation-and-mediaplayer-frameworks-in-ios-1e8h>

根据我们想要实现的功能，我们需要使用 AVFoundation 或 MediaPlayer 框架。作为一个使用过许多涉及媒体播放的应用程序的人，以下是我的一些观察

## MP movie player controller vs av player

一开始我用 MPMoviePlayerController 是因为它很简单，其实就是 AVPlayer 的一个包装器。它提供了许多有用的通知和属性。

但是当我的要求改变时，需要更多的功能，我需要换成 AVPlayer 来获得更多的控制。这是我做过的最明智的决定

你应该尽早使用 AVPlayer。使用它比 MPMoviePlayerController 花费的时间多一点，但是你有很多控制。

## 自定义控件

当构建您自己的播放器时，MPMoviePlayerController 的内置控件可能无法满足您的需要。我在 SO 上看到许多关于自定义控件的问题。

## mpmovieplaycontroller

您必须将 controlStyle 设置为 MPMovieControlStyleNone，并设置计时器，因为 currentPlaybackTime 不符合 KVO 标准

## AVPlayer

AVPlayer 没有内置控件，但它有 addPeriodicTimeObserverForInterval:queue:using block:这使得处理当前时间很容易。periodTimeObserver 更好的一点是“每当时间跳跃和播放开始或停止时，也会调用这个块”

## 通知

### mpmovieplaycontroller

它有很多有用的通知，像 MPMoviePlayerNowPlayingMovieDidChangeNotification，mpmovieplayloadstatedidchangenotification，mpmoviedurationnavailablenotification，...

### AVPlayer

AVPlayerItem 具有一些通知 AVPlayerItemDidPlayToEndTimeNotification、AVPlayerItemPlaybackStalledNotification、...如果你想拥有和 MPMoviePlayerController 一样的通知，你必须 KVO av player 的一些属性，比如 currentItem，currentTime，duration，...你必须阅读文件，以确保哪个属性是 KVO 合规的

## 寻找

### mpmovieplaycontroller

您可以将 currentPlaybackTime 更改为 seek，但由于效率和缓冲状态的原因，这会导致跳转。在搜寻过程中，我必须手动禁用滑块

### AVPlayer

AVPlayer 有这个**seek totime:tolerance before:tolerance after:completion handler:**允许您指定容差。很好

## 字幕

### mpmovieplaycontroller

我必须安排一个计时器，分别在某个字幕标记的开始和结束时间“唤醒”和“睡眠”。

### AVPlayer

AVPlayer 有这个**addBoundaryTimeObserverForTimes:queue:using block:**非常适合我的需求。我设置了两个边界时间观察器，一个用于开始时间，一个用于结束时间。开始时间是包含字幕标记出现的所有开始时间戳的数组。

## 时间刻度

AVPlayer 使用提供时间刻度的 CMTime，这是指定更准确时间的一种更舒适的方式

## 卷

AVPlayer 有**音量**属性(相对于系统音量),允许我以编程方式改变播放器音量

## 可玩时长

### mpmovieplaycontroller

它具有 playableDuration 属性

### AVPlayer

你得自己计算。参见[http://stack overflow . com/questions/6815316/how-can-I-get-the-playable-duration-of-avplayer](http://stackoverflow.com/a/12260669/1418457)

## 全屏

MPMoviePlayerController 通过创建另一个 UIWindow 来实现全屏模式，您可以从中学习如何使用 AVPlayer 来支持全屏模式

## 电影来源类型

### mpmovieplaycontroller

它有 movieSourceType，可以为播放系统提供线索，从而缩短加载时间

如果您知道电影的源类型，在播放开始前设置此属性的值可以缩短电影内容的加载时间

### AVPlayer

阅读 [AV 基金会编程指南](https://developer.apple.com/library/ios/documentation/AudioVideo/Conceptual/AVFoundationPG/Articles/02_Playback.html)上的**处理不同类型的资产**

创建并准备用于回放的 HTTP 实时流。使用 URL 初始化 AVPlayerItem 的实例。(您不能直接创建 AVAsset 实例来表示 HTTP 实时流中的媒体。)"

## AVAsset

AVPlayer 允许您访问 AVAsset，它为您提供了有关播放和加载状态的更多信息

## 允许视频中的一个范围可播放

AVPlayerItem 有 **forwardPlaybackEndTime** 和 **reversePlaybackEndTime** ，用来指定玩家可以玩的范围。当指定 forwardPlaybackEndTime 且播放头经过此点时，AVPlayerItem 将触发 AVPlayerItemDidPlayToEndTimeNotification

但是在我的情况下不起作用 [forwardPlaybackEndTime 不起作用](http://stackoverflow.com/questions/25717819/forwardplaybackendtime-does-not-work)

## 您可以同时运行多个 AVPlayer 实例

### 阅读更多

1.  [AV 基础编程指南](https://developer.apple.com/library/mac/documentation/AudioVideo/Conceptual/AVFoundationPG/Articles/02_Playback.html)
2.  [WWDC 2011 年第 405 届探索 AV 基金会](https://developer.apple.com/videos/wwdc/2011/)
3.  [WWDC 2011 年第 415 届会议与 AV 基金会的媒体合作](https://developer.apple.com/videos/wwdc/2011/)
4.  [WWDC 2014 会议 503 掌握现代媒体回放](https://developer.apple.com/videos/wwdc/2014/)
5.  [WWDC 2011 年第 406 届会议 iOS 应用中的 AirPlay 和外部显示器](https://developer.apple.com/videos/wwdc/2011/)
6.  [AVPlayerDemo](https://developer.apple.com/library/ios/samplecode/AVPlayerDemo/Introduction/Intro.html)
7.  [VKVideoPlayer](https://github.com/viki-org/VKVideoPlayer)
8.  [电影播放控制器](https://github.com/alobi/ALMoviePlayerController)
9.  [AV 基金会播放视频](http://mobiliture.com/ios/tutorials/av-foundation-playing-videos/)
10.  [AVPlayer 和 MPMoviePlayerController 的区别](http://stackoverflow.com/questions/8146942/avplayer-and-mpmovieplayercontroller-differences)
11.  [MPMoviePlayer 提示](http://jomnius.blogspot.com/2011/04/mpmovieplayer-tips.html)

原帖[https://github.com/onmyway133/blog/issues/210](https://github.com/onmyway133/blog/issues/210)