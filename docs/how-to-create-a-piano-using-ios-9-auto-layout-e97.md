# 如何使用 iOS 9 自动布局创建钢琴

> 原文：<https://dev.to/onmyway133/how-to-create-a-piano-using-ios-9-auto-layout-e97>

一开始，人们使用框架和自动调整遮罩，然后他们使用自动布局，然后 iOS 9 鼓励他们使用`NSLayoutAnchor`、`UILayoutGuide`和`UIStackView`

为了更方便的自动布局，在 iOS 和[主播](https://github.com/onmyway133/Anchors)中查看[如何让自动布局更方便](https://medium.com/flawless-app-stories/how-to-make-auto-layout-more-convenient-in-ios-df3b42fed37f)

## NSLayoutAnchor

> NSLayoutAnchor 类是一个工厂类，用于使用 fluent API 创建 NSLayoutConstraint 对象。使用这些约束，通过自动布局以编程方式定义布局。

它有 3 个子类

`NSLayoutDimension`

*   func constraintequatontont(_ c:cgfloat)-(nslayout constraint！)！

`NSLayoutXAxisAnchor`

*   允许使用水平约束
*   防止这些

```
// This constraint generates an incompatible pointer type warning
cancelButton.leadingAnchor.constraintEqualToAnchor(saveButton.topAnchor, constant: 8.0).active = true 
```

`NSLayoutYAxisAnchor`

*   允许使用垂直约束
*   防止这些

```
// This constraint generates an incompatible pointer type warning
cancelButton.topAnchor.constraintEqualToAnchor(saveButton.trailingAnchor, constant: 8.0).active = true 
```

## UILayoutGuide

以前，我们使用虚拟视图来辅助约束。现在我们用`UILayoutGuide`

### 定义一系列视图之间的相等间距

[![uilayoutguide_spacing](img/55b22e1523d0be5f3068c0a9d8d0eb20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PsXpf29n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloud.githubusercontent.com/assets/2284279/25923413/b2c9085c-35dd-11e7-874f-58269cdbc707.png)

见全文[要旨](https://gist.github.com/onmyway133/fcf892aac565d36ee890) 

```
let space1 = UILayoutGuide()
view.addLayoutGuide(space1)

let space2 = UILayoutGuide()
view.addLayoutGuide(space2)

space1.widthAnchor.constraintEqualToAnchor(space2.widthAnchor).active = true

saveButton.trailingAnchor.constraintEqualToAnchor(space1.leadingAnchor).active = true

cancelButton.leadingAnchor.constraintEqualToAnchor(space1.trailingAnchor).active = true
cancelButton.trailingAnchor.constraintEqualToAnchor(space2.leadingAnchor).active = true

clearButton.leadingAnchor.constraintEqualToAnchor(space2.trailingAnchor).active = true 
```

### 布局参考线也可以充当黑盒，包含许多其他视图和控件

[![uilayoutguide_container](img/b397313af00de3d53df982aab5527a02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rEOtMr2h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloud.githubusercontent.com/assets/2284279/25923425/bbe47ffc-35dd-11e7-86cf-79b642188b9e.png)

见全文[要旨](https://gist.github.com/onmyway133/faa135b4db601d0db9a8) 

```
let container = UILayoutGuide()
view.addLayoutGuide(container)

// Set interior constraints
label.lastBaselineAnchor.constraintEqualToAnchor(textField.lastBaselineAnchor).active = true
label.leadingAnchor.constraintEqualToAnchor(container.leadingAnchor).active = true

textField.leadingAnchor.constraintEqualToAnchor(label.trailingAnchor, constant: 8.0).active = true
textField.trailingAnchor.constraintEqualToAnchor(container.trailingAnchor).active = true

textField.topAnchor.constraintEqualToAnchor(container.topAnchor).active = true
textField.bottomAnchor.constraintEqualToAnchor(container.bottomAnchor).active = true

// Set exterior constraints
// The contents of the container can be treated as a black box
let margins = view.layoutMarginsGuide

container.leadingAnchor.constraintEqualToAnchor(margins.leadingAnchor).active = true
container.trailingAnchor.constraintEqualToAnchor(margins.trailingAnchor).active = true

// Must use NSLayoutConstraint with the scene's top and bottom layout guides.
NSLayoutConstraint(item: container,
    attribute: .Top,
    relatedBy: .Equal,
    toItem: topLayoutGuide,
    attribute: .Bottom,
    multiplier: 1.0,
    constant: 20.0).active = true 
```

### layoutMarginsGuide

边距现在表示为`layoutMarginsGuide`，它是`UILayoutGuide`的子类

### topLayoutGuide 和 bottomLayoutGuide

在容器的例子中，我们看到了如何使用`NSLayoutConstraint`和`topLayoutGuide`。`topLayoutGuide`和`bottomLayoutGuide`是符合`UILayoutSupport`协议的对象

### 布局帧

> 布局指南在其所属视图的坐标系中定义了一个矩形空间。在调用其所属视图的 layoutSubviews 方法时，此属性包含有效的 CGRect 值。

在上面的容器示例中，容器布局指导框架是

```
(16.0, 40.0, 343.0, 21.0) 
```

## 钢琴

[![piano](img/3798c0f3d12df7ba39a3b079238be3b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W4DAOo7Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloud.githubusercontent.com/assets/2284279/25923400/a1a60246-35dd-11e7-9ccf-46f3d910f722.png)

关于如何使用`UILayoutGuide`、`NSLayoutAnchor`和`UIStackView`创建钢琴，请参见 Github 上的[钢琴](https://github.com/onmyway133/archives/tree/master/Piano)

原帖[https://github.com/onmyway133/blog/issues/22](https://github.com/onmyway133/blog/issues/22)