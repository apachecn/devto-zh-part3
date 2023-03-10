# 弗雷迪 vs JSON:如何制作一个自上而下的射手

> 原文：<https://dev.to/koehr/freddy-vs-json-how-to-make-a-top-down-shooter-50lg>

我将告诉你我是如何在不使用任何额外库的情况下用 JavaScript 创建了一个简单的自顶向下的射手。但是这篇文章并不是复制完整的游戏，而是试图展示从零开始编写游戏的步骤。

* * *

这是来自我个人博客的交叉帖子: [https://koehr.tech](//koehr.tech)

* * *

几年前(哦，快十年了！我已经那么老了吗？)，当 Canvas API 被大多数浏览器广泛采用时，我开始尝试它。这种魅力很高，我立即尝试将其用于互动玩具和游戏。

当然，我制作(和制作)的游戏通常都不是很精良。这主要是因为我创作它们只是为了好玩，没有太多的视觉享受甚至声音。真正让我着迷的是底层的机制。否则，我可以只使用那些已经存在的[令人敬畏的游戏引擎](https://github.com/collections/javascript-game-engines)。

为了分享其中的乐趣，我在公司的一次技术会议上制作了一个小小的自上而下的射手([我们正在招聘，顺便说一下](https://wunder.dog))。[的结果可以在 Github](https://github.com/nkoehring/FreddyvsJSON) 上找到。我对代码做了很好的注释，所以阅读它应该会很有帮助。但是如果你想知道我是如何一步一步创造出这个游戏的，这篇文章就是为你准备的。

## 游戏

给你一个我创造的印象:

[![screenshot](img/5518a88afc3d431cf28ad7dc8f12ad55.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QzZTp3JO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/frub6slks3h4e6wtjk5k.jpg)

灰色的小盒子是你的船。你可以用 WASD 或箭头键控制灰色小盒子，你可以通过按空格键或回车键向你的敌人发射黄色小盒子——红色盒子。但是敌人会反击。他们并没有真正瞄准好，但在某些时候，他们会用红色的小盒子淹没屏幕。如果他们打你，他们会受伤。每次受伤，你都会退缩，直到完全消失。同样的事情也会发生在你的对手身上。

## 前提条件

这篇文章不是关于游戏本身，而是关于游戏的基本机制和一些技巧。我的意图是为有一些现有编程经验的人提供一个了解更复杂游戏开发的入口。以下事情有助于全面理解一切:

### 基础游戏引擎机制

大多数(如果不是全部的话)游戏引擎都有相同的基本构件:

*   `state`，定义当前情况(如主菜单、游戏运行、游戏失败、游戏获胜等)。
*   存储所有对象和相关数据的地方。
*   通常每秒运行 60 次的`main loop`，读取对象信息，绘制屏幕，并对对象数据进行更新
*   一个将按键、鼠标移动和点击映射到数据变化的`event handler`。

### 画布元素

Canvas 元素允许您直接在浏览器中处理基于像素的数据。它提供了一些绘制图元的函数。例如，画一个蓝色的矩形很容易，但是画一个三角形你需要不止一个动作；要画一个圆，你需要知道如何使用圆弧。

正是因为用 Canvas API 绘制矩形是最简单快捷的事情，所以我在《Freddy vs JSON》中的所有内容中都使用了它们。这使得绘制更令人兴奋的图案或图形的复杂性远离，并有助于专注于实际的游戏机制。这意味着，在初始化画布之后，除了设置颜色，我们只使用两个函数:

```
const ctx = canvas.getContext('2d') // this is the graphics context
ctx.fillStyle = '#123456'           // use color #123456

ctx.fillText(text, x, y)            // write 'text' at coords x, y
ctx.fillRect(x, y, width, height)   // draw filled rectangle 
```

Enter fullscreen mode Exit fullscreen mode

## 第一步:一些 HTML 和一个初始化的画布

因为代码将在浏览器中运行，所以一些 HTML 是必要的。最小的集合应该是下面两行:

```
<canvas id="canvas" />
<script src="./app.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

这很有效，但当然一些造型会很棒。或许还有一个头衔？在 Github 上查看[的完整版本。](https://github.com/nkoehring/FreddyvsJSON/blob/master/index.html)

初始化画布也非常简单。在`app.js`中，下列行是必需的:

```
const canvas = document.getElementById('canvas')
// you can set height and width in HTML, too
canvas.width = 960
canvas.height = 540
const ctx = canvas.getContext('2d') 
```

Enter fullscreen mode Exit fullscreen mode

我为宽度和高度选择了相当随意的值。你可以根据自己的喜好随意改变它们。只要知道较高的值显然会导致更多的工作为您的计算机。

## 第二步:游戏模式/状态

为了避免产生[大泥球](http://en.wikipedia.org/wiki/Big_ball_of_mud)，通常使用[状态机](https://en.wikipedia.org/wiki/Finite-state_machine)。其思想是描述高级状态及其有效转换，并使用中央状态处理器来控制它们。

这些库有助于状态机，但是自己创建也不太难。在我创建的游戏中，我使用了一个非常简单的状态机实现:可能的状态及其转换在[类枚举对象](https://en.wikipedia.org/wiki/Enumerated_type)中描述。这里有一些代码来说明这个想法。代码使用了一些相当新的语言特性:[符号](https://developer.mozilla.org/en-US/docs/Glossary/Symbol)和[计算属性名](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names)。

```
const STATE = {
  start: Symbol('start'),  // the welcome screen
  game: Symbol('game'),    // the actual game
  pause: Symbol('pause'),  // paused game
  end: Symbol('end')       // after losing the game
}

const STATE_TRANSITION = {
  [STATE.start]: STATE.game, // Welcome screen => Game
  [STATE.game]: STATE.pause, // Game => Pause
  [STATE.pause]: STATE.game, // Pause => Game
  [STATE.end]: STATE.start   // End screen => Welcome screen
} 
```

Enter fullscreen mode Exit fullscreen mode

这不是一个完整的状态机，但可以完成这项工作。为了简单起见，我在一个场合违反了状态机:没有从游戏运行到游戏结束的过渡。这意味着我必须在玩家死亡后直接跳到结束屏幕，而不需要使用状态处理程序。但是这让我避免了很多复杂的事情。现在状态控制逻辑有效的只有一行:

```
newState = STATE_TRANSITION[currentState] 
```

Enter fullscreen mode Exit fullscreen mode

Freddy vs JSON 在点击处理程序中使用了这个。点击画布会将状态从欢迎屏幕切换到实际游戏，暂停和取消暂停游戏，并在失败后将您带回欢迎屏幕。所有这些都在一行中。新状态被设置为中央更新循环所关注的变量。稍后会详细介绍。

当然，国家可以做更多的事情。例如，可以实现武器或舰船的升级。游戏可以过渡到更高的难度水平，并获得特殊的游戏状态，如升级商店或转移阶段之间的动画。你的想象力是极限。和你的状态处理程序中的行数。

## 步骤三:数据处理

游戏通常要处理大量的信息。一些例子是玩家的位置和健康状况，每个敌人的位置和健康状况，每个正在飞来飞去的子弹的位置和玩家到目前为止击中的数量。

JavaScript 允许不同的方式来处理这个问题。当然，国家可以是全球性的。但我们都(应该)知道，全局变量是万恶之源。全局常量是可以的，因为它们是可预测的。只是不要使用全局变量。如果你仍然不相信，请阅读 stackexchange 上的这个[条目。](https://softwareengineering.stackexchange.com/questions/148108/why-is-global-state-so-evil)

代替全局变量，你可以把所有东西放在同一个范围内。下面是一个简单的例子。下面的代码示例使用模板文本，这是一种新的语言功能。[在这里了解更多关于模板文字的信息](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)。

```
function Game (canvas) {  // the scope
  const ctx = canvas.getContext('2d')
  const playerMaxHealth = 10
  let playerHealth = 10

  function handleThings () {
    ctx.fillText(`HP: ${playerHealth} / ${playerMaxHealth}`, 10, 10)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这很好，因为你不用真正使用全局变量就可以轻松访问全局变量。如果你对每件事都只有一个大的范围，它仍然为潜在的问题打开了大门，但是第一个游戏可能足够小，不用想太多。

另一种方法是使用类:

```
class Game {
  constructor (canvas) {
    this.ctx = canvas.getContext('2d')
    this.playerMaxHealth = 10
    this.playerHealth = 10
  }

  handleThings () {
    const max = this.playerMaxHealth
    const hp = this.playerHealth
    ctx.fillText(`HP: ${hp} / ${max}`, 10, 10)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来有点像样板文件，但是类可以很好地封装公共功能。如果你的游戏越来越好，你想保持理智，他们会变得更好。但是在 JavaScript 中，它们只是语法上的糖。一切都可以用函数和函数作用域来实现。所以你用什么取决于你自己。最后两个代码示例本质上是一样的。

既然我们决定了如何保存所有的数据(Freddy vs JSON 使用了一个类，所以我在这里也将使用类),我们可以进一步构造它...或者不是。弗雷迪 vs JSON 拯救一切平淡。例如，这意味着每个玩家属性都有自己的变量，而不是使用包含许多属性的玩家对象。后者可能可读性更强，所以你可能想走这条路。如今对象访问也相当快，所以如果你写`this.player.health`而不是`this.playerHealth`，可能没有明显的区别。如果您真的很重视性能，那么您可能想进一步研究这个主题。你可以从我的 [jsperf 实验](https://jsperf.com/nested-and-flat-property-access/1)开始。

数据操作发生在更新循环或处理事件时。接下来的步骤将进一步解释这些主题。

## 第四步:主循环

如果基于事件的改变足够了，就像在一个网站上，一个单独的循环就没有必要了。用户单击某个地方，这将触发一个事件来更新某些内容，并最终重新呈现页面的一部分。但是在游戏中，有些事情的发生并不需要用户的直接互动。敌人进入场景并向你射击，可能会有一些背景动画，音乐播放，等等。为了让这一切成为可能，游戏需要一个不断运行的循环，反复调用一个函数来检查和更新所有东西的状态。为了让事情非常顺利，它应该以一致的间隔调用这个函数——每秒至少 30 次，最好 60 次。

下面的代码示例使用了另一个相当新的语言特性，叫做[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)。

定期运行功能的典型方法包括使用`setInterval` :

```
let someValue = 23
setInterval(() => {
  someValue++
}, 16) 
```

Enter fullscreen mode Exit fullscreen mode

或者`setTimeout`

```
let someValue = 42

function update () {
  someValue++
  setTimeout(update, 16)
}

update() 
```

Enter fullscreen mode Exit fullscreen mode

第一个版本只是每 16 毫秒无休止地运行一次函数(每秒 62.5 次)，不管函数本身需要多少时间，或者是否已经完成。第二个版本在它设置一个定时器在 16 毫秒后再次启动它自己之前做它潜在的长时间运行的工作。

第一个版本尤其成问题。如果一次运行需要的时间超过 16 毫秒，那么在第一次运行完成之前，它会再运行一次，这可能会带来很多乐趣，但不一定会带来任何有用的结果。第二个版本在这里显然更好，因为它只在做了所有其他事情之后设置下一个超时。但是仍然有一个问题:不管函数需要运行多长时间，它都要再等待 16 毫秒才能再次运行函数。

为了减轻这种情况，函数需要知道完成它的工作需要多长时间，然后从等待时间中减去这个值:

```
let lastRun
let someValue = 42

function update () {
  someValue++
  const duration = Date.now() - lastRun
  const time = duration > 16 ? 0 : 16 - time
  setTimeout(update, time)
  lastRun = Date.now()
}

lastRun = Date.now()
update() 
```

Enter fullscreen mode Exit fullscreen mode

`Date.now()`返回以毫秒为单位的当前时间。有了这些信息，我们可以计算出自上次运行以来已经过了多长时间。如果从那时起已经过了超过 16 毫秒，就立即开始更新并摧毁那台可怜的计算机(或者更好地减慢执行时间并对计算机友好)，否则只要有必要就等待，保持在每秒 60 次运行左右。

请注意，Date.now()不是衡量性能的最佳方式。要了解有关性能和高分辨率时间测量的更多信息，请查看:[https://developer . Mozilla . org/en-US/docs/Web/API/Performance](https://developer.mozilla.org/en-US/docs/Web/API/Performance)

酷毙了。这样，通过将时间间隔设置为 33 毫秒，你也可以将一切都放慢到每秒 30 帧。但是我们不要走那条路。让我们做那些酷孩子用他们闪亮的新浏览器做的事情。让我们使用 [requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) 。

将你的更新函数作为一个参数，并在下次重画之前调用它。它还为您提供了最后一次调用的时间戳，因此您不必请求另一次调用，这可能会影响您的性能。让我们进入细节:

```
function update () {
  /* do some heavy calculations */
  requestAnimationFrame(update)
}

update() 
```

Enter fullscreen mode Exit fullscreen mode

这是最简单的版本。它尽可能在下次重画时运行你的更新函数。这意味着它通常每秒运行 60 次，但速度可能会有所不同，这取决于运行它的计算机的屏幕刷新率。如果你的函数花费的时间超过了屏幕刷新的间隔时间，它会简单地跳过一些重画，因为它在完成之前不会要求重画。这样，它将始终与刷新率保持一致。

做很多事情的函数可能不需要经常运行。每秒 30 次通常足以让事情看起来很顺利，其他一些计算可能不需要每次都进行。这让我们回到了之前的定时函数。在这个版本中，我们使用调用函数
时`requestAnimationFrame`给我们的时间戳

```
let lastRun

function update (stamp) {
  /* heavy work here */
  lastRun = stamp

  // maybe 30fps are enough so the code has 33ms to do its work
  if (stamp - lastRun >= 33) {
    requestAnimationFrame(update)
  }
}

// makes sure the function gets a timestamp
requestAnimationFrame(update) 
```

Enter fullscreen mode Exit fullscreen mode

## 步骤五:事件处理

人们通常希望感觉自己在控制自己的所作所为。这就把我们带到了游戏需要处理用户输入的地方。输入可以是鼠标移动、鼠标点击或按键。按键也分为按下和释放按键。我将在本节稍后解释原因。

如果你的游戏是那个页面上唯一运行的东西(它值得那么多关注，不是吗？)输入事件可以简单地绑定到`document`。否则，它们需要直接绑定到画布事件。后者对于按键事件可能更复杂，因为按键事件最适合实际输入字段。这意味着您需要在页面中插入一个，并确保它保持焦点，以便它获得事件。每次点击画布都会使它失去焦点。为了避免这种情况，你可以使用下面的黑客:

```
inputElement.onblur = () => inputElement.focus() 
```

Enter fullscreen mode Exit fullscreen mode

或者简单地将所有内容放入自己的页面，并将事件监听器绑定到`document`。这让你的生活变得轻松多了。

旁注:人们可能想知道为什么我不使用 addEventListener。如果它能让你感觉更好，请使用它。为了简单起见，我不在这里使用它，只要每个元素对于每个事件类型都有一个事件侦听器，这就不是问题。

### 鼠标移动

鼠标移动在弗雷迪大战 JSON 中并没有真正使用，但是如果不解释一下，这篇文章就不完整。这就是你的做法:

```
canvas.onmousemove = mouseMoveEvent => {
  doSomethingWithThat(mouseMoveEvent)
} 
```

Enter fullscreen mode Exit fullscreen mode

只要鼠标在画布上，它的每一个小动作都会被执行。通常你想要[去抖](https://davidwalsh.name/javascript-debounce-function)那个事件处理器，因为事件可能会以疯狂的速度触发。另一种方法是只在非常简单的情况下使用它，比如保存鼠标坐标。这些信息可以用在与事件触发无关的函数中，比如我们的更新函数:

```
class Game {
  constructor (canvas) {
    // don't forget to set canvas width and height,
    // if you don't do it, it will set to rather
    // small default values
    this.ctx = canvas.getContext('2d')
    this.mouseX = 0
    this.mouseY = 0

    // gets called at every little mouse movement
    canvas.onmousemove = event => {
      this.mouseX = event.offsetX
      this.mouseY = event.offsetY
    }

    this.update()
  }

  // gets called at each repaint
  update () {
    requestAnimationFrame(() => this.update())
    this.fillRect('green', this.mouseX, this.mouseY, 2, 2)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

鼠标事件对象包含了更多有用的信息。我建议你去看看这个链接，了解一下。

这将在你用鼠标触摸画布的任何地方画出两个像素宽的方框。是的，一个十行的绘图程序！Photoshop 我们来找你了！

### 鼠标点击

但是让我们回到现实。鼠标点击是另一个重要的交互:

```
canvas.onclick = mouseClickEvent => {
  doSomethingWithThat(mouseClickEvent)
} 
```

Enter fullscreen mode Exit fullscreen mode

事件对象再次包含各种有用的信息。这和你从鼠标移动中得到的是同一类型的物体。让生活更简单，不是吗？

现在，为了利用鼠标点击，让我们改写前面的代码示例:

```
class Game {
  constructor (canvas) {
    // set canvas.width and canvas.height here
    this.ctx = canvas.getContext('2d')
    this.mouseX = 0
    this.mouseY = 0
    this.drawing = false

    canvas.onmousemove = event => {
      this.mouseX = event.offsetX
      this.mouseY = event.offsetY
    }
    canvas.onmousedown = () => {
      this.drawing = true
    }
    canvas.onmouseup = () => {
      this.drawing = false
    }

    this.update()
  }

  update () {
    requestAnimationFrame(() => this.update())
    if (this.drawing) {
      this.fillRect('green', this.mouseX, this.mouseY, 2, 2)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[在 CodeSandbox 上查看一下](https://codesandbox.io/s/3qw6q7j535)

现在，只有在按住鼠标键的情况下才能绘制方框。嘣，离 Photoshop 的易用性又近了一步！你已经可以用它做什么了，这太不可思议了。看看这件不可思议的艺术品:

[![incredible piece of art](img/d13b1a600ac4dc6a145c29e12a6f6624.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WwGj6if9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qc2qr2d0d7c1kta6vcy2.jpg)

### 关键事件

最后一个重要的输入来自按键。好吧，这不是最后一种输入类型。其他的将来自操纵杆或游戏手柄。但是有一些像我一样的老派人仍然喜欢使用键盘来驾驶他们的飞船。

输入处理理论上很简单，但实际上却很难。这就是为什么本节不仅解释了关键事件是如何工作的，还解释了如何正确处理它们。期待事件处理，速度和加速度之间的关系，以及帧速率不可知的定时...

按键事件处理的最简单版本如下:

```
document.onkeypress = keyPressEvent => {
  doSomethingWithThat(keyPressEvent)
} 
```

Enter fullscreen mode Exit fullscreen mode

但是`keypress`被弃用，不应该使用。无论如何，最好将`keyPress`分成两个事件:`KeyDown`和`KeyUp`，我会解释为什么。

现在想象一下，如果用户按下`d`或`ArrowRight` :
，屏幕中间有一艘令人敬畏的宇宙飞船，你想让它飞向右边

```
class Game {
  constructor(canvas, width, height) {
    // we'll need those values
    this.width = canvas.width = width;
    this.height = canvas.height = height;
    this.ctx = canvas.getContext("2d");

    this.shipSize = 10;
    this.shipHalf = this.shipSize / 2.0; // you'll need that a lot

    // position the ship in the center of the canvas
    this.shipX = width / 2.0 - this.shipHalf;
    this.shipY = height / 2.0 - this.shipHalf;

    // event is a KeyboardEvent:
    // https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent
    document.onkeypress = event => {
      const key = event.key;
      if (key === "d" || key === "ArrowRight") {
        this.shipX++;
      }
    };

    this.update();
  }

  // convenience matters
  rect(color, x, y, w, h) {
    this.ctx.fillStyle = color;
    this.ctx.fillRect(x, y, w, h);
  }

  update() {
    // clean the canvas
    this.rect("black", 0, 0, this.width, this.height);

    // get everything we need to draw the ship
    const size = this.shipSize;
    const x = this.shipX - this.shipHalf;
    const y = this.shipY - this.shipHalf;

    // draw the ship
    this.rect("green", x, y, size, size);

    // redraw as fast as it makes sense
    requestAnimationFrame(() => this.update());
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[在 CodeSandbox 上查看一下](https://codesandbox.io/s/2w10vo897n)

好吧，这还有点用，至少如果你按下`d`。但是箭头键不知何故不工作，船的运动感觉有点跳动。那似乎不是最佳的。

问题是我们依赖重复的关键事件。如果您按住一个键，`keypress`事件每秒钟重复几次，这取决于您如何设置键重复率。没有办法用它来实现流畅的移动，因为我们无法知道用户重复按键的速度。当然，我们可以尝试测量重复率，希望用户按住键的时间足够长。但是让我们试着变得更聪明些。

让我们总结一下:我们拿着钥匙，船移动。我们把钥匙留下，运动就停止了。这就是我们想要的。这两件事发生得多么巧合啊...环境资源管理（Environmental Resources Management 的缩写）..事件:

```
class Game {
  constructor(canvas, width, height) {
    // we'll need those values
    this.width = canvas.width = width;
    this.height = canvas.height = height;
    this.ctx = canvas.getContext("2d");

    this.shipSize = 10;
    this.shipHalf = this.shipSize / 2.0; // you'll need that a lot

    // position the ship in the center of the canvas
    this.shipX = width / 2.0 - this.shipHalf;
    this.shipY = height / 2.0 - this.shipHalf;

    this.shipMoves = false;

    // key is pressed down
    document.onkeydown = event => {
      const key = event.key;
      switch (key) {
        case "d":
        case "ArrowRight":
          this.shipMoves = "right";
          break;
        case "a":
        case "ArrowLeft":
          this.shipMoves = "left";
          break;
        case "w":
        case "ArrowUp":
          this.shipMoves = "up";
          break;
        case "s":
        case "ArrowDown":
          this.shipMoves = "down";
          break;
      }
    };

    document.onkeyup = () => {
      this.shipMoves = false;
    };

    this.update();
  }

  // convenience matters
  rect(color, x, y, w, h) {
    this.ctx.fillStyle = color;
    this.ctx.fillRect(x, y, w, h);
  }

  update() {
    // move the ship
    if (this.shipMoves) {
      if (this.shipMoves === "right") this.shipX++;
      else if (this.shipMoves === "left") this.shipX--;
      else if (this.shipMoves === "up") this.shipY--;
      else if (this.shipMoves === "down") this.shipY++;
    }

    // clean the canvas
    this.rect("black", 0, 0, this.width, this.height);

    // get everything we need to draw the ship
    const size = this.shipSize;
    const x = this.shipX - this.shipHalf;
    const y = this.shipY - this.shipHalf;

    // draw the ship
    this.rect("green", x, y, size, size);

    // redraw as fast as it makes sense
    requestAnimationFrame(() => this.update());
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[在 CodeSandbox 上查看一下](https://codesandbox.io/s/nr8r6myz60)

我想马上加上所有的方向。现在运动本身与关键事件分离了。不是在每个事件上直接改变坐标，而是为运动方向设置一个值，主循环负责调整坐标。这很好，因为我们不再关心任何关键的重复率了。

但是这里仍然存在一些问题。首先，飞船一次只能向一个方向移动。相反，它应该总是能够同时向两个方向移动，比如向左和向上。然后，如果从一个键到另一个键的切换太快，运动就会停止。这可能发生在你的船和敌人的子弹之间的激烈情况下。此外，运动也与帧速率有关。如果玩家计算机上的帧速率下降或者屏幕以不同的速率刷新，你的船会变慢或变快。最后但并非最不重要的是，船只是跳到全速，然后回到零。为了更自然的感觉，它应该加速和减速。

很多工作。让我们一个一个地解决问题:

双向运动很容易做到。我们只需要第二个变量。为了进一步简化，我们可以将这些变量设置为数字，而不是标识字符串。在这里你可以看到原因:

```
class Game {
  constructor(canvas, width, height) {
    /* ... same as before ... */

    this.shipMovesHorizontal = 0;
    this.shipMovesVertical = 0;

    // this time, the values are either positive or negative
    // depending on the movement direction
    document.onkeydown = event => {
      const key = event.key;
      switch (key) {
        case "d":
        case "ArrowRight":
          this.shipMovesHorizontal = 1;
          break;
        case "a":
        case "ArrowLeft":
          this.shipMovesHorizontal = -1;
          break;
        case "w":
        case "ArrowUp":
          this.shipMovesVertical = -1;
          break;
        case "s":
        case "ArrowDown":
          this.shipMovesVertical = 1;
          break;
      }
    };

    // to make this work, we need to reset movement
    // but this time depending on the keys
    document.onkeyup = event => {
      const key = event.key;
      switch (key) {
        case "d":
        case "ArrowRight":
        case "a":
        case "ArrowLeft":
          this.shipMovesHorizontal = 0;
          break;
        case "w":
        case "ArrowUp":
        case "s":
        case "ArrowDown":
          this.shipMovesVertical = 0;
          break;
      }
    };

    this.update();
  }

  /* more functions here */

  update() {
    // move the ship
    this.shipX += this.shipMovesHorizontal;
    this.shipY += this.shipMovesVertical;

    /* drawing stuff */
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[在 CodeSandbox 上找到完整版本](https://codesandbox.io/s/v0l8v95nr5)

这不仅允许飞船同时向两个方向移动，还简化了一切。但是仍然有一个问题，快速按键没有得到很好的识别。

从代码的角度来看，在那些紧张的时刻实际发生的事情是正确的:如果按下一个相同尺寸的键(水平或垂直)，设置移动方向，如果松开，将移动设置为零。但是人类不是很精确。他们可能会在完全松开右箭头(或`d`)之前的一瞬间按下左箭头(或`a`)。这样，该功能会在那一瞬间切换移动方向，但随后会因为释放键而停止。

为了解决这个问题，`keyup`处理器需要更多的逻辑:

```
document.onkeyup = event => {
  const key = event.key;
  switch (key) {
    case "d":
    case "ArrowRight":
      if (this.shipMovesHorizontal > 0) {
        this.shipMovesHorizontal = 0;
      }
      break;
    case "a":
    case "ArrowLeft":
      if (this.shipMovesHorizontal < 0) {
        this.shipMovesHorizontal = 0;
      }
      break;
    case "w":
    case "ArrowUp":
      if (this.shipMovesVertical < 0) {
        this.shipMovesVertical = 0;
      }
      break;
    case "s":
    case "ArrowDown":
      if (this.shipMovesVertical > 0) {
        this.shipMovesVertical = 0;
      }
      break;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

[在 CodeSandbox 查看完整代码](https://codesandbox.io/s/x765pl1zm4)

好多了，不是吗？无论我们做什么，飞船都在朝着预期的方向飞行。是时候解决最后的问题了。让我们先从简单的开始:加速度。

目前，这艘船只是有一个固定的速度。让我们先快点，因为我们需要行动，对吗？为此，我们将定义船的最大速度:

```
this.shipSpeed = 5  // pixel per frame 
```

Enter fullscreen mode Exit fullscreen mode

并将其作为乘数:

```
 update() {
    // move the ship
    this.shipX += this.shipMovesHorizontal * this.shipSpeed;
    this.shipY += this.shipMovesVertical * this.shipSpeed;

    /* drawing stuff */
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们不是跳到全速，而是更新每个轴的速度值:

```
 constructor () {
    /* ... */
    this.shipSpeed = 5
    this.shipVelocityHorizontal = 0
    this.shipVelocityVertical = 0
    /* ... */
  }

  /* ...more stuff... */

  update () {
    // accelerate the ship
    const maxSpeed = this.shipSpeed;
    // speed can be negative (left/up) or positive (right/down)
    let currentAbsSpeedH = Math.abs(this.shipVelocityHorizontal);
    let currentAbsSpeedV = Math.abs(this.shipVelocityVertical);

    // increase ship speed until it reaches maximum
    if (this.shipMovesHorizontal && currentAbsSpeedH < maxSpeed) {
      this.shipVelocityHorizontal += this.shipMovesHorizontal * 0.2;
    } else {
      this.shipVelocityHorizontal = 0
    }
    if (this.shipMovesVertical && currentAbsSpeedV < maxSpeed) {
      this.shipVelocityVertical += this.shipMovesVertical * 0.2;
    } else {
      this.shipVelocityVertical = 0
    }

    /* drawing stuff */
  } 
```

Enter fullscreen mode Exit fullscreen mode

这慢慢地加速船，直到全速前进。但还是马上停下来。为了使船减速，也为了确保船确实停下来，而不是由于舍入误差而随机浮动，需要更多的线。你会在 CodeSandbox 的最终版本[中找到一切。](https://codesandbox.io/s/kxpn09n077)

现在最后一个问题已经解决了:依赖于帧速率的运动。目前，所有的值都在某种程度上进行了调整，使它们在当前的速度下工作良好。假设每秒 60 帧。现在，可怜的电脑必须在后台安装更新，或者可能只是铬变得凌乱。可能是播放器的屏幕刷新率不一样。结果是帧速率的下降或增加。让我们以下降到一半为例。每秒 30 帧对几乎所有东西来说都是完全平滑的。电影每秒 30 帧，他们做得很好，对不对？然而，我们的船突然只有一半快，这种差异是非常明显的。

为了防止这种情况，运动需要基于实际时间。不是将固定值添加到每帧的坐标中，而是添加一个值，该值考虑自上次更新以来所经过的时间。速度变化也是如此。因此，我们以每毫秒像素为单位设置值，而不是每秒 60 帧的或多或少任意的 5 个像素，因为一切都以毫秒为精度。

```
5px*60/s = 300px/s = 0.3px/ms 
```

Enter fullscreen mode Exit fullscreen mode

这使得下一步相当容易:计算自上次更新以来的毫秒数，并乘以最大速度和加速度值:

```
 constructor () {
    /* ... */
    this.shipSpeed = 0.3  // pixels per millisecond
    // how fast the ship accelerates
    this.shipAcceleration = this.shipSpeed / 10.0
    this.shipVelocityHorizontal = 0
    this.shipVelocityVertical = 0
    /* ... */

    // this should always happen right before the first update call
    // performance.now gives a high precision time value and is also
    // used by requestAnimationFrame
    this.lastDraw = performance.now()
    requestAnimationFrame(stamp => this.update(stamp))
  }

  /* ...more stuff... */

  // See the main loop section if "stamp" looks fishy to you.
  update (stamp) {
    // calculate how much time passed since last update
    const timePassed = stamp - this.lastDraw
    this.lastDraw = stamp

    // accelerate the ship
    const maxSpeed = this.shipSpeed * timePassed;
    const accel = this.shipAcceleration * timePassed;

    let currentAbsSpeedH = Math.abs(this.shipVelocityHorizontal);
    let currentAbsSpeedV = Math.abs(this.shipVelocityVertical);

    if (this.shipMovesHorizontal && currentAbsSpeedH < maxSpeed) {
      const acceleration = 
      this.shipVelocityHorizontal += this.shipMovesHorizontal * accel;
    } else {
      this.shipVelocityHorizontal = 0
    }
    if (this.shipMovesVertical && currentAbsSpeedV < maxSpeed) {
      this.shipVelocityVertical += this.shipMovesVertical * accel;
    } else {
      this.shipVelocityVertical = 0
    }

    /* drawing stuff */
  } 
```

Enter fullscreen mode Exit fullscreen mode

[在 CodeSandbox 查看完整版本](https://codesandbox.io/s/j4rzoq5kqy)

如果一切都和以前一样，你做的一切都是对的。现在独立于帧速率，你的飞船将每毫秒移动五个像素。不幸的是，除了改变屏幕的刷新率或覆盖`requestAnimationFrame`之外，我没有找到一个好的方法来测试这一点，所以我没有在帖子中提到这一部分。

## 结束

恭喜你，你造了一艘完全移动的船。这篇文章到此结束，但是当然还有很多关于游戏开发的东西需要学习。Freddy vs JSON 增加了更多的元素，但是只使用了本文中描述的技术。请随意查看[的源代码](https://github.com/nkoehring/FreddyvsJSON)，并创建大量类似的游戏。或者完全不同的。发挥创造力，享受使用你刚刚学到的东西。