# 重写一个老项目！第 2 部分:JavaScript

> 原文：<https://dev.to/kenbellows/rewriting-an-old-project-part-2-javascript-32mk>

> **注意**
> 这是一个迷你系列的第二部分，我在其中重温了我大学时的一个老周末黑客，我称之为[js sphere](https://github.com/kenbellows/jsphere)(因为我很懒，想不出任何更有趣的东西)。
> 
> 如果你还没有读过第一部分，那是关于 HTML 和 CSS 的，我建议你浏览一下，或者至少读一下开头的背景故事部分。
> 
> 正如我上次所说的，我不打算在这篇文章中讨论代码*做什么*，只讨论它的结构和语法。但是我确实打算以后写一个合适的演练和教程。

# 上次...

在第 1 部分中，我们回顾了的标记和样式，我已经发现这非常有趣。但是，我的意思是，这个项目主要是一个 JavaScript 项目。都是为了那个`<canvas>`！

作为参考，以下是我们正在重建的内容:

[https://codepen.io/kenbellows/embed/MxZrjy?height=600&default-tab=result&embed-version=2](https://codepen.io/kenbellows/embed/MxZrjy?height=600&default-tab=result&embed-version=2)

好吧。从哪里开始？伙计，这段代码有太多需要修改的地方，而且很多都是交织在一起的，所以很难一步一步来。所以我要做的就是...去吧，我想。

请在这里随意批评或评论这些代码！我喜欢反馈和(建设性的)批评，我总是对其他人如何完成同样的任务感兴趣，我甚至以喜欢偶尔骑自行车而闻名。

# Minutia: nah.

让我先提一下，有些变化我不会明确讨论，因为它们太小了。是的，我把所有的`var`改成了`const`和`let`，我重新排序了一些函数，我把所有的 title cased 方法改成了 normal came case(*为什么我把所有方法的第一个字母都大写了？我疯了吗？*)。但你并不关心所有这些事情。我将把重点放在真正大的事情上，我觉得有趣或好笑的事情，以及真正证明这种语言在七年里取得了多大进步的事情。

先说个搞笑的。

# 老弟，你连循环吗？

我固定了一个地方，在那里我做了一些超级奇怪的事情，可能是因为我太匆忙了，没有想清楚。这是在`Sphere`的构造函数中，当我生成所有需要表示球体表面上的点的`Dot`实例时。我用一个嵌套的`for`环从上到下环绕球体移动，边走边给球体表面添加点。这很好，但是我用一种超级奇怪的方式写了这些循环:

```
this.points = new Array();
var angstep = Math.PI/10;
var i = 0;
// Loop from 0 to 2*pi, creating one row of points at each step
for (var angxy=0; angxy<2*Math.PI; angxy+=angstep){
    this.points[i] = new Array();
    var j=0;
    for (var angyz=0; angyz<2*Math.PI; angyz+=angstep) {
        // Loop from 0 to 2*pi, creating one point at each step
        var px  = r * Math.cos(angxy) * Math.sin(angyz) + x,
            py  = r * Math.sin(angxy) * Math.sin(angyz) + y,
            pz  = r * Math.cos(angyz) + z,
            pfg = pz > z;
        this.points[i][j] = new Dot(px,py,pz,pfg);
        j++;
    }
    i++;
} 
```

在循环之外的`i`和`j`变量是怎么回事？为什么它们没有在循环中被创建和递增？我肯定知道一个`for`循环可以有多个变量；我为什么不这么做呢？

```
for (var i=0, angxy=0; angxy<2*Math.PI; i++, angxy+=angstep) { ... } 
```

但是，为什么我首先需要那些索引计数器呢？为什么我要使用`this.points[i] =`和`this.points[i][j] =`来添加数组？为什么我不使用`[].push()`向数组中添加东西呢？还有我到底为什么用`new Array()`而不是`[]`？我上了多少 OOP？？？

所以我摆脱了那些废话。我还决定将`this.points`做成一个简单的点列表，而不是一个有行的 2D 矩阵，因为我在重构其他代码时意识到，我引用`this.points`的唯一方式*是通过几个嵌套循环到达每个点，通常是像这样奇怪的`for ... in`循环:* 

```
for (var i in this.points) {
    for (var j in this.points[i]) {
        this.points[i][j]...
    }
} 
```

首先，*确实可以工作*，但是像这样在数组上使用`for...in`循环很奇怪，至少可以说是出乎意料的。当我第一次看到它时，我感到非常困惑，我想任何其他开发者阅读它时也会感到困惑。

第二，除了索引到`this.points`之外，我从未使用过`i`或`j`，这使得它成为更优雅的`for...of`循环的绝佳候选，在我最初编写这段代码时，它还不存在。

我进行了重构来解决所有这些问题，这就是我的结局。构造函数循环被更新成这样(还包括对点构造函数的修改，我稍后会谈到):

```
// The angle delta to use when calculating the surface point positions;
// a larger angstep means fewer points on the surface
const angstep = Math.PI/10;
this.points = [];
// Loop from 0 to 2*pi, creating one row of points at each step
for (let angxy=0; angxy<2*Math.PI; angxy+=angstep){
    for (let angyz=0; angyz<2*Math.PI; angyz+=angstep) {
        // Loop from 0 to 2*pi, creating one point at each step
        this.points.push(new Dot({
            x: r * Math.cos(angxy) * Math.sin(angyz) + x,
            y: r * Math.sin(angxy) * Math.sin(angyz) + y,
            z: r * Math.cos(angyz) + z,
            fg: Math.cos(angyz) > 0
        }));
    }
} 
```

`this.points`元素上的循环现在看起来像这样:

```
for (const point of this.points) {
    point...
} 
```

啊...好多了。😌

# 类！

由于这个项目完全是由表面上的一堆点组成的交互式球体，并且由于我在 2012 年攻读计算机科学学位，过去几年的功能革命还没有开始，所以这个项目主要由两种高级对象类型组成:

*   `Dot` -代表球体表面上的单个点
*   `Sphere` -代表整个球体，及其所有的`Dot`

2012 年，原生的`class`还没有被标准化，所以我用老派的`function`作为构造者的风格来写所有的东西。例如，这里是`Sphere`构造函数的一部分:

```
function Dot(x,y,z,fg,fgColor,bgColor) {
    // Default Values:  { x: 0, y: 0, z: 0, fg: true, fgcolor: #7EE37E, bgcolor: "#787878" }
    this.x = typeof x !== 'undefined' ? x : 0;
    this.y = typeof y !== 'undefined' ? y : 0;
    this.z = typeof z !== 'undefined' ? z : 0;
    this.fg = typeof fg !== 'undefined' ? fg : true;
    this.fgColor = typeof fgColor !== 'undefined' ? fgColor : "#7EE37E";
    this.bgColor = typeof bgColor !== 'undefined' ? bgColor : "#787878";

    this.Draw = function(ctx) {
        // Store the context's fillStyle
        var tmpStyle = ctx.fillStyle;
        // Set the fillStyle for the dot
        ctx.fillStyle = (this.fg ? this.fgColor : this.bgColor);
        // Draw the dot
        ctx.fillCircle(x,y,this.fg?10:5);
        // Restore the previous fillStyle
        ctx.fillStyle = tmpStyle;
    }
} 
```

现在，这里有另一个问题，这可能与我对 JS 原型的有限理解有关:`Dot`的每个实例都定义了自己的`Draw`函数的副本，而不是引用一个类方法。我应该写的是:

```
function Dot(x,y,z,fg,fgColor,bgColor) {
    // ...
}
Dot.protoype.Draw = function(ctx) {
    // Store the context's fillStyle
    var tmpStyle = ctx.fillStyle;
    // Set the fillStyle for the dot
    ctx.fillStyle = (this.fg ? this.fgColor : this.bgColor);
    // Draw the dot
    ctx.fillCircle(x,y,this.fg?10:5);
    // Restore the previous fillStyle
    ctx.fillStyle = tmpStyle;
} 
```

这声明了函数的单个副本，而不是每次都在一个新函数上浪费内存。

但这是一个枝节问题，因为 ES2015 中引入的原生`class` es(又名 ES6)在引擎盖下处理了这个问题！

但在我们开始之前，让我们再提一件事:ES2015 还引入了[对象析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring)、[函数参数默认值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters)和[将这两者结合使用](https://davidwalsh.name/destructuring-function-arguments)，以基本上获得带有默认值的命名参数的等价物，就像在 Python 和 Ruby 等语言中看到的那样！

AAAA 和一个*多了一个*的东西:ES2015 *也*引入了 [`Object.assign()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) ，我喜欢用它来初始化`this`的属性，比传统的一个一个的方法简洁多了。(伙计，ES2015 真是个好东西！)

结合使用这两种技术，我可以从`Dot`构造函数:
中删除这个讨厌的片段

```
// Gross 🤢
this.x = typeof x !== 'undefined' ? x : 0;
this.y = typeof y !== 'undefined' ? y : 0;
this.z = typeof z !== 'undefined' ? z : 0;
this.fg = typeof fg !== 'undefined' ? fg : true;
this.fgColor = typeof fgColor !== 'undefined' ? fgColor : "#7EE37E";
this.bgColor = typeof bgColor !== 'undefined' ? bgColor : "#787878"; 
```

将所有这些改进放在一起，我可以像这样重写`Dot`:

```
class Dot {
    constructor({x=0, y=0, z=0, fg=true, fgColor='#7EE37E', bgColor='#787878'}={}) {
        Object.assign(this, {x, y, z, fg, fgColor, bgColor});
    }

    draw(ctx) {
        // Store the context's fillStyle
        var tmpStyle = ctx.fillStyle;
        // Set the fillStyle for the dot
        ctx.fillStyle = (this.fg ? this.fgColor : this.bgColor);
        // Draw the dot
        ctx.fillCircle(x, y, this.fg ? 10 : 5);
        // Restore the previous fillStyle
        ctx.fillStyle = tmpStyle;
    }
} 
```

干净多了！

# 重新分离我的顾虑

正如我刚才所描述的那样，当我重写我的类时，我开始发现`Dot`正在做的事情更好地由`Sphere`来处理，而`Sphere`在每个`Dot`上重复执行操作，这些操作可以由它们中的每一个在内部更干净地处理。

首先，上面的`Dot`类的`Draw`方法？是啊，它从来没有真正被调用过。原来我需要一些来自`Sphere`的关于每一个`Dot`的上下文来正确的画出它，也就是它在球体表面的位置以及球体当前是如何旋转的。比起将所有的上下文传递给每个`Dot`，直接从`Sphere`类开始绘制要干净得多。因此...我删除了`Dot`上的`Draw`方法，以及随之而来的`fgColor`和`bgColor`属性。

第二，有两种主要的方式改变`Dot`来移动`Sphere`:它们是*翻译*(移动)，它们是*缩放*(放大或缩小)。我的事件处理程序中有一堆重复的代码，每个`Dot`的属性都被手动重新计算并以同样的方式更新，所以我想我应该[弄干](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)我的代码并移动逻辑将`Dot`改为`Dot`类本身。我给了`Dot`两个新方法:`scale`和`translate`。

做完这些之后，我的`Dot`类看起来是这样的:

```
class Dot {
    constructor({x=0, y=0, z=0, fg=true}={}) {
        Object.assign(this, {x, y, z, fg});
    }

    /**
     * Scale this dot's position by multiplying all coordinates by the given scale factor
     * @param {Number} scaleFactor
     */
    scale(scaleFactor) {
        this.x *= scaleFactor
        this.y *= scaleFactor
        this.z *= scaleFactor
    }

    /**
     * Move this dot to a new position indicated by the given x, y, and z distances
     * @param {Number} [x=0]
     * @param {Number} [y=0]
     * @param {Number} [z=0]
     */
    translate({x=0, y=0, z=0}) {
        this.x += x
        this.y += y
        this.z += z
    }
} 
```

# 不要乱动原型！

我根据经验做了一些改变。在我离开大学之前，我对公认的社区最佳实践几乎一无所知，也不知道它们为什么重要。例如，我有几个在画布上绘图的实用函数。在我的旧代码中，我直接将它们添加到了`CanvasRenderingContext2D`原型中，如下:

```
Object.getPrototypeOf(ctx).fillCircle = ctxFillCircle; 
```

这并不理想。假设有一天一个真实的`ctx.fillCircle`被添加到规范中，并且它与我的代码不同。在这个小演示中，这可能没多大关系，因为我是唯一一个处理代码的人，但是在一个有多个作者的项目中，遇到一个看起来不太一样的标准化方法可能会非常混乱。甚至未来的我也可能会困惑！

所以我进行了重构:给每个函数添加了一个`ctx`参数，函数中所有对`this`的引用都被改成了`ctx`，我删除了上面修改了`CanvasRenderingContext2D`原型的代码，我修改了代码以直接调用函数，并将`ctx`作为第一个参数传递，而不是作为`ctx`的方法调用它们。

# 删除 jQuery

最后是 jQuery。我喜欢它，它为网络做了很多好事，但在大多数情况下，包括我的情况，它不再被需要。

在这个项目中，我基本上用 jQuery 做了三件事，其中两件被简单地替换了:

*   我使用了`$(function() { ... })`包装器来延迟代码执行，直到页面加载完毕。正如我在第 1 部分中讨论的，如果您将`defer`属性添加到任何需要等待的`<script>`标签，这是不必要的。([再次感谢，@crazytim！](https://dev.to/crazytim/comment/a255)
*   我在几个地方使用了经典的`$()`选择器函数来获取元素。这是现在(实际上是那时，虽然我对它不熟悉)带有`document.querySelector`和`document.querySelectorAll`的平台的本地部分。
*   最难的一个:我使用了`jquery.events.drag`插件来处理基于鼠标的交互性。这个需要稍微多做一点来替换。

前两个超级基础，我就懒得展示了。相反，我将关注第三个:事件插件。

我是这样使用它的:

```
$("#canvas")
    .drag("init", function(e){
        startX = e.clientX;
        startY = e.clientY;
    })
    .drag(function(e){
        if (e.ctrlKey) {
            if (e.altKey) sphere.HiddenFun2(e.clientX-startX,e.clientY-startY);
            else sphere.Zoom(e.clientX-startX, e.clientY-startY, -1);
        } else if (e.shiftKey) {
            if (e.altKey) sphere.HiddenFun1(e.clientX-startX,e.clientY-startY);
            else sphere.Pan(e.clientX-startX,e.clientY-startY);
        } else {        
            sphere.Rotate(e.clientX-startX,e.clientY-startY);
        }
        startX = e.clientX;
        startY = e.clientY;
    }); 
```

因此，当拖动开始时，存储光标的起始位置，然后当拖动继续时，根据使用的修饰键执行某些操作，并为下一次移动更新光标位置。

那么，对于常规的旧`mouse`事件，我们该如何做呢？

我分三个阶段实现了它:拖动开始、拖动和拖动结束。拖动开始事件是`mousedown`；我们将记录光标的起始位置，并添加拖动和拖动结束事件侦听器。拖动事件将是`mousemove`；在这里，我们将执行所有的逻辑来检查修改键，并实际操作球体。最后，我将使用两个事件进行拖动停止:`mouseup`和`mouseout`。这样，我们就不会遇到那种奇怪的情况，你把鼠标移出窗口，然后放开，应用程序就停留在拖动状态，直到你再次点击。这些事件只是删除了在拖动启动时注册的事件处理程序。

我决定在`window`中添加`mousemove`、`mouseup`和`mouseout`，这样你可以在点击后在页面上移动鼠标。我不得不使用`event.stopPropagation()`抑制`canvas`和`body`元素上的`mouseout`事件，以防止在将鼠标移进和移出画布时过早触发拖动停止。

下面是所有这些的样子:

```
let dragOrigin;
canvas.addEventListener('mousedown', dragStartHandler)
function dragStartHandler(e){
    dragOrigin = {
        x: e.clientX,
        y: e.clientY
    };
    document.addEventListener('mousemove', dragHandler);

    window.addEventListener('mouseup', dragStopHandler);
    window.addEventListener('mouseout', dragStopHandler);

    canvas.addEventListener('mouseout', stopPropagation);
    document.body.addEventListener('mouseout', stopPropagation);
}

function dragHandler(e){
    if (e.ctrlKey || e.metaKey) {
        if (e.altKey) sphere.hiddenFun2(e.clientX-dragOrigin.x, e.clientY-dragOrigin.y);
        else sphere.zoom(e.clientX-dragOrigin.x, e.clientY-dragOrigin.y, -1);
    }
    else if (e.shiftKey) {
        if (e.altKey) sphere.hiddenFun1(e.clientX-dragOrigin.x, e.clientY-dragOrigin.y);
        else sphere.pan(e.clientX-dragOrigin.x, e.clientY-dragOrigin.y);
    }
    else {
        sphere.rotate(e.clientX-dragOrigin.x, e.clientY-dragOrigin.y);
    }
    dragOrigin.x = e.clientX;
    dragOrigin.y = e.clientY;
}

function dragStopHandler(e) {
    document.removeEventListener('mousemove', dragHandler);
    document.removeEventListener('mouseup', dragStopHandler);
    document.removeEventListener('mouseout', dragStopHandler);

    canvas.removeEventListener('mouseout', stopPropagation);
    document.body.removeEventListener('mouseout', stopPropagation);
}

function stopPropagation(e) {
    e.stopPropagation();
} 
```

所以即使那样也不算太糟！但是后来我意识到我已经忘记了...

# 触摸支持移动😭

在实现了我更新的代码后，我访问了手机上的页面；什么都没用。然后我突然想到:*手机没有鼠标！*🤦

一些快速的搜索让我想起了最近的 [`pointer`事件](https://developer.mozilla.org/en-US/docs/Web/API/PointerEvent) ( `pointermove`，`pointerup`等等。)，旨在统一许多不同类型的类似指针的交互:鼠标、触摸、手写笔，我们还没有想到的新东西。问题(目前)是[移动支持不稳定](https://caniuse.com/#feat=pointer) : `pointer`事件在 Android、Opera Mobile、Android Browser、Samsung Browser 和 IE Mobile(？？？)，但不适用于 Android、iOS Safari 或大多数其他移动浏览器的 Firefox..

另一个问题是，除了旋转球体之外，其他任何操作都需要按下键盘上的修饰键(Shift+拖动平移，Ctrl+拖动缩放)。因此`pointer`事件本身只会启用旋转，不会启用其他功能。

一个合适的解决方案是学习`touch`事件，以及如何实现像缩放和双指平移这样的手势，但老实说，我稍微研究了一下，发现*太*复杂了！比我现在想的还要多。所以`pointer`事件将不得不作为一个折中的措施，只修复旋转，只在一些移动设备上工作。我对此并不兴奋😞

不管怎样，这就是所有这些看起来的样子。我通过检查`<body>`是否有`onpointermove`属性来测试对`pointer`事件的支持，如果没有，我就返回到`mouse`事件，因为有些桌面浏览器也不支持`pointer`事件(看看你，Safari😡).

```
// Prefer 'pointer' events when available
const pointerEvent = (
  'onpointermove' in document.body
    ? 'pointer'
    : 'mouse'
);
// Shorthands for 'mouse' or 'pointer' events
const [downEvt, upEvt, moveEvt, outEvt] = (
  ['down', 'up', 'move', 'out']
    .map(evtType => pointerEvent + evtType)
);

// Drag events
let dragOrigin;
canvas.addEventListener(downEvt, dragStartHandler)
function dragStartHandler(e){
    dragOrigin = {
        x: e.clientX,
        y: e.clientY
    };
    document.addEventListener(moveEvt, dragHandler);

    window.addEventListener(upEvt, dragStopHandler);
    window.addEventListener(outEvt, dragStopHandler);

    canvas.addEventListener(outEvt, stopPropagation);
    document.body.addEventListener(outEvt, stopPropagation);
}

function dragHandler(e){
    if (e.ctrlKey || e.metaKey) {
        if (e.altKey) sphere.hiddenFun2(e.clientX-dragOrigin.x, e.clientY-dragOrigin.y);
        else sphere.zoom(e.clientX-dragOrigin.x, e.clientY-dragOrigin.y, -1);
    }
    else if (e.shiftKey) {
        if (e.altKey) sphere.hiddenFun1(e.clientX-dragOrigin.x, e.clientY-dragOrigin.y);
        else sphere.pan(e.clientX-dragOrigin.x, e.clientY-dragOrigin.y);
    }
    else {
        sphere.rotate(e.clientX-dragOrigin.x, e.clientY-dragOrigin.y);
    }
    dragOrigin.x = e.clientX;
    dragOrigin.y = e.clientY;
}

function dragStopHandler(e) {
    document.removeEventListener(moveEvt, dragHandler);
    document.removeEventListener(upEvt, dragStopHandler);
    document.removeEventListener(outEvt, dragStopHandler);

    canvas.removeEventListener(outEvt, stopPropagation);
    document.body.removeEventListener(outEvt, stopPropagation);
}

function stopPropagation(e) {
    e.stopPropagation();
} 
```

顺便说一下，在做这个的时候，我发现 [Chrome 的开发工具非常适合模拟带有触摸事件的移动设备](https://developers.google.com/web/tools/chrome-devtools/device-mode/)！

哦，最后一件事。我不得不在画布上添加一个 CSS 规则来使 touch 正常工作:`touch-action: none;`。这基本上防止了浏览器捕获触摸事件来尝试滚动页面，并允许我在我的 JavaScript 中使用它们。

# 成品

这是一个很大的迭代，老实说，在写这篇文章的时候，我对它做了更多的调整，但是它就在那里！以下是所有更新后的样子:

[https://codepen.io/kenbellows/embed/bZzxMP?height=600&default-tab=result&embed-version=2](https://codepen.io/kenbellows/embed/bZzxMP?height=600&default-tab=result&embed-version=2)

# 结论

我还是想解决一些事情。我遇到的最大问题是它对移动设备不友好，真的一点也不友好。不仅缩放和平移在手机上不起作用，而且网页本身几乎完全没有响应。我想添加一些 CSS 来更干净地处理特别小的页面。也许我会在这之后再做一些调整🤔正如我在开头所说的，我很乐意收到您的任何反馈！见鬼，如果你觉得慷慨，那就做一个反对回购的公关吧！

但这里是我的总体收获:HTML、CSS 和 JavaScript 在过去的 7 年里有了很大的改进，我作为一名开发人员也是如此。体验是一件很神奇的事情！

在我将要写的这个系列的最后一部分...最后，我将介绍 JavaScript 本身，并讨论它是如何工作的。这将是一个关于如何用普通 JavaScript 构建一个东西的独立教程，而不是依赖于前两篇文章。