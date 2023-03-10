# 离开主线程渲染你的图形

> 原文：<https://dev.to/yashints/offscreencanvas-render-your-graphics-off-the-main-thread-1732>

在网页上渲染图像可能是一项计算量非常大的操作。这使得在主线程上运行这类操作更加困难，因为它可能会降低渲染速度或影响用户体验。

# 背景

[Canvas](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/canvas) 在我看来是最有趣的 HTML 元素之一。使用它最普遍的原因是绘制图形或动画。

来自谷歌开发者网站:

> 它通常用于在富媒体 web 应用程序和在线游戏中创建漂亮的用户体验。

事实上，您可以编写脚本，提高了标准，使元素更加有趣。这在很多情况下给了你很大的灵活性，但是我们今天主要关注动画或者渲染。

# 问题

同时，执行 JavaScript 是用户体验问题最常见的来源之一。因为所有的 JavaScript 代码[都在与用户交互相同的线程](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)上运行，这些繁重的计算操作除了影响实际和感知的性能之外，还会影响用户体验。

# 举例

为了给你一个使用 canvas 有多简单的例子，考虑下面的代码:

```
<canvas id="canvas" width="300" height="300">
  An alternative text describing what your canvas
  displays.
</canvas> 
```

Enter fullscreen mode Exit fullscreen mode

在您的 JavaScript 文件中:

```
var mainCanvas = document.getElementById(
  'myCanvas'
)
var mainContext = mainCanvas.getContext('2d')

var canvasWidth = mainCanvas.width
var canvasHeight = mainCanvas.height

var angle = 0

var requestAnimationFrame =
  window.requestAnimationFrame ||
  window.mozRequestAnimationFrame ||
  window.webkitRequestAnimationFrame ||
  window.msRequestAnimationFrame

function drawCircle() {
  mainContext.clearRect(
    0,
    0,
    canvasWidth,
    canvasHeight
  )

  // color in the background
  mainContext.fillStyle = '#EEEEEE'
  mainContext.fillRect(
    0,
    0,
    canvasWidth,
    canvasHeight
  )

  // draw the circle
  mainContext.beginPath()

  var radius =
    25 + 150 * Math.abs(Math.cos(angle))
  mainContext.arc(
    225,
    225,
    radius,
    0,
    Math.PI * 2,
    false
  )
  mainContext.closePath()

  // color in the circle
  mainContext.fillStyle = '#006699'
  mainContext.fill()

  angle += Math.PI / 64

  requestAnimationFrame(drawCircle)
}

drawCircle() 
```

Enter fullscreen mode Exit fullscreen mode

它看起来是这样的:

