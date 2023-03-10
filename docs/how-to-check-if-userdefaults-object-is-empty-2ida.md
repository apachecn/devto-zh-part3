# 如何检查 UserDefaults 对象是否为空

> 原文：<https://dev.to/maeganwilson_/how-to-check-if-userdefaults-object-is-empty-2ida>

这将是检查是否设置了用户默认值的指南。在使用它之前，你会想用它来确保一个值不为零。

下面是 ViewController 类中的一个简单示例。

```
import UIKit

class ViewController: UIViewController {

  let theme = Themes() // custom class
  // 1\. create user defaults
  let defaults = UserDefaults.standard 

  override func viewWillAppear() {
    super.viewWillAppear()

    // 2\. Check if there is not an userDefaults object for theme
    if defaults.object(forKey: "theme") == nil {
      // 3\. If there is not an object, then set a default. This will
      //    not be ran if there is an object.
      defaults.set("dark", forKey: "theme")
    }

    theme.setTheme(defaults.string(forKey: "theme"))
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

链接到[要点](https://gist.github.com/maeganjwilson/9505d1eb5e488f84603e84bea8ed3057#file-checkdefaults-swift)

1.  创建一个常量来与用户默认值交互
2.  创建一个 if 语句来检查对象是否不存在。
3.  如果对象不存在，则设置默认选项。在上面的例子中，我将其设置为暗。

如果默认对象确实存在，则不需要设置默认选项，因为它之前已经设置过。

* * *

如果你喜欢我的帖子，请考虑分享或者请我喝杯咖啡！

[![Buy Me A Coffee](img/19a55b72c49ebe5183d093b110bcdd95.png)](https://www.buymeacoffee.com/appsbymw)