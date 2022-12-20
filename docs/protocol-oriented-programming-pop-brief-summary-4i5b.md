# 面向协议编程(POP)概要

> 原文：<https://dev.to/noahkuwae/protocol-oriented-programming-pop-brief-summary-4i5b>

使用 Swift 时，面向协议的编程是一种强大的方法。

假设您想为某个 UIView 添加一些功能。

*   添加 360 度旋转动画功能
*   直接访问 borderColor 属性

你怎么能意识到呢？

# 1。面向对象的方法

```
extension UIView {
    func rotate(duration: TimeInterval) {
        UIView.animate(withDuration: duration / 2, delay: 0.0, options: [.curveEaseIn], animations: {
            self.transform = self.transform.rotated(by: CGFloat.pi)
        }) { _ in
            UIView.animate(withDuration: duration / 2, delay: 0.0, options: [.curveEaseOut], animations: {
                self.transform = self.transform.rotated(by: CGFloat.pi)
            }, completion: nil)
        }
    }

    var borderColor: CGColor? {
        get {
            return layer.borderColor
        }
        set {
            layer.borderColor = newValue
        }
    }
}

var myView = UIView()
myView.rotate(duration: 1.0)
myView.borderColor = UIColor.green.cgColor 
```

Enter fullscreen mode Exit fullscreen mode

### 面向对象方式的问题

*   所有 UIView 子类自动继承属性和行为，即使你不需要它们(代码闻起来像“拒绝遗赠”)
*   如果您想添加更多的功能并将其实现到扩展中，扩展会不断增长

# 2。面向协议的方式

```
protocol Rotatable {
    func rotate(duration: TimeInterval)
}

extension Rotatable where Self: UIView {
    func rotate(duration: TimeInterval) {
        UIView.animate(withDuration: duration / 2, delay: 0.0, options: [.curveEaseIn], animations: {
            self.transform = self.transform.rotated(by: CGFloat.pi)
        }) { _ in
            UIView.animate(withDuration: duration / 2, delay: 0.0, options: [.curveEaseOut], animations: {
                self.transform = self.transform.rotated(by: CGFloat.pi)
            }, completion: nil)
        }
    }
}

protocol BorderColorable {
    var borderColor: CGColor? { get set }
}

extension BorderColorable where Self: UIView {
    var borderColor: CGColor? {
        get {
            return layer.borderColor
        }
        set {
            layer.borderColor = newValue
        }
    }
}

class MyView: UIView, Rotatable, BorderColorable {}

var myView = MyView()
myView.rotate(duration: 1.0)
myView.borderColor = UIColor.green.cgColor 
```

Enter fullscreen mode Exit fullscreen mode

### 分

*   遵循[界面分离原则](https://dev.to/noahkuwae/brief-summary-of-design-principles-for-oop-14mg)
*   实现多个协议是可能的
*   在协议扩展中定义默认实现

### 面向协议方式的好处

*   松散耦合:您可以避免继承不必要的属性或行为
*   可重用性:通过分离协议和实现多个协议，您可以实现必要且充分的功能

# 引用

[LinkedIn 学习 Swift 4:面向协议编程](https://www.linkedin.com/learning/swift-4-protocol-oriented-programming)