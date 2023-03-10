# 如何用 UIKit 改变 iOS 状态栏颜色

> 原文：<https://dev.to/nickymarino/how-to-change-the-ios-status-bar-color-with-uikit-1j0e>

[https://Nicky Marino . com/public/assets/2020/optimizing-virgo/example _ 11 . png](https://nickymarino.com/public/assets/2020/optimizing-virgo/example_11.png)

对于某些 iOS 应用程序，更改屏幕顶部状态栏的颜色可能会有所帮助。例如，如果我有一个黑色的背景，默认的状态栏样式很难阅读:

[![Dark iPhone app](img/a707c2f45c1d6c6784e585d6506791aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aeksKIf5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickymarino.com/public/assets/2019/change-status-bar-ios/dark-dark.png)

要更改视图控制器中状态栏的外观，首先将“基于视图控制器的状态栏外观”作为一个项目添加到您的`Info.plist`中，值为`YES`:

[![Info.plist](img/c88bb23ffcb52881e4e99853e6a0a288.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UIkGLg68--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickymarino.com/public/assets/2019/change-status-bar-ios/plist.png)

然后在任何视图控制器中，覆盖`preferredStatusBarStyle`属性:

```
override var preferredStatusBarStyle: UIStatusBarStyle {
    return .lightContent
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你需要更新状态栏的颜色，调用`setNeedsStatusBarAppearanceUpdate()`。现在全视图控制器看起来是这样的:

```
import UIKit

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view,
        // typically from a nib.
        setNeedsStatusBarAppearanceUpdate()
    }

    override var preferredStatusBarStyle: UIStatusBarStyle {
        return .lightContent
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

运行这个视图控制器，我们得到一个亮的状态栏！

[![Dark iPhone app with light status bar](img/bca6caec5f47f508a862843866f14a52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wdPXy7WA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickymarino.com/public/assets/2019/change-status-bar-ios/dark-white.png)