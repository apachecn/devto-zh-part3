# 了解 iOS 中的通知中心

> 原文：<https://dev.to/onmyway133/understanding-notificationcenter-in-ios-2gal>

`NSNotificationCenter`就像一个中心枢纽，应用程序的任何部分都可以在这里发布和接收通知

## 正在登记

注册 n 次通知，你会收到 n 次通知

## 调试说明

截至 iOS 9+，

> NSNotificationCenter 和 NSDistributedNotificationCenter 现在将在从调试器打印时提供调试描述，该描述将列出所有已注册的观察器，包括为帮助调试通知注册而被置零的引用。该数据仅在断点持续期间有效，因为基础存储区必须考虑多线程环境。对于传递给 addObserver 方法族的名称或对象为 null 的通知，通配符注册将在调试描述中显示为*。

```
print(NSNotificationCenter.defaultCenter().debugDescription) 
```

## 观察者

### 添加观察器(_:选择器:名称:对象:)

> 在接收方的调度表中添加一个条目，包括一个观察者、一个通知选择器和可选标准:通知名称和发送方。

```
override func viewDidLoad() {
    super.viewDidLoad()

    NSNotificationCenter.defaultCenter().addObserver(self, selector: "playerDidReachEndNotificationHandler:", name: AVPlayerItemDidPlayToEndTimeNotification, object: player.currentItem)
  }

func playerDidReachEndNotificationHandler(notification: NSNotification) {
    guard let playerItem = notification.object as? AVPlayerItem else { return }
    playerItem.seekToTime(kCMTimeZero)
  } 
```

*   NSNotificationCenter 持有对其观察者的弱引用
*   常规通知中心在发布通知的线程上传递通知

### addobserver forname(_:object:queue:using block:)

> 向接收方的调度表中添加一个条目，该条目包含一个通知队列和一个要添加到该队列的块，以及可选标准:通知名称和发送方。

```
observer = NSNotificationCenter.defaultCenter().addObserverForName(AVPlayerItemDidPlayToEndTimeNotification, object: player.currentItem, queue: NSOperationQueue.mainQueue()) { [weak self] notification in
        self?.playerDidReachEndNotificationHandler(notification)
    } 
```

*   此方法返回的是一个充当观察者的不透明对象，`NSNotificationCenter`保存了对此对象的强引用
*   避免强烈捕捉块中的对象。见 [NSNotificationCenter，块被认为是有害的](http://sealedabstract.com/code/nsnotificationcenter-with-blocks-considered-harmful/)

## 什么时候注销？

我们的想法是在不需要接收通知的时候就注销。很多时候，是我们的`deinit`

```
deinit {
    NSNotificationCenter.defaultCenter().removeObserver(self)
  } 
```

参见[注销观察者](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Notifications/Articles/Registering.html#//apple_ref/doc/uid/20000723-CEGCCFID)

> 在释放正在观察通知的对象之前，它必须告诉通知中心停止向它发送通知。否则，下一个通知会被发送到一个不存在的对象，程序就会崩溃

### iOS 9+版

参见 [OS X 10.11 和 iOS 9 发布说明
可可基金会框架](https://developer.apple.com/library/mac/releasenotes/Foundation/RN-Foundation/index.html#10_11NotificationCenter)

> 在 OS X 10.11 和 iOS 9.0 中，NSNotificationCenter 和 NSDistributedNotificationCenter 将不再向可能被取消分配的已注册观察员发送通知
> 
> 这意味着观察器不需要在其解除分配方法中取消注册

然而，

> 通过-[NSNotificationCenter addObserverForName:object:queue:using block]方法的基于块的观察器在不再使用时仍需要取消注册，因为系统仍持有对这些观察器的强引用

所以我们必须对返回的`observer`对象
调用`removeObserver`

```
deinit {
    NSNotificationCenter.defaultCenter().removeObserver(observer)
  } 
```

## 同步性

> 通知中心同步向观察者发送通知。换句话说，当发布通知时，控制不会返回到发布者，直到所有观察者都接收并处理了通知

如果您在向大量观察者发布通知时遇到性能问题，请考虑使用

*   派遣 _ 之后
*   许多`NSNotificationCenter`
*   `NSNotificationQueue`

## NSNotificationQueue

*   入队方法立即返回
*   与运行循环模式相关联
*   当前线程有`defaultQueue`

```
let queue = NSNotificationQueue(notificationCenter: NSNotificationCenter.defaultCenter())
    let notification = NSNotification(name: "CacheDidUpdateNotification", object: self)
    queue.enqueueNotification(notification, postingStyle: .PostASAP) 
```

## 阅读更多

*   [通知编程主题](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Notifications/Introduction/introNotifications.html#//apple_ref/doc/uid/10000043-SW1)
*   [具有被认为有害的块的 NSNotificationCenter】](http://sealedabstract.com/code/nsnotificationcenter-with-blocks-considered-harmful/)
*   [委托或通知](http://useyourloaf.com/blog/delegation-or-notification.html)
*   周五 Q&A 2010-01-08:NSNotificationQueue