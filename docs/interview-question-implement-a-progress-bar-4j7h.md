# 面试问题:实现一个进度条

> 原文：<https://dev.to/healeycodes/interview-question-implement-a-progress-bar-4j7h>

我看到这个问题在社交媒体上流传。[显然](https://www.reddit.com/r/javascript/comments/3rb88w/ten_questions_ive_been_asked_most_more_than_once/cwmn60t/)、[顶级公司](https://www.glassdoor.com/Interview/-1-Implement-a-loading-bar-that-animates-from-0-to-100-in-3-seconds-2-Start-loading-bar-animation-upon-a-button-cli-QTN_1393903.htm)都在用它来筛选前端工程师。我的未婚妻很快就要申请工作了，我让她试一试。她几乎完成了最后一个阶段(做了一点研究),但是一个棘手的递归错误把她绊倒了。我写了这篇文章来帮助她。如果你面临类似的主题问题，希望你会发现这很有用！

有一些变化，但这个问题通常是分阶段问的，越来越难。

#### 1。实现一个加载栏，在 3 秒钟内从 0 到 100%显示动画

这完全可以用 CSS 来完成。如果有些事情可以完全用 CSS 来完成，我倾向于选择 CSS。我的理由是，重构纯 CSS 的东西比试图扩展一个快速的 JavaScript 黑客更容易。CSS 具有很强的声明性，很容易阅读和理解其内部发生的事情。

对于一个只有 CSS 的进度条，我将使用两个 div——一个容器和进度条——和`keyframes`。这里重要的一行是`animation: 1s linear fill;`。有很多话要说。我们要用什么`transition-timing-function`——`ease`、`linear`、a `cubic-bezier`？

至少，这个快速回答表明你知道什么`keyframes`并且你可以在基础水平上使用它。

```
<div class="container">
  <div class="progress-bar"></div>
</div> 
```

```
.container {
  width: 300px;
  height: 50px;
  background-color: #D3D3D3;
}

.progress-bar {
  width: 100%;
  height: 50px;
  background-color: #90EE90;
  animation: 3s linear fill;
}

@keyframes fill {
    0% {
        width: 0%;
    }
    100% {
        width: 100%;
    }
} 
```

[https://codepen.io/healeycodes/embed/pmKQzM?height=600&default-tab=result&embed-version=2](https://codepen.io/healeycodes/embed/pmKQzM?height=600&default-tab=result&embed-version=2)

#### 2。单击按钮时开始加载条形动画

现在我们正在进入 JavaScript 领域。我一直认为`transition`非常棒，所以我将使用 JavaScript 为`progress-bar`元素添加一个类。它使你能够“定义一个元素的两个状态之间的转换” [(MDN)](https://developer.mozilla.org/en-US/docs/Web/CSS/transition) 。

我确保缓存对加载栏的引用。我在智能电视上做了很多工作，缓存是我们用来保持一切流畅的许多技巧之一。HTML 元素有 [Element#classList](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList) ，这是一个很棒的与类交互的 API。用起来很安全。您可以添加多个类，它只会添加一个实例，您也可以删除不存在的类，而不会出现任何错误。

[classList#toggle](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList#Methods) 特别有用。"当只有一个参数时:切换类值；即，如果该类存在，则删除它并返回 false，如果不存在，则添加它并返回 true" [(MDN)](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList#Methods) 。

```
<div class="container">
  <div class="progress-bar"></div>
</div>
<button onclick="loadBar()">Load</button> 
```

```
.container {
  width: 300px;
  height: 50px;
  background-color: #D3D3D3;
}

.progress-bar {
  width: 0%;
  height: 50px;
  background-color: #90EE90;
  transition: width 3s linear;
}

.load {
  width: 100%;
} 
```

```
const bar = document.querySelector('.progress-bar');

function loadBar () {
  bar.classList.add('load');
} 
```

[https://codepen.io/healeycodes/embed/MdXzqb?height=600&default-tab=result&embed-version=2](https://codepen.io/healeycodes/embed/MdXzqb?height=600&default-tab=result&embed-version=2)

#### 3。如果按钮被点击多次，将多个装载栏排队。装载栏 N 开始动画，装载栏 N-1 完成动画。

这里变得更有趣了。有一种方法可以通过删除和添加类来迭代我们之前的解决方案，但这感觉很糟糕。我认为目的是让你在这里使用更多的 JavaScript。技术面试问题其实没有终点。总是有限制、扩展和假设可以抛出。在继续阅读之前，我想看看你有什么想法👀。

这里有几个陷阱。你必须确保所用的时间正好是三秒，而不是多一秒或少一秒。一只扁虱的长度是多少？我想这取决于你的酒吧有多宽。每次上涨 1%似乎是一个最佳时机。注意:管理自己的状态比依赖 DOM 更好。

缓存在这里非常重要。您不希望一秒钟内遍历 DOM 33 次来查找一个元素。干净的代码可能需要两个函数。我使用了一个全局标志来跟踪进度条是否在运行。我们想添加一个到队列中，但我们不希望两个同时进行，否则我们的酒吧将加载两倍的速度！

```
<div class="container">
  <div class="progress-bar"></div>
</div>
<div>Queued bars: <span class="queued">0</span></div>
<button onclick="loadBar()">Load</button> 
```

```
.container {
  width: 300px;
  height: 50px;
  background-color: #D3D3D3;
}

.progress-bar {
  width: 0%;
  height: 50px;
  background-color: #90EE90;
} 
```

```
const bar = document.querySelector('.progress-bar');
const queued = document.querySelector('.queued');

let loader = false;
let width = 0;
let count = 0;

function loadBar() {
  queued.innerText = ++count;
  if (loader === false) {
    bar.style.width = 0;
    tick();
  }
}

function tick() {
  loader = true;
  if (++width > 100) {
    queued.innerText = --count;
    width = 0;
    if (count < 1) {
      loader = false;
      return;
    }
  }
  bar.style.width = `${width}%`;
  setTimeout(tick, 30);
} 
```

[https://codepen.io/healeycodes/embed/ZNRVBa?height=600&default-tab=result&embed-version=2](https://codepen.io/healeycodes/embed/ZNRVBa?height=600&default-tab=result&embed-version=2)

#### 4。做同样的事情，但没有计时器！

好吧，他们在采访中并没有真正问这个问题，但是有人在 DEV 的评论中提到了`requestAnimationFrame`,我认为在模仿之前的答案的同时用它来构建一个例子会很有趣。

如果不需要排队装载酒吧，逻辑会短得多。我最终决定使用两个耦合的函数。最近，我看到有人说任何一个`else`的实例都是重构的机会。我一直在思考这个问题，虽然没有一条规则是正确的，但它最近一直在影响我如何塑造函数。看看这个。

```
<div class="container">
  <div class="progress-bar"></div>
</div>
<div>Queued bars: <span class="queued">0</span></div>
<button onclick="loadBar(1)">Load</button> 
```

```
.container {
  width: 300px;
  height: 50px;
  background-color: #D3D3D3;
}

.progress-bar {
  width: 0%;
  height: 50px;
  background-color: #90EE90;
} 
```

```
const bar = document.querySelector('.progress-bar');
const queued = document.querySelector('.queued');

let loading = false;
let count = 0;

function tick (timestamp, dist, duration) {
  const runtime = timestamp - starttime;
  let progress = runtime / duration;
  progress = Math.min(progress, 1);
  bar.style.width = `${dist * progress}%`;
  if (runtime > duration) {
    loading = false;
    count--;
    loadBar(0);
    return;
  }
  requestAnimationFrame (function(timestamp) {
      tick(timestamp, dist, duration)
  });
}

function loadBar (increment) {
  count += increment;
  queued.innerText = count;
  if (loading === true || count < 1) { return; }
  bar.style.width = 0;
  loading = true;
  requestAnimationFrame (function(timestamp) {
    starttime = timestamp;
    tick(timestamp, 100, 3000);
  });
} 
```

[https://codepen.io/healeycodes/embed/EzpJNo?height=600&default-tab=result&embed-version=2](https://codepen.io/healeycodes/embed/EzpJNo?height=600&default-tab=result&embed-version=2)

### 结束注释

如果你整篇文章都在对我大喊大叫，那就是高分。是的，这个[元素](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/progress)从 HTML5 开始就存在了。您可以通过设置两个属性`max`和`value`来管理它。CSS Tricks 有一篇[文章](https://css-tricks.com/html5-progress-element/)介绍如何对其进行样式化和动画化。它们还涵盖了不同的状态，确定的和不确定的——后者意味着“未知的进展”。这些状态很棒，因为它们给了我们一种与用户交流的方式。

这个面试问题不是关于完美的答案，而是关于你如何在面试过程中表达你的想法，以及你提出的问题。是否也应该有百分比的数字表示？这是在低功耗设备上运行吗？如果是这样，不要以百分之一的增量上升。也许五个或十个更好。

我认为一个好的扩展可能是要求受访者构建一个接口，该接口接收描述进度状态的 WebSocket 消息并将其传达给用户。

如何看待这个面试问题？对你来说，它是否在解决问题和浏览器知识的十字路口相遇？

* * *

加入我的关于编程和个人成长的[时事通讯](https://buttondown.email/healeycodes)的 150 多人注册！

我发关于科技的微博。