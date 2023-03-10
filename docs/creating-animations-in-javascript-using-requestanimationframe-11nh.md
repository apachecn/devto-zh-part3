# 使用 requestAnimationFrame 在 Javascript 中创建动画

> 原文：<https://dev.to/digi0ps/creating-animations-in-javascript-using-requestanimationframe-11nh>

Javascript 中的动画？我打赌关键帧更好。_ 我几乎完全使用 CSS 来为我的网站制作动画。事实上，我很少直接使用 Javascript 来接触元素的样式。但是我最近偶然发现了一个使用 requestAnimationFrame 制作动画的教程。我原以为它的用法会很复杂，但它非常简单然而*功能强大*。

### JavaScript 中的动画先于 requestAnimationFrame

在 requestAnimationFrame 之前，经常可以看到人们使用 setInterval 来更新元素的样式，这样它的动画就类似于下面给出的代码。对于一个平滑的动画，我们需要匹配显示帧速率，即 60fps，所以我们需要每秒执行 60 次我们的函数，这意味着间隔为**17 毫秒**。

```
const progressBar = document.querySelector('#progress-bar')
let width = 0

const updateProgressBar = () => {
  progressBar.style.width = width + '%'
  width += 1
  if (width > 100){
    clearInterval(id)
  }
}

const id = setInterval(updateProgressBar, 17) 
```

更新元素样式的函数在设定的时间间隔内重复执行。虽然这个*做了工作*，但这不是一个有效的方法。这种方法有许多缺点。计时器不准确，即浏览器会优先考虑其他用户界面任务。即使用户在其他标签页中，该功能也会继续执行，根据执行情况，这意味着更高的 CPU 使用率。

