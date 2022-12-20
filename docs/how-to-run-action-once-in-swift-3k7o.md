# 如何在 Swift 中运行一次 action

> 原文：<https://dev.to/onmyway133/how-to-run-action-once-in-swift-3k7o>

有时候，我们只想运行一次操作。如果我们能把这个封装起来就好了

## 分派 _ 一次

在 Objective-C 时代，有 dispatch_once 来确保一个代码块在应用程序的生命周期中只运行一次

`dispatch_once`是指运行一次且仅运行一次的动作

## 曾经

很多时候，我们只是想让一个动作运行一次。封装这一点可以防止分散的布尔标志，并且使意图更加清晰

我们可以有

```
class Once {

  var already: Bool = false

  func run(@noescape block: () -> Void) {
    guard !already else { return }

    block()
    already = true
  }
} 
```

然后我们可以像
一样使用它

```
class ViewController: UIViewController {
  let once = Once()

  override func viewDidAppear(animated: Bool) {
    super.viewDidAppear(animated)

    once.run {
      cameraMan.setup()
    }
  }
} 
```

支持我的应用程序

*   [推送 Hero -测试推送通知的纯 Swift 原生 macOS 应用](https://onmyway133.com/pushhero)
*   [PastePal -粘贴板、便笺和快捷方式管理器](https://onmyway133.com/pastepal)
*   [快速检查-智能待办事项管理器](https://onmyway133.com/quickcheck)
*   [Alias - App 和文件快捷方式管理器](https://onmyway133.com/alias)
*   [我的其他应用](https://onmyway133.com/apps/)

❤️❤️😇😍🤘❤️❤️