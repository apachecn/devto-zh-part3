# 在 Swift 中使用扩展管理颜色

> 原文：<https://dev.to/ghost/managing-colors-with-extensions-in-swift-10ci>

作为一名全职的 web 开发人员和兼职的 iOS 开发人员，我最近尝试在一个位置创建一些颜色变量，以便在整个项目中使用。在大多数 web 开发项目中，您可能会将它们放在一个`utils`文件夹中，并在任何需要的地方放置它们:

定义颜色变量:

```
// utils/colors.js

export const dark = '#151951' // Default for all text 
```

使用变量:

```
// components/container.js

import { dark } from '../utils/colors'

const Container = () => {
    return <p style=`color: ${dark}`></p> }

export default Container 
```

但是在使用 Swift 的 iOS 开发中，这就有一个问题。不像在网络上，你不需要明确声明你在每个文件中导入和导出什么。相反，类的方法在整个项目中神奇地变得可用。虽然很方便，但是阅读您代码的其他人如何知道这些方法来自哪里呢？

我们可以[使用 Swift 协议](https://medium.com/@KaushElsewhere/better-way-to-manage-swift-extensions-in-ios-project-78dc34221bc8)并命名同一个类中的所有实用程序，或者做一些我更喜欢的更简单的事情:在所有扩展之前添加一个`_`，这样我们就知道它们是定制的实用函数。在 Swift 中使用[扩展，上面同样的例子看起来会是这样:](https://docs.swift.org/swift-book/LanguageGuide/Extensions.html)

> 在 iOS 开发中使用颜色的十六进制值需要用`UIColor`进行一些额外的计算，因为苹果不直接接受十六进制值。在 StackOverflow 上看到这个[的几十个不同实现。下面的方法是从](https://stackoverflow.com/questions/24263007/how-to-use-hex-color-values)[贾里德·戴维森](https://www.youtube.com/watch?v=HPhqO0D1tG4)那里借来的，表情符号记录的想法来自[和 yy Hope 的《绝色佳人》系列](https://medium.com/swift-programming/swift-prettify-your-print-statements-pt-1-64832bb7fafa)。

定义颜色变量:

```
// Colors.swift

import UIKit

extension UIColor {

    // Defined colors
  static let _dark = UIColor().hex("#151951")

  func hex(_ hex: String) -> UIColor {

    // Clean string
    var hexString = hex.trimmingCharacters(in: .whitespacesAndNewlines).uppercased()

    // Remove hash
    if hexString.hasPrefix("#") {
      hexString.remove(at: hexString.startIndex)
    }

    // Log warning if hex value is not 6 characters
    if hexString.count != 6 {
      print("⚠️ Hex value #\(hexString) is not 6 characters.
      Please enter a value with 6 characters.")
      return UIColor.white
    }

    var rgb: UInt32 = 0
    Scanner(string: hexString).scanHexInt32(&rgb)

    return UIColor.init(
      red: CGFloat((rgb & 0xFF0000) >> 16) / 255.0,
      green: CGFloat((rgb & 0x00FF00) >> 8) / 255.0,
      blue: CGFloat(rgb & 0x0000FF) / 255.0,
      alpha: 1.0
    )
  }
} 
```

使用颜色变量:

```
// CustomLabel.swift

import UIKit

class CustomLabel: UILabel {
  required init?(coder aDecoder: NSCoder) {
    super.init(coder: aDecoder)
    self.textColor = UIColor._dark
  }
} 
```

我很快意识到，你可以用 JavaScript 简单地在网上做的事情，在装有 Swift 的 iOS 中通常不会免费提供。这可能是因为这种语言相对较新，或者是建筑师做出的特殊设计选择，或者是两者的混合。

虽然我知道将 web 思维应用于原生 iOS 环境并没有多大帮助，但我发现在我学习编写更地道的 Swift 时，比较和对比实现是很有用的。希望最终我会成为一名更好的软件开发人员。

干杯！🍻