[https://codepen.io/digi0ps/embed/xeyXGg?height=600&default-tab=result&embed-version=2](https://codepen.io/digi0ps/embed/xeyXGg?height=600&default-tab=result&embed-version=2)

### 现在什么是 requestAnimationFrame？

开发 requestAnimationFrame 是为了克服 **setInterval/setTimeout** 方法的缺点，提供本地 API 来运行任何类型的动画。它接受一个函数作为它的参数，并告诉浏览器在下一次重画之前执行这个函数。

它非常类似于 setInterval，除了我们请求浏览器在下一个可用的时机执行动画，而不是预定义的时间间隔。

浏览器通常基于负载、电池和屏幕上的元素可见性来优化执行。所有的动画被组合成一个重画，从而减少了所需的 CPU 周期数。当用户切换到不同的选项卡时，动画也会暂停。耶！

[![](img/7443d16c629ef50436355712ff6a4805.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oLPNQE15--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/480/1%2AsdoJ3O2cZiPTrrm9WezNmQ.gif)

现在用 requestAnimationFrame 做同样的进度条给我们

```
const updateProgressBar = () => {
  progressBar.style.width = width + '%'
  width += 1
  if (width < 100){
    requestAnimationFrame(updateProgressBar) 
  }
}

requestAnimationFrame(updateProgressBar) 
```

正如我们所见，我们最初调用 **requestAnimationFrame** ，然后递归地继续调用它，直到达到所需的*宽度*级别。显然，这种语法比 setInterval 好得多。

[https://codepen.io/digi0ps/embed/QPZmGG?height=600&default-tab=result&embed-version=2](https://codepen.io/digi0ps/embed/QPZmGG?height=600&default-tab=result&embed-version=2)

### 重力使用 requestAnimationFrame

现在，让我们尝试使用 requestAnimationFrame 来实现(更复杂的)一个球在重力作用下自由落体的动画。

我们将建立一个页面，每当用户点击页面时，这个页面就会产生一个球，球就会掉到页面底部。我们将尝试为秋天创造模拟重力。

首先我们将编写一个函数来创建一个**球状元素。**球将被 div(带*边界半径* 50%)绝对定位。我们将把从点击事件接收到的 **x，y** 坐标传递给这个函数。

```
const createBall = (top, left) => {
  const ball = document.createElement("div");
  ball.classList.add("ball");
  ball.style.top = top + "px";
  ball.style.left = left - 25 + "px";
  document.body.appendChild($ball);
  return ball;
} 
```

这里我们创建了一个 **div，**添加了类 *ball* ，并在返回创建的元素之前设置了顶部/左侧的值。

接下来我们写一个函数来初始化秋天的动画，这个函数创建一个函数来处理样式和初始化动画帧请求。

```
const initFalling = ball => {
  const ballHeight = 100
  const acceleration = 9.8 / 60;
  const { innerHeight } = window;

  let fallingSpeed = 0;

  const animateFall = () => {
    const top = parseInt(ball.style.top);
    const newTop = `${top + fallingSpeed}px`;

    // To break the fall, when the ball is near the surface
    if (parseInt(newTop) >= innerHeight - ballHeight) {
      ball.style.top = this.innerHeight - ballHeight + "px";
      ball.style.background = "red";
      return null;
    }

    // Else set the top to the new value
    ball.style.top = newTop;
    fallingSpeed = fallingSpeed + acceleration;
    requestAnimationFrame(animateFall);
  };

  requestAnimationFrame(animateFall);
}; 
```

让我们分解这个函数。

每个球都以 0 的速度开始下落，并随着每次执行而加速。由于重力加速度为每秒 9.8m/s，浏览器每秒执行我们的函数 60 次(一般显示帧率)，所以每次执行的加速度为 **9.8/60** 。

我们在 function 内部编写了另一个函数，并将其命名为 **animateFall** 。这将是传递给 requestAnimationFrame 的主函数。里面的东西很简单。我们取回球的*顶端*，并给它加上*下落速度。*我们检查这个 **newTop** 值是否大于窗口的 **innerHeight。如果不是，那么我们将其设置为新值，并在再次请求动画帧之前增加**下降速度**。如果 **newTop** 更大，那么球已经碰到了表面，所以我们从函数返回。**

我们差不多完成了，现在我们要做的就是创建一个事件处理程序，并将这两个函数链接在一起。

```
const onClickListener = event => {
  const { x, y } = event;
  const ball = createBall(y, x);
  initFalling(ball)
};

document.addEventListener("click", onClickListener, false); 
```

事件坐标和定位坐标被反转。也就是这里的 x 相当于定位的左侧，y 相当于顶部。我们创造了一个*自由落体入口。*

[https://codepen.io/digi0ps/embed/eoPQWy?height=600&default-tab=result&embed-version=2](https://codepen.io/digi0ps/embed/eoPQWy?height=600&default-tab=result&embed-version=2)

### 我们还可以做得更好

*   **垂死的动画；**增加了一个基于碰撞冲击的濒死动画。下落速度是球在空中飞行时间的指标。因此，我们可以添加一个渐隐动画，其中移动较多的球渐隐较快，而移动较少的球渐隐较慢。
*   **随机性；**为球的颜色和大小增加了随机因子。
*   **组织起来；使用 *Typescript + Webpack 将代码组织成组件。***

### 什么时候应该用这个代替关键帧？

虽然 requestAnimationFrame 和关键帧/过渡的性能非常接近，但在大多数情况下，我们可以坚持使用 CSS 动画。

如果动画依赖于某些状态或数据，使用 JavaScript 制作动画会变得非常有用，就像上面的例子，我们使用 JS 处理球的下落。我们也可以用它来创建更复杂的动画，比如交错的动画或者拖尾的动画(提示:接下来我会做一个拖尾动画的教程)。

### 参考文献

*   [window . requestanimationframe()](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)
*   [requestAnimationFrame——流畅 JavaScript 动画的秘密](http://researchhubs.com/post/computing/javascript/requestAnimationFrame.html)
*   [CSS 和 JavaScript 动画性能](https://developer.mozilla.org/en-US/docs/Web/Performance/CSS_JavaScript_animation_performance)

**和……**

[![](img/0dee04dc46230647b2a2d7e4d7795c60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DdlQl5yV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/640/1%2AyNMpWj2Qwis_XVDYcdhoPw.gif)

感谢您的阅读！