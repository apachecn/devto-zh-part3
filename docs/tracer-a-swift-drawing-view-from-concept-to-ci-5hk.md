# Tracer:从概念到 CI 的快速绘图视图

> 原文：<https://dev.to/jamiely/tracer-a-swift-drawing-view-from-concept-to-ci-5hk>

# 简介

儿童应用程序教授字母的一种常见方式是让某人画出字母，应用程序会跟踪用户的绘画。对于本文，我们将通过子类化`UIView`，增量地添加特性，添加测试，管理 Cocoapod 依赖项，以及使用 fastlane 来实现这个功能。您可以使用 [GitHub](https://github.com/jamiely/Tracer) 上的源代码继续学习。在这个过程中，我添加了到 git 标签的链接，指向特定时间点的代码的指针。

# 触动

有几个处理用户触摸视图的回调函数。

```
override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?)
override func touchesMoved(_ touches: Set<UITouch>, with event: UIEvent?)
override func touchesEnded(_ touches: Set<UITouch>, with event: UIEvent?) 
```

当我们检测到有人移动了他们的触摸时，我们可以获取触摸列表，其中包括触摸的当前和先前位置。我们将把它们存储在一个`struct Line`中。

```
struct Line {
    let start: CGPoint
    let end: CGPoint
} 
```

然后，我们将在`UIView`的`draw`调用中绘制这些线。绘图功能类似于 HTML canvas 等其他绘图 API。

```
override func draw(_ rect: CGRect) {
    lines.forEach(drawLine)
}

private func drawLine(line: Line) {
    guard let context = UIGraphicsGetCurrentContext() else {
        print("ERROR: no context available")
        return
    }
    context.move(to: line.start)
    context.addLine(to: line.end)
    context.setStrokeColor(UIColor.black.cgColor)
    context.strokePath()
    UIGraphicsEndImageContext()
} 
```

[参考/标签/画线](https://github.com/jamiely/tracer/releases/tag/line-drawing)

[https://www.youtube.com/embed/g7SyzN9jgsI](https://www.youtube.com/embed/g7SyzN9jgsI)

# 出界

我们希望实现一个功能，用户必须遵循特定的路径。如果用户超出了这条路径的某个阈值，那么我们希望检测到这一点，以便向用户提供某种指示。

至少有两种方法可以实现这一点:

1.  我们可以允许用户绘制，只要所绘制的任何点距离预期路径中的任何点都在阈值之内。
2.  我们可以在用户画图时跟踪他们的进度，并记录下预期路径上的哪些点已经被画出，并要求用户继续沿着该路径前进。这样，我们只需要检查尚未绘制的预期路径部分。

我们将首先实现#1，因为它更简单。

要实现这一点，我们首先需要接受一些预期的路径。虽然我们可能希望使用贝塞尔路径在某些点平滑路径，我们将保持事情简单，只支持线段之间的直线。我们将在我们的绘图下显示路径来引导我们的触摸。由于这个预期路径不会改变，我们将把它画在一个图像上。我们将在绘图后面的图像视图上显示图像。

[![Shows the expected path, traced path, and out of bounds concept](img/409b68c02007513d634152330e574cc9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TXvnIIv6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e74x9e4vvfbg0dihj05x.png)

接下来，我们在画图的时候，会比较我们画的点，计算期望路径中每个点的距离。这是相当低效的，但有办法让它更快。一种方法是将空间划分为一组区域，并注意预期路径中的哪些点属于这些区域。(与 [BSP](https://en.wikipedia.org/wiki/Binary_space_partitioning) 相关)。)现在，我们将只使用这种简单的方法。如果我们画的线超出了预期路径上最近点的阈值距离，那么我们将给出一些指示(通过画红线)。

我们创建一个属性，当设置了一个`UIImageView`时，该属性将在与它相关联的图像上绘制预期的路径。

```
var expectedPath: Array<CGPoint> {
    get { return _expectedPath }
    set {
        _expectedPath = newValue
        drawExpectedPath(points: newValue)
    }
}

private func drawExpectedPath(points: Array<CGPoint>) {
    UIGraphicsBeginImageContext(expectedPathView.bounds.size)

    guard var last = points.first,
        let context = UIGraphicsGetCurrentContext() else {
        print("There should be at least one point")
        return
    }

    context.setFillColor(UIColor.white.cgColor)
    context.fill(expectedPathView.bounds)

    context.setStrokeColor(UIColor.blue.cgColor)
    points[1..<points.count].forEach { pt in
        context.move(to: last)
        context.addLine(to: pt)
        context.strokePath()
        last = pt
    }

    let image = UIGraphicsGetImageFromCurrentImageContext()
    UIGraphicsEndImageContext()
    expectedPathView.image = image
} 
```

我们更改了`touchesMoved`调用来记录应该用于线条的颜色。如果该行有效，则为黑色；如果无效，则为红色。我们将在`Line`结构中添加一个属性来保存颜色。

```
override func touchesMoved(_ touches: Set<UITouch>, with event: UIEvent?) {
    touches.forEach {
        let start = $0.previousLocation(in: self)
        let end = $0.location(in: self)
        let color = colorForPoints(start, end)
        self.lines.append(Line(start: start, end: end, color: color))
    }
    setNeedsDisplay()
} 
```

仅仅检查线的两个点是否都在预期路径中任何点的某个阈值内，如果是，返回黑色，否则返回红色。

```
private func colorForPoints(_ pts: CGPoint...) -> CGColor {
    if pts.allSatisfy(isPointWithinBounds) {
        return UIColor.black.cgColor
    }

    return UIColor.red.cgColor
}

private func isPointWithinBounds(_ pt: CGPoint) -> Bool {
    let threshold: CGFloat = 75
    return expectedPath.contains { ept in
        let dx = pt.x - ept.x
        let dy = pt.y - ept.y
        let distance = sqrt(dx * dx + dy * dy)
        return distance < threshold
    }
} 
```

[![A red line denotes out of bounds](img/76124003a540e46f0571ba5cea04a732.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IjDnxHt---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ovw08t7x71x2bkdhexmn.png)

[参考/标签/红线](https://github.com/jamiely/tracer/tree/red-lines)

[https://www.youtube.com/embed/ZzuO4-Euju8](https://www.youtube.com/embed/ZzuO4-Euju8)

# 提炼特性

尽管我们取得了很大的进步，但这并不令人印象深刻。有两个问题。

*   首先，如果有一些漂亮的图片来勾勒出我们要追踪的路径，比如一个大字母 T(我们选择 T 是因为它容易追踪)，事情看起来会好很多。
*   第二，当我们指定一条路径和阈值时，我们希望视图找出给定路径之间的路点，使得任意两点之间的距离小于阈值。这将确保我们生成的红线是我们所期望的。

如果你有兴趣阅读更多内容，这篇文章将在[https://Jamie . ly/blog/programming/2019/04/01/tracer-a-swift-drawing-view . html](https://jamie.ly/blog/programming/2019/04/01/tracer-a-swift-drawing-view.html)继续，在这里我们将添加新功能，导入 cocoapods，添加单元测试和基于属性的测试，添加 CI，并通过 Cocoapods 分发我们的代码。