[https://codepen.io/yashints/embed/wbWppN?height=600&default-tab=result&embed-version=2](https://codepen.io/yashints/embed/wbWppN?height=600&default-tab=result&embed-version=2)

现在让我们给这段代码添加一点变化。让我们在动画运行的同时运行一些代码。我们添加了一个按钮和一个调用函数来计算斐波那契数列的处理程序🤷‍♂️.所以让我们开始添加按钮:

```
<button type="button" id="make-busy">
  Hit the main thread!
</button> 
```

Enter fullscreen mode Exit fullscreen mode

让我们用递归函数实现斐波那契数列。然后我们将使用`requestAnimationFrame`来确保这段代码在下一次可用的重画中运行。要了解更多关于`requestAnimationFrame`的信息，请务必查看[的文档](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)。

```
function fibonacci(num) {
  if (num <= 1) return 1

  return fibonacci(num - 1) + fibonacci(num - 2)
}

document
  .querySelector('#make-busy')
  .addEventListener('click', () => {
    document.querySelector('#busy').innerText =
      'Main thread working...'
    requestAnimationFrame(() => {
      requestAnimationFrame(() => {
        fibonacci(40)
        document.querySelector(
          '#busy'
        ).innerText = 'Done!'
      })
    })
  }) 
```

Enter fullscreen mode Exit fullscreen mode

现在如果你点击按钮，你会看到动画会在调用`fibonacci`函数返回后停止并恢复。

[https://codepen.io/yashints/embed/QREBrx/?height=600&default-tab=result&embed-version=2](https://codepen.io/yashints/embed/QREBrx/?height=600&default-tab=result&embed-version=2)

您可以清楚地看到，如果用户在您的 web 应用程序中面临类似的情况，他们会有什么样的感受。这正是`OffscreenCanvas`可以帮忙的时候。

# 屏幕外画布

直到最近，canvas 的绘图功能直接依赖于`<canvas>`元素，这意味着它将依赖于 DOM(文档对象模型)。`OffscreenCanvas`另一方面，通过将 DOM 和 Canvas API 的操作移出屏幕，将它们解耦。

更有趣的是，由于前面提到的分离，渲染操作现在可以在 worker 内部运行。仅此一点就为各种性能改进打开了可能性之门。

## 在工人中使用它

网络工作者是网络版的多线程。它们允许你在一个单独的线程中运行代码/任务(又名后台😁).既然 DOM 和 Canvas API 之间的解耦已经实现，我们可以在 worker 内部运行它了。

通过扩展我们之前的例子，我们将添加相同动画的另一个副本，但是这次我们将在 web worker 的`OffscreenCanvas`中进行渲染。

我们需要在一个脚本(我已经命名为`animation.js`)中设置我们的工人代码，以便以后使用。首先，我们需要将动画代码移动到它自己的文件中。然后让我们创建我们的工人代码:

```
let animationWorker = null
self.onmessage = function(e) {
  switch (e.data.msg) {
    case 'start':
      if (!animationWorker) {
        importScripts(
          e.data.origin + '/animation.js'
        )
        animationWorker = new ThemedAnimation(
          e.data.canvas.getContext('2d')
        )
      }
      animationWorker.start()
      break
    case 'stop':
      if (!animationWorker) {
        return
      }
      animationWorker.stop()
      break
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

不，你可以并排创建两个画布元素:

```
<main>
  <section class="support">
    Your browser does not support OffscreenCanvas.
  </section>
  <div>
    <h1>Canvas on main thread</h1>
    <p>
      Interaction is blocked when a theme is
      loading
    </p>
    <canvas
      id="canvas-window"
      width="400"
      height="400"
    ></canvas>
  </div>
  <div>
    <h1>Canvas on worker thread</h1>
    <p>
      Interaction works even if a theme is loading
    </p>
    <canvas
      id="canvas-worker"
      width="400"
      height="400"
    ></canvas>
  </div>
</main> 
```

Enter fullscreen mode Exit fullscreen mode

最后，从上面的脚本标签中创建 worker，请求一个动画帧(我们之前使用了`setTimeOut`),并同时在两个画布中运行动画。

```
document
  .querySelector('main')
  .classList.toggle(
    'supported',
    'OffscreenCanvas' in window
  )
document
  .querySelector('#make-busy')
  .addEventListener('click', () => {
    document.querySelector('#busy').innerText =
      'Main thread working...'
    requestAnimationFrame(() => {
      requestAnimationFrame(() => {
        Animation.fibonacci(40)
        document.querySelector(
          '#busy'
        ).innerText = 'Done!'
      })
    })
  })

const canvas = document.querySelector('#case1')
const animationWindow = new Animation(
  document
    .querySelector('#canvas-window')
    .getContext('2d')
)
animationWindow.start()
const workerCode = document.querySelector(
  '#workerCode'
).textContent
const blob = new Blob([workerCode], {
  type: 'text/javascript',
})
const url = URL.createObjectURL(blob)
const worker = new Worker(url)
const offscreen = document
  .querySelector('#canvas-worker')
  .transferControlToOffscreen()
const urlParts = location.href.split('/')
if (urlParts[urlParts.length - 1].indexOf('.') !== -1) {
  urlParts.pop()
}
worker.postMessage(
  {
    msg: 'start',
    origin: urlParts.join('/'),
    canvas: offscreen,
  },
  [offscreen]
)
URL.revokeObjectURL(url) // cleanup 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看这里发生了什么。首先我们需要检测这个特性(OffscreenCanvas 还不完全支持)。接下来，我们为按钮挂接一个事件处理程序，给主线程增加一些负载。这将给我们在主线程中的动画渲染带来一些压力。

之后，我们得到画布并在主线程中运行动画代码，之后，我们在 worker 中做同样的事情。除了我们必须向我们的工人发送一个消息来开始(注意工人代码中的开关情况)。

最后但同样重要的是，确保不要错过代码中最重要的部分，即在画布上调用`transferControlToOffscreen`。这就是`OffscreenCanvas`的神奇之处。该方法将我们的常规 canvas 转换为 OffscreenCanvas 实例。

出于某种原因，我不能在这里嵌入一个`iframe`，但是你可以在我的博客上看到[的结果。](https://yashints.dev/blog/2019/05/11/offscreen-canvas)

只是在这里增加了一个截图😊。

[![OffscreenCanvas demo](img/818ca48d52dc8ed94da6d4ad81bfe072.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fjMb5Rdy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tclrt44gs320lj9g8jvb.JPG)

# 总结

我们看到了使用`OffscreenCanvas`与在主线程上运行相同程序相比，在渲染平滑度上的差异。这个惊人的特性可以和我之前在发布的关于网络性能的文章中的其他技术一起使用，帮助你拥有一个更快的网站和更快乐的用户😊。