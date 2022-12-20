# 用 JavaScript 移动 Sprite 工作表字符

> 原文：<https://dev.to/martyhimmel/moving-a-sprite-sheet-character-with-javascript-3adg>

姗姗来迟的后续终于来了！😅

在第 1 部分中，我们介绍了如何使用`requestAnimationFrame`在计时器上制作 sprite sheet 角色的动画。现在，我们将根据用户输入来设置动画和移动，而不是循环播放定时动画。

### 设置

我们将使用第 1 部分的代码作为基线。为了使这变得简单一点，让我们修改一些旧代码，给我们一个更好的起点。

```
let img = new Image();
img.src = 'https://opengameart.org/sites/default/files/Green-Cap-Character-16x18.png';
img.onload = function() {
  window.requestAnimationFrame(gameLoop);
};

let canvas = document.querySelector('canvas');
let ctx = canvas.getContext('2d');

const SCALE = 2;
const WIDTH = 16;
const HEIGHT = 18;
const SCALED_WIDTH = SCALE * WIDTH;
const SCALED_HEIGHT = SCALE * HEIGHT;

function drawFrame(frameX, frameY, canvasX, canvasY) {
  ctx.drawImage(img,
                frameX * WIDTH, frameY * HEIGHT, WIDTH, HEIGHT,
                canvasX, canvasY, SCALED_WIDTH, SCALED_HEIGHT);
}

const CYCLE_LOOP = [0, 1, 0, 2];
let currentLoopIndex = 0;
let frameCount = 0;
let currentDirection = 0;

function gameLoop() {

  window.requestAnimationFrame(gameLoop);
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  `init`功能已被重命名为`gameLoop`。
2.  `step`功能已被删除。
3.  为了保持循环继续，在`gameLoop`结束时调用`window.requestAnimationFrame(gameLoop);`。
4.  按照惯例，所有的常量都是大写的。

### 获取用户输入

让我们设置处理用户输入。我们需要一对事件监听器来跟踪按键何时被按下和释放。我们还需要一些东西来跟踪这些状态。我们可以跟踪特定的按钮并只对它们做出响应，或者我们可以将所有的按键存储在一个对象中，然后检查我们需要什么。我个人倾向于用后者。

```
let keyPresses = {};

window.addEventListener('keydown', keyDownListener, false);
function keyDownListener(event) {
  keyPresses[event.key] = true;
}

window.addEventListener('keyup', keyUpListener, false);
function keyUpListener(event) {
  keyPresses[event.key] = false;
}

