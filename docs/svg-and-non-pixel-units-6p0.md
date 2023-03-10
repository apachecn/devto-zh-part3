# SVG 和非像素单位

> 原文：<https://dev.to/f_subal/svg-and-non-pixel-units-6p0>

有时你想在浏览器上渲染非像素的东西。就像你在制作一个文字处理器，在 A4 纸上打印文本(210x297mm 毫米) <sup id="fnref1">[1](#fn1)</sup> ，或者制作一个 t 恤创作工具，在 L 尺寸的纸上打印你的照片。

一般来说，用 HTML 元素制作创作工具是一件困难的事情(可拖动的`<div>` s，大量的`contenteditable` s，一切都以像素为单位)。但是用画布制作一切又是另一个地狱。这就是 SVG 的闪光点。

### SVG 作为接口(屏幕和打印之间)

SVG 有一个很棒的属性叫做`viewBox`。它表示容器的大小(或者说，画布的大小)。除了`width`或者`height`之外都可以应用，也就是说你可以做类似“请把这个 200x200 的图像用 50x50 显示出来”(其中`200x200`部分是`viewBox`)的事情。

可以，`viewBox`可以是“两个单位系统的边框”，像“请把这张 200x200 mm 的图片用 50x50 px 显示”。

```

  <rect width="80" height="80" fill="blue" /> // MILLIMETER !!
 
```

里面的`<rect>`和这个``里面的所有东西现在都代表毫米测量的东西。

### 像素和毫米相互转换

考虑将`<rect>`拖到``里面。因为鼠标光标以像素为单位移动，所以必须将鼠标运动从像素转换为毫米。

Let `SVGCanvas`是一个用`viewBox`渲染``的`React.Component`。

```
class SVGCanvas extends React.Component<Props> {
    ...

    render () {
        const { children } = this.props
        return {children}
    }
} 
```

通常，`width`、`height`和`viewBox`的计算方法如下...

```
class SVGCanvas extends React.Component<Props> {
    get widthPx () { ... }
    get heightPx () { ... }
    get widthMm () { ... }
    get heightMm () { ... }

    get viewBox() {
        return `0 0 ${this.widthMm}  ${this.heightMm}`
    }

    render () {
        const { children } = this.props
        return {children}
    }
} 
```

现在你知道我们可以从计算值中得到`currentDpm`(每毫米点数)或`currentDpi`(每英寸点数)。

得到`dpm`就简单多了，但是`dpi`在创作软件中更受欢迎(像 Adobe Photoshop)。所以我们会得到后者...

```
class SVGCanvas extends React.Component<Props> {
    ...

    get currentDpi() {
        return this.widthPx / this.widthMm * 25.4
    }

    render () {
        const { children } = this.props
        return {children}
    }
} 
```

很好！现在我们有了将像素转换成毫米的方法。

函数 px ➔毫米已知如下(因为 1 英寸= 25.4 毫米)。

> mm =(像素/ dpi) * 25.4

所以你可以

```
// using brand type
// https://basarat.gitbooks.io/typescript/docs/tips/nominalTyping.html
type Pixel = number & { _Pixel: never }
type Millimeter = number & { _Millimeter: never }

const makePx2mm = (dpi: number) => (px: Pixel) => (px / dpi * 25.4) as Millimeter 
```

```
class SVGCanvas extends React.Component<Props> {
    ...

    get px2mm() {
        return makePx2mm(this.currentDpi)
    }

    render () {
        const { children } = this.props
        return {children}
    }
} 
```

我建议把 px ➔ mm 作为一个“咖喱”功能。天真地制作`px2mm`会产生一个二元函数，但你往往只想传递像素值。所以还不如提前“绑定”`dpi`。 <sup id="fnref2">[2](#fn2)</sup>

### 跟随来调整窗口大小

现在我们的组件中有了`px2mm`。但是在现实世界的应用程序中，`dpi`(或`this.widthPx`)可能会动态变化。尤其是当窗口调整大小时。

这就是 curried 函数证明其优点的地方。

```
class SVGCanvas extends React.Component<Props, State> {
    ...

    componentDidMount() {
        document.addEventListener('resize', this.handleResize)
    }

    handleResize () {
        // calculate current width / height to show
        this.setState({ widthPx: ..., heightPx: ... })
    }

    ...
} 
```

```
class SVGCanvas extends React.Component<Props, State> {
    get currentDpi() {
        // using state.widthPx !!
        return this.state.widthPx / this.widthMm * 25.4
    }

    get px2mm() {
        return makePx2mm(this.currentDpi)
    }

    ...
} 
```

太好了！现在你的`px2mm`会在浏览器窗口调整大小时动态改变。

### 结论

*   SVG 有一个`viewBox`，它使 SVG 成为屏幕和打印之间的接口。
*   你可以用简单的数学方法将像素换算成毫米。
*   与 currying 像素➔毫米转换器，它很容易遵循窗口大小调整。
*   将它们放在一个组件中使您的 SVG 成为一个创作工具。

* * *

1.  我发现 icloud.com 的 Pages 应用程序使用 SVG 进行文字处理。 [↩](#fnref1)

2.  不过，你也可以通过`Function.prototype.bind`来完成这个任务。 [↩](#fnref2)