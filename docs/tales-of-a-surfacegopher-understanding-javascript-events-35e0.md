# #SurfaceGopher 的故事:理解 JavaScript 事件

> 原文：<https://dev.to/andyhaskell/tales-of-a-surfacegopher-understanding-javascript-events-35e0>

[![Tales of a #SurfaceGopher: Understanding JavaScript Events](img/9137ded5775eb144d48a6a942b6c93fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vddb3Ef2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2lxpgp8t3yntpbuzv3iq.jpeg)

JavaScript 中的事件是如何处理 web 应用程序的各种不同类型的输入，使其具有交互性。自 90 年代以来，一些最熟悉的输入是像点击和按键这样的事情，但今天的网络应用程序也可以处理输入，如对 API 请求的响应、数据库事务，以及对移动网络应用程序来说，像你的 GPS 位置这样的事情。

事件的主要概念和我们为什么有它们是因为事件提供了一种让用户与你的 web 应用程序交互的方式，以及让你的 web 应用程序对发生的事情做出反应的方式。我们让我们的 web 应用程序监听事件，并在**回调函数**中运行响应事件的 JS 代码，以做诸如显示新数据或响应用户偏好变化之类的事情。

但是学习 JavaScript 时，我很难理解这些事件。事件是如何“介入”并出现在我们的代码中的，它们到底是什么？而人们在谈论的这个神秘的“事件循环”是什么？

## 用事件做实验

让我们来看一个处理事件的小 HTML 页面:

```
<html>
  <head>Event handling with a sloth</head>
  <body>
    <h1 id="sloth" onclick="handleClick()">
      ((.(-ω-)((. Sloth is sleeping
    </h1>
    <script type="text/javascript">
      function handleClick() {
        console.log('You clicked on the <h1>!');
        let sloth = document.getElementById("sloth");
        sloth.innerHTML = '((.(⊙ω⊙)((. Sloth is awake';
      }
    </script>
  </body>
</html> 
```

如果你点击 HTML 页面上的文本，因为我们将函数`handleClick`注册为 h1 的`onclick`处理程序，如果我们点击 h1，它会打开树懒的眼睛。到目前为止很简单。

[![Our HTML page after clicking to wake up the sloth](img/c9cc730af31d47cfea89e10846fa6e61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Aqh0Abco--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ykd0i4r1jb725bvfchvf.png)

注意:在不支持 HTML 页面中⊙和ω的浏览器上，在你的 HTML 和 JavaScript 中将⊙替换为ʘ，将ω替换为ω，来制作树懒的眼睛和鼻子

让我们用点击处理程序做一些实验。尝试让`handleClick`接受一个名为`event`的参数，并记录它的值:

```
<h1 id="sloth" onclick="handleClick(event)">
  ((.(-ω-)((. Sloth is sleeping
</h1>
<script type="text/javascript">
  function handleClick(event) {
    console.log(event);
    console.log('You clicked on the <h1>!');
    let sloth = document.getElementById("sloth");
    sloth.innerHTML = '((.(⊙ω⊙)((. Sloth is awake';
  }
</script> 
```

重新加载你的页面，再次点击树懒，打开你的浏览器控制台(Mac 的 Command+Option+I，Windows 的 Ctrl+Shift+I)，你会看到一个你可以探索的对象。

[![The Google Chrome browser console logging our event. Google Chrome's got a lot to say about a mouse click!](img/a3f5827e48ddaabfe5be3f66b95eae44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wA_jBUnU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yujjbojgga69669zkxao.png)

正如你所看到的，这个对象是一个[鼠标事件](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent)。滚动它，您会看到它有类似于`screenX`和`screenY`(表示您点击的屏幕上的 X 和 Y 坐标的数字)、`ctrlKey`(当您点击 h1 时是否按住 Ctrl 键)和`target`(鼠标事件是针对哪个 HTML 元素的)。

现在让我们尝试另一个实验。在脚本标签的末尾，将这个循环添加到`<script>`标签的末尾:

```
setTimeout(function() {
  for (let i = 0; i < 1000000000; i++) {
    if (i % 1000000 == 0 && i > 0) {
      console.log(i);
    }
  }
}, 100); 
```

使用这个`setTimeout`，我们等待十分之一秒(让我们的页面显示)，然后我们开始一个循环，数到 10 亿，这应该需要几秒钟才能完成。现在刷新这个页面，然后再点击几次树懒，然后打开你的浏览器控制台。

[![The browser console counting to a billion. The mouse event doesn't log until after the counting loop completes](img/22a0543c764f4b524071688b3f2cb626.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xUtCs-8R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ysxlgi5xpe6oqbzb1ntu.png)

有趣的是，尽管在计数器结束之前你肯定用一个点击事件唤醒了我们的树懒，但是我们直到循环结束之后才真正运行`handleClick`。这是怎么回事？这就是 JavaScript 中事件循环的用武之地！

## 引入事件循环

事件循环是在后台工作的队列，用于处理浏览器中的事件。当它处理一个我们注册到监听器的事件时，它给出一个 JavaScript 事件处理函数，比如我们的`handleClick`方法，一个包含事件细节的对象。然后，我们的事件处理函数可以将它用作参数之一。

但是如你所见，这不会马上发生。看来`handleClick`实际上只有在那个大 for 循环结束后才被调用。在我们数到十亿之后，事件循环将事件对象传递给`handleClick`。

事件循环有点像传送带，将盒子转储到机器中，这就是我们的 JavaScript 运行时。正如我们所见，运行时一次只能处理一段代码。所以如果它在运行一些代码，比如我们的 for 循环数到十亿，机器会给传送带亮红灯，这样它就不会再放更多的盒子进去了

[![Diagram of the for runtime machine with a click event box on the conveyor belt. Our for loop is running, so the JS runtime machine gives the event loop the red light on giving it more events](img/98bee2d7e44a6382159e5cf58674f52d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z4CJShtj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4uj1qkyx5vbwyycrtslo.jpeg)

但是一旦 for 循环完成，运行时机器就运行完了它拥有的所有代码，所以它给传送带一个绿灯，让它去另一个盒子。

[![With no code to run, the event loop conveyor belt has the green light to give the runtime an event](img/e9d49f5902824ee8995451a2b6fb4b17.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z77rkEH9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ml2ozgvaa4npb810lajr.jpeg)

然后机器会打开盒子，里面是我们的`MouseEvent`。这是一个针对`h1`的`click`事件，所以它运行的下一段代码是 h1 的 onclick，`handleClick`。一旦完成，运行时再次自由地从事件循环中取出另一个事件。

但是，即使我们的 JavaScript 代码正在运行，如果我们正在监听的其他事件发生了，比如另一次单击或窗口大小调整，这些事件也会进入事件循环，等待处理。

[![Adding another event onto the event loop conveyor belt by clicking](img/9d1e7f4812760f7cba42e61e54b5b2b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ao_-5BdS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/opactd1kp8cy88ymot0v.jpeg)

我的狗 Lola the Micropanda 也使用事件循环来尝试在后院追逐松鼠。她不擅长这个，但是你得尊重那个弗卢弗！

```
lola.onsquirrel = function(event) {
  lola.barkWhichIsReallyIneffectiveForSneakingUpOnASquirrel();

  lola.runTo(event.squirrelLocation);
}; 
```

Lola 注册了一个`onsquirrel`事件监听器，所以当她在院子里看到一只松鼠时，她会跑出去试图抓住松鼠，并立即开始吠叫，然后跑到松鼠所在的地方。即使她听到“晚餐时间！”，她不会跑回房子，直到她完成追逐松鼠，因为在运行时还有事情要做。相反，她将`DinnerTimeEvent`放在事件循环中，这样她就可以进来吃饭了。

[![Lola the Micropanda lying in the grass waiting for SquirrelEvents](img/05d0974e4c71cfcda730e22cf28fd925.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZcS-MaJz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/59bjum56q30xr9ukrxf8.jpg)

但实际上，事件循环是我们如何能够在一个 JavaScript web 应用程序中同时进行多件事情。JavaScript **运行时**一次只能做一件事，但是像视频流、获取 GPS 位置更新和鼠标点击这样的动作都在运行时之外**，所以像大下载这样缓慢的事情不会阻塞我们的单个 JavaScript 线程。这意味着将事件放入事件循环是外部世界与运行时通信的方式，因此您的 web 应用程序可以响应事件。**

我们从事件循环中得到的另一件事是，因为它为 JavaScript 提供了一种一次只做一件事的方法，这意味着我们不需要做任何特殊的事情来确保，例如，两个改变同一个变量的 JavaScript 函数不会同时运行。在允许你一次做多件事的语言中，比如 C 或 Go，虽然并行运行代码通常会使你的程序更快，但是并发代码很难调试。

## 什么是 JavaScript 事件？

我们已经看到了事件循环，现在让我们来看看事件本身。正如我们在示例代码中看到的，我们能够向我们的`onclick`回调添加一个`event`参数，当我们点击树懒唤醒它时，`event`不是`undefined`。它是一个对象，也是一个非常详细的对象，包含了你想知道的关于鼠标点击的一切。

您看到的事件是一个`MouseEvent`，但是还有许多其他类型的事件，事件循环可以发送到您的回调函数。例如，有`KeyboardEvent`用于按键之类的事情。对于平板电脑和手机，还有`DeviceOrientationEvent`，它会告诉你用户拿着设备的方向。在 [MDN 事件文档](https://developer.mozilla.org/en-US/docs/Web/API/Event#Introduction)中有一个很大的事件列表，但是不要被淹没。需要知道的主要事情是，所有这些事件都是表示您的 web 应用程序上“发生了什么”的数据，因此您的 JS 代码可以对其做出响应。

所有这些事件对象都继承自`Event`原型，所以它们都有一些共同的结构。我们在`handleClick`函数中登录的`MouseEvent`有`KeyboardEvent`没有的`pageX`和`pageY`值，但是鼠标和键盘事件都有来自`Event`原型的数据。例如，事件有一个描述事件的`type`，如“click”、“keypress”或“keyup ”,这样我们可以监听该类型的事件，还有一个`target`,告诉您 DOM 中的哪个对象(像 HTML 元素)调度了该事件。

所有这些事件类型最终都只是来自运行时外部的普通 JavaScript 对象。事实上，如果你进入浏览器控制台并输入:`new MouseEvent('How do you do fellow mouse events?', {screenX: 250, screenY: 250});`，你会看到一个鼠标事件被记录下来。

[![Our "MouseEvent" in the browser console](img/0f36ce970f19fbabf88fcf98786a1ceb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cLRYHrY---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6molhoai6gs42asnel92.png)

这是因为您正在调用 MouseEvent 构造函数来创建一个对象，该对象表示您的鼠标以某种方式与屏幕上的 pixel (250，250)进行交互。这种交互实际上并没有发生，但它只是一个`MouseEvent`对象，就像一次真正的点击所引起的事件循环一样！有趣的是，因为我们通过了“你好，老鼠伙伴们？”作为 MouseEvent 构造函数的第一个参数，“你好，鼠标事件伙伴？”是鼠标事件的`type`，而不是常规鼠标事件的类型，比如`click`或`mouseover`。

另一件要注意的事情是，并不是 JavaScript 中所有异步动作的回调都与`Event` s 一起工作。一个`Event`对象是我们正在监听的发生的事情的表示，但它不是表示发生了什么的唯一方式。例如，如果在你的控制台上运行:

```
setTimeout(function(event){
  console.log(event);
}, 500); 
```

然后在半秒钟内，即使来自事件循环的任务进入运行时来启动回调，您也不会记录任何`Event`，记录的只是`undefined`。对`setTimeout`的响应是只运行你传入的函数，因为除了“半秒已过”之外，没有任何关于事件的数据要表示。

可以用来构建 Google Chrome 扩展的 JavaScript APIs 采用了另一种方法；您注册回调来监听诸如打开标签页或开始下载之类的事情，这一次我们确实有关于发生了什么的数据进入回调。

```
chrome.downloads.onCreated.addListener(function(downloadItem) {
  alert(`Now downloading from ${downloadItem.url}`);
}); 
```

代码看起来像我们在处理一个事件，比如我们的 click 事件，但是我们在回调中得到的对象是**而不是**一种 DOM `Event`，它是一个 Google Chrome `DownloadItem`，描述了正在进行的下载。然而，在所有这些情况下，我们的`handleClick`函数、我们的`setTimeout`回调函数和我们的 Chrome 扩展下载回调函数都会监听某种事件的发生，它们只是用不同的数据进行不同的处理。

我们已经了解了 JavaScript 中的事件循环是什么以及如何使用它，我们还仔细研究了这些 JS 事件对象以及它们如何表示所发生的事情。我希望这有助于你理解事件背后发生的事情。了解了这些概念以及它们是如何处理回调的，我建议构建一些处理事件的 web 应用程序，并检查一下[承诺](https://developers.google.com/web/fundamentals/primers/promises)，这是响应异步动作的另一种流行方式。

下次见，继续偷懒！

[![Two-toed sloth sleeping](img/a262695aac645e425437356891f1f2a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0d9POoY---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2tlq8j72y295ggqsk324.jpeg)

图像为 reference nastosanics[cc-by 2.0](https://creativecommons.org/licenses/by/2.0/)的图像