function gameLoop() {
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

### 移动人物

现在我们正在捕捉用户输入，让我们添加角色并处理移动。

首先，我们将只使用面朝下的字符的第一帧。我们还需要跟踪角色的 x 和 y 位置。我们还应该添加一个`MOVEMENT_SPEED`常量，这样我们以后就可以很容易地改变它。这转化为每个动画帧移动的像素数。

```
const MOVEMENT_SPEED = 1;
let positionX = 0;
let positionY = 0;

function gameLoop() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  if (keyPresses.w) {
    positionY -= MOVEMENT_SPEED;
  } else if (keyPresses.s) {
    positionY += MOVEMENT_SPEED;
  }
  if (keyPresses.a) {
    positionX -= MOVEMENT_SPEED;
  } else if (keyPresses.d) {
    positionX += MOVEMENT_SPEED;
  }

  drawFrame(0, 0, positionX, positionY);
  window.requestAnimationFrame(gameLoop);
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/martyhimmel/embed/omKKJy?height=600&default-tab=result&embed-version=2](https://codepen.io/martyhimmel/embed/omKKJy?height=600&default-tab=result&embed-version=2)

我们有一个感人的角色！

*注:原来用的是箭头键，但由于上下按时页面滚动，所以改用了 WASD 键。任何组合键都可以。*

### 改变方向

目前，角色总是面朝下。让我们面对不同的方向。和第 1 部分一样，我们将使用`currentDirection`变量来存储角色面对的方向。为了更直观一点，让我们为每个方向添加一个常数。

```
const FACING_DOWN = 0;
const FACING_UP = 1;
const FACING_LEFT = 2;
const FACING_RIGHT = 3;
let currentDirection = FACING_DOWN; 
```

Enter fullscreen mode Exit fullscreen mode

既然已经设置好了，让我们更新移动处理条件和处理设置方向的`drawFrame`调用。

```
// Inside gameLoop
if (keyPresses.w) {
  positionY -= MOVEMENT_SPEED;
  currentDirection = FACING_UP;
} else if (keyPresses.s) {
  positionY += MOVEMENT_SPEED;
  currentDirection = FACING_DOWN;
}

if (keyPresses.a) {
  positionX -= MOVEMENT_SPEED;
  currentDirection = FACING_LEFT;
} else if (keyPresses.d) {
  positionX += MOVEMENT_SPEED;
  currentDirection = FACING_RIGHT;
}

drawFrame(0, currentDirection, positionX, positionY); 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/martyhimmel/embed/pYzgVj?height=600&default-tab=result&embed-version=2](https://codepen.io/martyhimmel/embed/pYzgVj?height=600&default-tab=result&embed-version=2)

而现在我们多个方向。现在让我们添加不同的框架。我们仍然会坚持使用`0, 1, 0, 2`帧模式来制作行走动画。为此，我们可以在我们的`drawFrame`调用中带回对`CYCLE_LOOP[currentLoopIndex]`的引用。

```
drawFrame(CYCLE_LOOP[currentLoopIndex], currentDirection, positionX, positionY); 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以恢复帧增量和限制。这看起来与第 1 部分有些不同。我们仍然需要处理移动，所以我们将增加帧数，然后每隔几帧重置计数并更新索引，而不是提前返回。然而，如果有任何移动，我们只希望帧增加*。* 

```
const FRAME_LIMIT = 12;

function gameLoop() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  let hasMoved = false;

  if (keyPresses.w) {
    positionY -= MOVEMENT_SPEED;
    currentDirection = FACING_UP;
    hasMoved = true;
  } else if (keyPresses.s) {
    positionY += MOVEMENT_SPEED;
    currentDirection = FACING_DOWN;
    hasMoved = true;
  }

  if (keyPresses.a) {
    positionX -= MOVEMENT_SPEED;
    currentDirection = FACING_LEFT;
    hasMoved = true;
  } else if (keyPresses.d) {
    positionX += MOVEMENT_SPEED;
    currentDirection = FACING_RIGHT;
    hasMoved = true;
  }

  if (hasMoved) {
    frameCount++;
    if (frameCount >= FRAME_LIMIT) {
      frameCount = 0;
      currentLoopIndex++;
      if (currentLoopIndex >= CYCLE_LOOP.length) {
        currentLoopIndex = 0;
      }
    }
  }

  drawFrame(CYCLE_LOOP[currentLoopIndex], currentDirection, positionX, positionY);
  window.requestAnimationFrame(gameLoop);
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/martyhimmel/embed/mobrbX?height=600&default-tab=result&embed-version=2](https://codepen.io/martyhimmel/embed/mobrbX?height=600&default-tab=result&embed-version=2)

我们做到了！角色在画布上移动，改变方向，并在所有动画帧中循环。

### 稍微清理一下

在我们继续之前，让我们对这个做一点重构:

```
const SCALE = 2;
const WIDTH = 16;
const HEIGHT = 18;
const SCALED_WIDTH = SCALE * WIDTH;
const SCALED_HEIGHT = SCALE * HEIGHT;
const CYCLE_LOOP = [0, 1, 0, 2];
const FACING_DOWN = 0;
const FACING_UP = 1;
const FACING_LEFT = 2;
const FACING_RIGHT = 3;
const FRAME_LIMIT = 12;
const MOVEMENT_SPEED = 1;

let canvas = document.querySelector('canvas');
let ctx = canvas.getContext('2d');
let keyPresses = {};
let currentDirection = FACING_DOWN;
let currentLoopIndex = 0;
let frameCount = 0;
let positionX = 0;
let positionY = 0;
let img = new Image();

window.addEventListener('keydown', keyDownListener);
function keyDownListener(event) {
    keyPresses[event.key] = true;
}

window.addEventListener('keyup', keyUpListener);
function keyUpListener(event) {
    keyPresses[event.key] = false;
}

function loadImage() {
  img.src = 'https://opengameart.org/sites/default/files/Green-Cap-Character-16x18.png';
  img.onload = function() {
    window.requestAnimationFrame(gameLoop);
  };
}

function drawFrame(frameX, frameY, canvasX, canvasY) {
  ctx.drawImage(img,
                frameX * WIDTH, frameY * HEIGHT, WIDTH, HEIGHT,
                canvasX, canvasY, SCALED_WIDTH, SCALED_HEIGHT);
}

loadImage();

function gameLoop() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  let hasMoved = false;

  if (keyPresses.w) {
    moveCharacter(0, -MOVEMENT_SPEED, FACING_UP);
    hasMoved = true;
  } else if (keyPresses.s) {
    moveCharacter(0, MOVEMENT_SPEED, FACING_DOWN);
    hasMoved = true;
  }

  if (keyPresses.a) {
    moveCharacter(-MOVEMENT_SPEED, 0, FACING_LEFT);
    hasMoved = true;
  } else if (keyPresses.d) {
    moveCharacter(MOVEMENT_SPEED, 0, FACING_RIGHT);
    hasMoved = true;
  }

  if (hasMoved) {
    frameCount++;
    if (frameCount >= FRAME_LIMIT) {
      frameCount = 0;
      currentLoopIndex++;
      if (currentLoopIndex >= CYCLE_LOOP.length) {
        currentLoopIndex = 0;
      }
    }
  }

  drawFrame(CYCLE_LOOP[currentLoopIndex], currentDirection, positionX, positionY);
  window.requestAnimationFrame(gameLoop);
}

function moveCharacter(deltaX, deltaY, direction) {
  positionX += deltaX;
  positionY += deltaY;
  currentDirection = direction;
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来干净多了。常量和变量都在靠近顶部的一个地方(我们甚至可以将它们移动到一组对象中，而不是全局范围，但是为了本教程，我们将保持简单)。按键事件监听器是这组函数中的第一个。启动整个游戏循环的图像加载器有它自己的功能。并且移动处理已经被移动到它自己的功能中。

### 保持在界限内

将运动处理提取到它自己的功能中实际上还有一个额外的目的。现在，角色可以离开画布边界。使用`moveCharacter`函数，我们可以在一个地方而不是四个地方检查边界冲突。

我们的碰撞检测看起来像这样:

1.  角色的左边缘是否接触或通过画布的左边缘？
2.  角色的右边缘是否接触或通过画布的右边缘？
3.  角色的上边缘是否接触或通过画布的上边缘？
4.  角色的底边是否接触或通过画布的底边？

如果这些都是真的，我们需要阻止角色向给定的方向移动。因为我们同时处理两个方向，所以我们可以分离水平和垂直移动检查和限制。这样，如果角色在一边的中间，他们可以沿着边滑动，直到碰到角落。

让我们更新我们的移动函数来处理这些情况。

```
function moveCharacter(deltaX, deltaY, direction) {
  if (positionX + deltaX > 0 && positionX + SCALED_WIDTH + deltaX < canvas.width) {
    positionX += deltaX;
  }
  if (positionY + deltaY > 0 && positionY + SCALED_HEIGHT + deltaY < canvas.height) {
    positionY += deltaY;
  }
  currentDirection = direction;
} 
```

Enter fullscreen mode Exit fullscreen mode

有一点要记住的是`positionX`和`positionY`是指人物的左上角。正因为如此，`positionX + SCALED_WIDTH`给了我们角色的右边缘，`positionX + SCALED_HEIGHT`给了我们角色的下边缘。

考虑到这一点，以下是检查如何翻译以匹配上述问题:

1.  `positionX + deltaX > 0`检查左边缘碰撞。
2.  `positionX + SCALED_WIDTH + deltaX < canvas.width`检查右边缘碰撞。
3.  `positionY + deltaY > 0`检查顶部边缘碰撞。
4.  `positionY + SCALED_HEIGHT + deltaY < canvas.height`检查底边碰撞。

### 最后一个怪癖

现在我们的角色还在范围内，还有一个小问题需要处理。如果用户在角色处于动画周期的第二或第四帧时停止按键，这看起来有点奇怪。这个角色在中间步幅站着不动。我们在角色不动的时候重置画面怎么样？

在`gameLoop`函数中，就在调用`drawFrame`之前，让我们添加一个检查:

```
if (!hasMoved) {
    currentLoopIndex = 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在，角色在不移动时将始终保持自然的站立姿势。

### 最终结果

下面是最后一段代码:

```
const SCALE = 2;
const WIDTH = 16;
const HEIGHT = 18;
const SCALED_WIDTH = SCALE * WIDTH;
const SCALED_HEIGHT = SCALE * HEIGHT;
const CYCLE_LOOP = [0, 1, 0, 2];
const FACING_DOWN = 0;
const FACING_UP = 1;
const FACING_LEFT = 2;
const FACING_RIGHT = 3;
const FRAME_LIMIT = 12;
const MOVEMENT_SPEED = 1;

let canvas = document.querySelector('canvas');
let ctx = canvas.getContext('2d');
let keyPresses = {};
let currentDirection = FACING_DOWN;
let currentLoopIndex = 0;
let frameCount = 0;
let positionX = 0;
let positionY = 0;
let img = new Image();

window.addEventListener('keydown', keyDownListener);
function keyDownListener(event) {
    keyPresses[event.key] = true;
}

window.addEventListener('keyup', keyUpListener);
function keyUpListener(event) {
    keyPresses[event.key] = false;
}

function loadImage() {
  img.src = 'https://opengameart.org/sites/default/files/Green-Cap-Character-16x18.png';
  img.onload = function() {
    window.requestAnimationFrame(gameLoop);
  };
}

function drawFrame(frameX, frameY, canvasX, canvasY) {
  ctx.drawImage(img,
                frameX * WIDTH, frameY * HEIGHT, WIDTH, HEIGHT,
                canvasX, canvasY, SCALED_WIDTH, SCALED_HEIGHT);
}

loadImage();

function gameLoop() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  let hasMoved = false;

  if (keyPresses.w) {
    moveCharacter(0, -MOVEMENT_SPEED, FACING_UP);
    hasMoved = true;
  } else if (keyPresses.s) {
    moveCharacter(0, MOVEMENT_SPEED, FACING_DOWN);
    hasMoved = true;
  }

  if (keyPresses.a) {
    moveCharacter(-MOVEMENT_SPEED, 0, FACING_LEFT);
    hasMoved = true;
  } else if (keyPresses.d) {
    moveCharacter(MOVEMENT_SPEED, 0, FACING_RIGHT);
    hasMoved = true;
  }

  if (hasMoved) {
    frameCount++;
    if (frameCount >= FRAME_LIMIT) {
      frameCount = 0;
      currentLoopIndex++;
      if (currentLoopIndex >= CYCLE_LOOP.length) {
        currentLoopIndex = 0;
      }
    }
  }

  if (!hasMoved) {
    currentLoopIndex = 0;
  }

  drawFrame(CYCLE_LOOP[currentLoopIndex], currentDirection, positionX, positionY);
  window.requestAnimationFrame(gameLoop);
}

function moveCharacter(deltaX, deltaY, direction) {
  if (positionX + deltaX > 0 && positionX + SCALED_WIDTH + deltaX < canvas.width) {
    positionX += deltaX;
  }
  if (positionY + deltaY > 0 && positionY + SCALED_HEIGHT + deltaY < canvas.height) {
    positionY += deltaY;
  }
  currentDirection = direction;
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是结果:

[https://codepen.io/martyhimmel/embed/drbpvZ?height=600&default-tab=result&embed-version=2](https://codepen.io/martyhimmel/embed/drbpvZ?height=600&default-tab=result&embed-version=2)