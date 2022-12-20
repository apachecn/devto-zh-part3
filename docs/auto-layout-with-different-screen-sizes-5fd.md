# 不同屏幕尺寸的自动布局

> 原文：<https://dev.to/onmyway133/auto-layout-with-different-screen-sizes-5fd>

自动布局很牛逼。只需声明约束，视图就会根据其父视图的`bounds`变化相应地调整大小。但有时它看起来并不好，因为我们有固定的填充、宽度、高度值，甚至固定的字体大小。

阅读更多[如何在 iOS 中让自动布局更方便](https://medium.com/flawless-app-stories/how-to-make-auto-layout-more-convenient-in-ios-df3b42fed37f)

使用`Size Class`可以在一定程度上解决这个问题。`size class`的想法是我们有许多组约束，并且基于设备特性，我们启用了其中的一些。这在 Storyboard 中更方便(虽然很难推理)，但是如果我们用代码(我更喜欢的方式)来做，那么它会有很多代码。而大量的代码意味着大量的 bug。

如果你看一下 [iOSRes](http://iosres.com/) ，我们会看到比率`16:9` ( `height:width`)

*   iPhone SE (320 x 568): 1.775
*   iPhone 6 (375 x 667): 1.778
*   iPhone 6+ (414 x 736): 1.778

它们的比例基本相同。因此，我们可以有一个简单的方法，根据比例缩放元素。鉴于设计师通常为`iPhone 6 size`设计，我们可以以此为基础。

在这种方法中，内容将根据其比例放大或缩小。你可能会争辩说，更大的手机是为了显示更多，而不是更大地显示相同的内容。您可能是对的，在这种情况下，您需要创建不同的约束和不同的 ui。但是如果你想要简单有效的解决方案，这是其中之一

这是我在做`Windows Phone development`时使用的技术，但它也适用于许多平台

### 计算比率

```
class Device {
  // Base width in point, use iPhone 6
  static let base: CGFloat = 375

  static var ratio: CGFloat {
    return UIScreen.main.bounds.width / base
  }
} 
```

### 扩展使之方便

我们可以有一个名为`adjusted`的计算属性，它根据比率
调整大小

```
extension CGFloat {

  var adjusted: CGFloat {
    return self * Device.ratio
  }
}

extension Double {

  var adjusted: CGFloat {
    return CGFloat(self) * Device.ratio
  }
}

extension Int {

  var adjusted: CGFloat {
    return CGFloat(self) * Device.ratio
  }
} 
```

### 使用比率

您可以随意调节

```
label.font = UIFont.systemFont(ofSize: 23.adjusted)

phoneTextField.leftAnchor.constraint(equalTo: container.leftAnchor, constant: 30.adjusted),
phoneTextField.rightAnchor.constraint(equalTo: container.rightAnchor, constant: -30.adjusted),

imageView.widthAnchor.constraint(equalToConstant: 80.adjusted), imageView.heightAnchor.constraint(equalToConstant: 90.adjusted), 
```

原帖[https://github.com/onmyway133/blog/issues/35](https://github.com/onmyway133/blog/issues/35)