# 使用 AssemblyScript 优化 CanvasRenderingContext2D 函数调用

> 原文：<https://dev.to/jtenner/optimizing-canvasrenderingcontext2d-function-calls-using-assemblyscript-4i4b>

编辑:由于 AssemblyScript 编译器已经彻底改变，成为更好的垃圾收集工具，并具有不同的内存模型，该软件不再编译，需要从头开始进行彻底的重写。该软件的概念仍然适用并且有效。此外，没有计划的突破性变化，它将如何在未来工作。请继续读下去！

本文深入探讨了我如何在 AssemblyScript 中实现了一个版本的`CanvasRenderingContext2D`原型。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[jtenner](https://github.com/jtenner)/[canvas-as](https://github.com/jtenner/canvas-as)

### 一个由 AssemblyScript 支持的小型画布渲染框架🎉🎉🎉

<article class="markdown-body entry-content container-lg" itemprop="text">

# 帆布组件

一个由`AssemblyScript` <g-emoji class="g-emoji" alias="tada" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f389.png">驱动的小画布渲染框架🎉</g-emoji>T3】🎉 <g-emoji class="g-emoji" alias="tada" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f389.png">🎉</g-emoji>。

## 关于

项目`canvas-as`是一个实验，它导致了一个被称为 [as2d](http://github.com/as2d/as2d) 的高度行为测试版本的开发。因为这个项目没有经过测试，而且可能比较慢，所以我不赞成用 as2d 代替它。

谢谢你的理解。我把这份报告留在这里是有原因的。

* * *

`canvas-as`框架是一个有趣的项目，它利用了 AssemblyScript 项目所提供的大量艰苦工作，实际上它被证明是相当高效的！它避免了在 wasm 桥上使用重复的函数调用，并编译了一组 canvas 指令，使得从`AssemblyScript`内部在 canvas 上绘图更加容易。

## 目标

为在浏览器中利用`CanvasRenderingContext2D`原型提供一个高性能的框架，同时尽可能地遵循 javascript 规范，并偏离它…

</article>

[View on GitHub](https://github.com/jtenner/canvas-as)

`canvas-as`项目完全是实验性的，不知道这个库是否应该用于生产。然而，我想分享一些使用 AssemblyScript 带来的好处。

任何认识我的人都知道我对使用画布充满热情，我开始掌握它的复杂性，这样我就可以教别人如何使用它。我个人在这项事业中的一些失败包括制作了一个名为`e2d`的 JavaScript 库，但最终失败了。

`e2d`的想法是创建一个声明性的画布视图层，供开发人员像`react`一样选择和使用。一些代码(包括一个 jsx 语法)看起来像这样:

```
e2d.render(
  <translate x={100} y={100}> // translate the contents by [100, 100]
    <fillText text="Hello world!" /> // fill some text
  </translate>,
  ctx,
); 
```

有时候，当一个想法看起来闪闪发光，很有用的时候，当它在现实中行不通的时候，你很难把自己拉回来，不再去追求它。这个特别的想法很糟糕，因为它非常占用内存。每次函数调用都返回一个对象往往会导致过多的堆分配。这是关于垃圾收集的非常有价值的一课，我永远不会忘记。

一旦我有了再次尝试新事物的勇气，我决定拿起 AssemblyScript，犯一些全新的错误！

于是，`canvas-as`诞生了。

# 从地面开始上升

我开始使用 AssemblyScript 抽象出`CanvasRenderingContext2D`原型。在阅读了一些 AssemblyScript 文档后，我选择编写一些 JavaScript 链接函数，因为这是显而易见的事情。经过几个小时的工作，我把一切都连接起来，并为 Web Assembly 提供了一堆 JavaScript 回调函数。

```
// Inside AssemblyScript
@external("ctx", "getFillStyle")
declare function getFillStyle(id: i32): string;

@external("ctx", "setFillStyle")
declare function setFillStyle(id: i32, value: string): void;

export class CanvasRenderingContext2D {
  protected id: i32;
  public get fillStyle(): string {
    return getFillStyle(this.id);
  }

  public set fillStyle(value: string): void {
    setFillStyle(this.id, value);
  }
}

// and inside javascript
function getFillStyle(id: number): number {
  var ctx: CanvasRenderingContext2D = contexts.get(id);
  return wasm.newString(ctx.fillStyle);
}

function setFillStyle(id: number, value: number): void {
  var ctx: CanvasRenderingContext2D = contexts.get(id);
  ctx.fillStyle = wasm.getString(value);
} 
```

在测试和阅读了更多关于 Web Assembly 引擎的内容后，我得出结论，为每个属性集调用 JavaScript 会在 Web Assembly 和 JavaScript 之间产生太多的跳转。我认为使用私有财产会更快。

```
// Link to a setFillStyle function
@external("ctx", "setFillStyle")
declare function setFillStyle(id: i32, value: string): void;

// Call this function when the property is set
export class CanvasRenderingContext2D {
  private _fillStyle: string = "#000"; // this is the initial value

  public get fillStyle(): string {
    return _fillStyle;
  }

  public set fillStyle(value: string): void {
    this._fillStyle = value;
    setFillStyle(this.id, value);
  }
} 
```

这里的问题是我们没有像 ECMAScript 要求的那样解析输入并确定它的 CSS 颜色值。尽管有这个明显的缺陷，但它的性能基本符合预期，并且假设开发人员在生成颜色值时不会出错。

我得出了一个合乎逻辑的结论，那就是必须有一个更好的解决方案来与 Web Assembly 主机通信，所以我开始创建一些稍微不同的东西。如果有可能将一组 canvas 指令排队并连续调用它们，而不留下一个链接的 JavaScript 函数调用，那会怎么样？

我创建了一个`Serializer<T>`类来看看它会是什么样子。

```
// in AssemblyScript
@external("ctx", "render")
declare function render(id: i32, data: Float64Array): void;

class Serializer<T> {
  private index: i32 = 0;
  private data: Float64Array = new Float64Array(8000);

  @inline
  private write_one(instruction: T, value: f64): void {
    var next: i32 = this.index + 3; // calculate the next instruction pointer
    this.write(<f64>instruction);
    this.write(<f64>next);
    this.write(value);
  }

  @inline
  private write_zero(instruction: T): void {
    var next: i32 = this.index + 2; // calculate the next instruction pointer
    this.write(<f64>instruction);
    this.write(<f64>next);
  }

  @inline
  private write(value: f64): void {
    unchecked(this.data[this.index] = value);
    ++this.index;
  }
}

class CanvasRenderingContext2D extends Serializer<CanvasInstruction> {
  protected id: i32 = 0; // this refers to the context id in javascript
  private _fillStyle: string = "#000";

  // fillStyle implementation
  public get fillStyle(): string { return _fillStyle; }
  public set fillStyle(value: string): void {
    this._fillStyle = value;
    this.write_one(
      CanvasInstruction.FillStyle,
      // the changetype macro function converts reference types into pointers
      <f64>changetype<usize>(value),
    );
  }

  // required for telling the browser to draw
  public commit(): void {
    this.write_zero(CanvasInstruction.Commit);
    this.index = 0; // reset the writer
    render(this.id, this.data);
  }
} 
```

现在，我们可以在 JavaScript 主机中对一组函数调用进行排队。

```
// in JavaScript

function render(id: number, dataPointer: number): void {
  // Get the canvas context from a `Map<number, CanvasRenderingContext2D>`
  var ctx: CanvasRenderingContext2D = contexts.get(id);
  // use the performant AssemblyScript loader functions to create a TypedArray
  var data: Float64Array = wasm.getArray(Float64Array, dataPointer);

  var i: number = 0;
  while (i < data.length) {
    if (data[i] === CanvasInstruction.Commit) break;

    switch (data[i]) {
      case CanvasInstruction.FillStyle:
        // use `send_string` instead of string pointer (
        ctx.fillStyle = wasm.getString(data[i + 2]);
        break;
      ...
    }
    i = data[i + 1]; // the next index was already calculated in Web Assembly
  }
} 
```

这个循环避免了许多在正常的 canvas 开发中不可避免的堆分配。然而，有几个瓶颈。循环本身以及 switch 语句为每个周期增加了一些工作，并且对`getString()`的每个调用都执行许多堆分配，只是为了获得一个 JS 字符串引用。

我决定再次冒险进入兔子洞，看看是否还有进一步的优化。

# 进一步优化 CanvasRenderingContext2D

是时候走捷径了，从学习上下文对象如何工作开始(通过反复试验)。)例如，当执行任何类型的`fill()`操作时，如果实现巧妙，就有可能避免连续写入多个`fillStyle`值。

```
// For example, the following "red" is ignored by the browser, effectively.
ctx.fillStyle = "red";
ctx.fillStyle = "green";
ctx.fillRect(100.0, 100.0, 200.0, 200.0); 
```

这种优化可以在调用`fillRect()`函数时发生。这是检查和查看自从上次`fill()`操作以来`fillStyle`是否被改变的好时机。如果*被*改变，我们可以发出一个`fillStyle`赋值操作，有效地跳过前面例子中的`"red"`字符串。

您可能会认为这种优化是非常无用的，但是实际上检查值是否改变是非常便宜的。请记住，每次设置`fillStyle`属性时，浏览器都会解析字符串并生成一个 CSS 颜色。如果不打算使用`fillStyle`，为什么要让 JavaScript 解析颜色呢？

```
ctx.fillStyle = "black";
var result: string = ctx.fillStyle;
assert(result === "#000"); 
```

画布优化的另一个例子是减少路径操作。

```
ctx.beginPath();
ctx.rect(1, 1, 2, 2);
ctx.beginPath(); // overwrite the current path and start over
ctx.rect(100, 100, 200, 200);
ctx.fill(); 
```

这组路径指令忽略了前两个函数调用。如果它只是被忽略，为什么要告诉浏览器开始一个新的路径？相反，我们可以简单地检查是否有任何相关的路径指令排队等待写入，并发出以下(等价的)指令。

```
ctx.beginPath();
ctx.rect(100, 100, 200, 200);
ctx.fill(); 
```

事实证明，这种方法*非常有效*。

优化的另一个好例子是画布变换的计算。

```
ctx.translate(100.0, 100.0);
ctx.rotate(Math.PI);
ctx.moveTo(50.0, 50.0);
ctx.lineTo(0.0, 0.0);
ctx.stroke(); 
```

这里会发生什么优化？看一下`currentTransform`属性做参考。

```
// canvas-as stores the matrix values pre-calculated
var matrix = ctx.currentTransform;
// properties a-f are the same values that `setTransform` needs
var values: Array<f64> = [matrix.a, matrix.b, ...]; 
```

因为我们已经计算出需要什么样的转换，我们可以发出一个单独的`setTransform(a, b, c, d, e, f)`操作来代替。

```
ctx.setTransform(-1, 1.2246467991473532e-16, -1.2246467991473532e-16, -1, 100, 100);
ctx.moveTo(50.0, 50.0);
ctx.lineTo(0.0, 0.0)
ctx.stroke(); 
```

结果，`translate()`和`rotate()`函数调用被组合到一个单独的`setTransform()`操作中，该操作是在调用`moveTo`函数时计算的。这是因为每个路径操作无论如何都取决于当前的变换值*。事实上，使用`translate`和`rotate`将导致浏览器再次执行那些计算*(!)*，所以这种优化提供的增益结合得很好。*

 *最后，一个(非常重要的)最后的优化包括移除`save()`和`restore()`函数调用。`save()`和`restore()`函数实际上让浏览器复制上下文的整个状态，并在堆上复制一份。相反，为什么不实现一组预分配的堆栈值，并在每次调用保存和恢复时更改单个指针呢？

大多数 web 开发人员都熟悉虚拟 DOM 的概念，它反映了 html 文档的状态。相反，`canvas-as`将镜像画布上下文的状态，并且只执行绝对必要的操作。

最终，我们现在可以完全避免那些讨厌的堆分配了！

举下面这个例子。

```
ctx.save();
ctx.translate(x, y);
ctx.rotate(r);
ctx.drawImage(img, 0, 0);
ctx.restore();
ctx.fillRect(100, 100, 100, 100); 
```

合并转换并删除`save()`和`restore()`函数，会产生以下操作。

```
ctx.setTransform(a, b, c, d, e, f);
ctx.drawImage(img, 0, 0);
ctx.setTransform(1, 0, 0, 1, 0, 0);
ctx.fillRect(100, 100, 100, 100); 
```

这个解决方案的最后一个问题涉及到了`clip()`函数。这是因为不使用`save()`和`restore()`函数调用就不可能恢复一个裁剪操作区域。`canvas-as`优化的渲染器实际上不得不打破规范来实现这个特性。

```
ctx.save(true); // This is a hard save! Not a virtual one.
ctx.rect(100, 100, 100, 100);
ctx.clip();
// do some drawing
.
.
.
ctx.restore(); // implied hard restore 
```

现在，所有这些优化的例子都结束了，我们终于可以执行一个压力测试来看看这个实验的结果是什么。

# 测试 API

这是我用来衡量`canvas-as`表现的压力测试。

```
class Star {
  public x: f64 = Math.random() * 800.0;
  public y: f64 = Math.random() * 600.0;
}
var pi2: f64 = Math.PI * 2.0;
var stars: Star[] = new Array<Star>(0);
for (let i = 0; i < 1000; i++) stars.push(new Star());

function frame(): void {
  // turn the screen black
  ctx.fillStyle = "black";
  ctx.fillRect(0.0, 0.0, 800.0, 600.0);

  // draw each star
  var star: Star;
  for (let i = 0; i < 1000; i++) {
    star = stars[i];
    star.y += 1;
    if (star.y > 600.0) star.y -= 600;
    ctx.fillStyle = "white";
    ctx.save();
    ctx.translate(star.x, star.y)
    ctx.beginPath();
    ctx.arc(0.0, 0.0, 1.0, 0.0, pi2);
    ctx.fill();
    ctx.restore();
    // in assemblyscript, uncomment the next line to batch calls to js
    // if (i % 50 == 0) ctx.commit()
  }
  // in AssemblyScript, uncomment the next line
  // ctx.commit();
} 
```

这里唯一要提到的是，当在一个`requestAnimationFrame`循环中运行这个`frame`函数时，Firefox 似乎慢得像爬行一样。看起来它和`CanvasRenderingContext2D`原型有关系。我无法进一步挖掘它来找到问题。相反，我所有的测量都是用谷歌 Chrome 和 Opera 分别采样 20 秒的帧时间进行平均的。

无论如何，这是我在这个人为的压力测试中使用 Opera 和 Chrome 中的 devtools 收集的数据。

```
Garbage Collection Rate (less is better)
optimized-as: 2.4/s
as (without optimization): 3.6/s
js: 1.7/s

Total Heap Memory Usage Range: (smaller range is better)
optimized-as: 4.5mb-5.4mb (0.9mb difference)
as (without optimization): 4.5mb-8.7mb (4.2mb difference)
js: 4.2mb-6.8mb (2.6mb difference)

Frame Rate (more is better)
optimized-as: 57.6 fps
as (without optimization): 52.4fps
js: 56.55fps 
```

结果是苦乐参半。我们使用优化版本减少了大量的垃圾收集开销，但增加了浏览器请求收集的次数。然而，内存使用本身减少了很多，导致垃圾收集器收集的内存总量减少。

毫不奇怪，当运行 Web 程序集版本时，CPU 执行时间变得非常一致，并且优化版本(几乎)导致了更少的 CPU 使用。

# 结论

AssemblyScript 通过实现一种算法，勉强胜过纯 JavaScript，否则它可能无法在`requestAnimationFrame`循环中使用该算法。使用虚拟堆栈在内存管理的环境中有明显的好处，并导致更一致的代码执行。

然而，这种实现有其自身的缺点。

`canvas-as`没有解决的第一个突出问题实际上是确定适当的 API 抽象应该发生在哪里。抽象画图 API 最快，还是实现其他简单使用画布的东西更快？这就是为什么`canvas-as`更像是一个哲学实验，而不是一个实际的具体 API。

它也没有任何单元测试。我不知道我的库是否如预期的那样运行，但这可以通过从头开始并进行适当的测试驱动开发来缓解。这将由 jest、适当的画布模拟和大量的空闲时间来完成！

最后，非常重要的是要注意到`canvas-as`是一项正在进行中的工作，我使用的例子是为了证明一种基于尚未完全成熟的语言的实验性 web 技术。

# 提问？

欢迎在下面留下问题！我想让社区参与到我的项目中来，讨论如何做事情，我期待着在 dev.to 上与大家见面。

感谢您阅读我的文章。

乔希*