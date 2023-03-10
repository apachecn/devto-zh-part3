# 如何在 macOS 中保存窗口大小和位置

> 原文：<https://dev.to/onmyway133/how-to-save-window-size-and-position-in-macos-3e5n>

如果`setFrameAutosaveName`不起作用，那么我们需要手动保存和加载窗口大小和位置

我尝试了所有的解决方法。它只能保存位置，不能保存大小。所以我们应该手动操作。这是我在我的 GifCapture 应用程序[https://github.com/onmyway133/GifCapture](https://github.com/onmyway133/GifCapture)
上做的

```
class MainWindowController: NSWindowController, NSWindowDelegate {

  let key = "GifCaptureFrameKey"

  override func windowDidLoad() {
    super.windowDidLoad()

    NotificationCenter.default.addObserver(self, selector: #selector(windowWillClose(_:)), name: Notification.Name.NSWindowWillClose, object: nil)
  }

  override func awakeFromNib() {
    super.awakeFromNib()

    guard let data = UserDefaults.standard.data(forKey: key),
      let frame = NSKeyedUnarchiver.unarchiveObject(with: data) as? NSRect else {
        return
    }

    window?.setFrame(frame, display: true)
  }

  func windowWillClose(_ notification: Notification) {
    guard let frame = window?.frame else {
      return
    }

    let data = NSKeyedArchiver.archivedData(withRootObject: frame)
    UserDefaults.standard.set(data, forKey: key)
  }
} 
```

原帖[https://stack overflow . com/questions/2734888/how-can-I-easy-save-the-window-size-and-position-state-using-obj-c/44721893 # 44721893](https://stackoverflow.com/questions/2734888/how-can-i-easily-save-the-window-size-and-position-state-using-obj-c/44721893#44721893)