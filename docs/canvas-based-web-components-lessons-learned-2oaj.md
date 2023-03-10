# 基于画布的 Web 组件:经验教训

> 原文：<https://dev.to/megazear7/canvas-based-web-components-lessons-learned-2oaj>

我最近花时间学习 html canvas，发现它非常适合 web 组件。为此，我创建了 [canvas.alexlockhart.me](https://canvas.alexlockhart.me/) 来显示一些基于 canvas 动画的 web 组件。发现在创建这些画布动画时需要保持简单性，我学到了以下几课。

## 设定场景

web 组件应该负责创建画布并调整其大小以填充所需的空间。这通常是 web 组件本身的大小，但在某些情况下也可能是整个窗口的大小，例如网站介绍动画。

web 组件应该传递配置值，维护环境信息，并运行事件循环。

通过元素属性传递的配置应该调整为值 1。例如，提供速度 2 应该使速度加倍。这样，客户端代码不需要知道默认速度实际上可能是 7。

下面是一个示例，说明如何从值 1:
缩放提供的属性

```
this._defaultSpeed = 0.1;
this.speed = parseFloat(this.getAttribute("speed")) * this._defaultSpeed
                      || this._defaultSpeed; 
```

Enter fullscreen mode Exit fullscreen mode

## 使事情发生

绘制到画布和更新对象状态以创建动画不应该在组件中完成，而应该在组件创建的对象中完成

```
connectedCallback() {
    ...

    // Set the scene by creating objects
    this.objects.push(new ExampleLine({
        context: this.context,
        contextWidth: this.canvas.width,
        contextHeight: this.canvas.height,
        thickness: this.lineThickness,
        speed: this.speed
    }));

    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这个 ExampleLine 类应该能够在每个动画循环中更新和绘制自己。

```
class ExampleLine {
    draw() {
        // Draw this object to the canvas
        ...
    }

    update() {
        // Update internal state of this object.
        // Environment information may need passed in from the web component
        ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 知道何时退出

web 组件应该知道何时销毁不再需要的对象，以及何时停止动画循环。

如果一个动画永远不会结束，那么动画的复杂度一定是恒定的。例如，如果你每 100 毫秒向场景中添加一个新对象，那么你需要以同样的速度破坏对象。否则场景会变得太复杂，最终会占用太多内存。

如果只在某些用户交互过程中需要动画，比如悬停或滚动，web 组件需要知道这一点，并且只在需要时运行动画循环。场景中的“对象”不需要关心这些信息。当相应的方法被调用时，它们应该简单地更新和绘制自己。

## 相对论至关重要

所有计算都应该以宽度和高度的百分比完成，然后在根据画布的宽度和高度绘制画布时才转换为绝对值。

```
class ExampleLine {
    // Only convert from scaled positions to absolute positions
    // when they are needed to draw to the canvas.
    // This method converts the 0 to 1 scaled positions into
    // absolute sizes based upon the canvas's width and height.
    get pos() {
        return {
            p1: { x: this.contextWidth  * this.p1.x,
                  y: this.contextHeight * this.p1.y },
            p2: { x: this.contextWidth  * this.p2.x,
                  y: this.contextHeight * this.p2.y }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 完整的例子

这个代码笔提供了创建基于画布的 web 组件的方法的完整例子。对于这个简单的动画来说，这是多余的，但是为创建更复杂的动画提供了一个可扩展的模式。网站 [canvas.alexlockhart.me](https://canvas.alexlockhart.me/) 提供了我用这些方法创建的一些 web 组件的例子。

查看我的博客,了解更多我对技术和其他各种话题的思考。