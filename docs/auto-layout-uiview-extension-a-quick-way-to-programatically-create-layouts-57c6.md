# 自动布局 UIView 扩展:以编程方式创建布局的快速方法

> 原文：<https://dev.to/luisramos1337/auto-layout-uiview-extension-a-quick-way-to-programatically-create-layouts-57c6>

最近我对这个博客产生了新的兴趣。我已经修改了主题(大部分是从我朋友的博客上偷来的，[看看](https://accidental.dev/))，现在我更专注于定期写作。我注意到我的许多代码片段都静静地放在 Github 或 Gitlab 上，可以用一两篇文章来描述我为什么要做它们！

我想说的第一个是一个 UIView 扩展，我总是在我的 iOS 项目中使用它。我总是更喜欢用编程的方式来构建我的界面，这让我去寻找一些自动布局库。

我在使用自动布局库时遇到的问题是，如果一个新的开发人员加入团队，这个人将需要处理我选择的库，它通常有自己的一套习惯用法和抽象。我的经验是，人们喜欢不同的建筑布局，他们总是偏爱其他的图书馆！此外，添加的抽象让我离自动布局细节有点太远了。

几年前，我没有选择库，而是用几个方法创建了一个扩展，看看是否可以不添加依赖项。

```
func align(with view:UIView, constant:CGFloat = 0.0) {
  translatesAutoresizingMaskIntoConstraints = false
  leftAnchor.constraint(equalTo: view.leftAnchor, constant: constant).isActive = true
  rightAnchor.constraint(equalTo: view.rightAnchor, constant: -constant).isActive = true
  topAnchor.constraint(equalTo: view.topAnchor, constant: constant).isActive = true
  bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -constant).isActive = true
} 
```

我根据需要不断添加，它一直在增长。目前看起来更像这样:

```
@discardableResult
func align(with view: UIView, constant: CGFloat = 0.0) -> [NSLayoutConstraint] {
  translatesAutoresizingMaskIntoConstraints = false
  let constraints = [
    leftAnchor.constraint(equalTo: view.leftAnchor, constant: constant),
    rightAnchor.constraint(equalTo: view.rightAnchor, constant: -constant),
    topAnchor.constraint(equalTo: view.topAnchor, constant: constant),
    bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -constant),
  ]
  NSLayoutConstraint.activate(constraints)
  return constraints
}

@discardableResult
func alignTop(to anchor: NSLayoutYAxisAnchor, constant: CGFloat = 0.0, priority: UILayoutPriority = .required, isActive: Bool = true) -> NSLayoutConstraint {
  translatesAutoresizingMaskIntoConstraints = false
  let constraint = topAnchor.constraint(equalTo: anchor, constant: constant)
  constraint.priority = priority
  constraint.isActive = isActive
  return constraint
} 
```

如您所见，一些带有合理默认值的包装函数使得自动布局更好使用。代码变得更加清晰易读，而且您不会失去自动布局提供给我们的任何特性。

```
func viewDidLoad() {
  super.viewDidLoad()
  // ... setup view code

  containerView.align(with: view)
  textView.alignTop(with: view, constant: 40)
  textView.alignLeadingTrailing(with: view, constant: 20)
} 
```

几年来，这个扩展将它复制到每个新项目中，并添加了一些缺失的东西。它很适合我的需求，所以我已经不再寻找自动布局库！完整的扩展可在本公开概要中获得。感谢阅读！