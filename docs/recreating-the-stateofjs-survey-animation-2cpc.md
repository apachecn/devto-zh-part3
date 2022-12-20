# 重现司法调查动画第 2 部分

> 原文：<https://dev.to/ryan_dunton/recreating-the-stateofjs-survey-animation-2cpc>

这是我试图重现 JS 动画的 [2018 状态的第 2 部分。第一部分可以在](https://2018.stateofjs.com/)[这里找到](https://dev.to/ryan_dunton/recreating-the-stateofjs-survey-animation-5ca9)

[一看我完成的尝试](https://state-of-js-animation-rgd.herokuapp.com/)。

当我们停下来的时候，我看到了这样一个页面:

[![components](img/c56fecdb10b697af66f16660d0d366a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fo_KxSRU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vdya8h0m828ym53ufca9.png)

并且每个`Letter`组件具有在挂载点
上运行的以下状态

```
this.setState({
      minTop: -offsetTop - margin,
      maxTop: windowHeight - elementHeight + margin - offsetTop,
      maxRight: windowWidth - elementX - (elementWidth - margin),
      minLeft: -offsetLeft - margin,
      animationType,
      currentLeftOrRight,
      currentTopOrBottom
    }); 
```

既然每个组件都知道它的 minTop、maxTop、maxRight、minLeft 和 animationType，我们就可以制作动画函数了。

因为我们使用格林斯托克的 TweenLite，我们可以移动到一个新的 x，y 坐标，我们只需要得到坐标。我们可以用一个简单的 switch 语句获得坐标，并在最大/最小范围内给它随机相反的坐标。

```
const getNewCoordinates = (minTop, maxTop, minLeft, maxRight, currentSide) => {
  let xDirection, yDirection, x, y;
  // alternate sides
  switch (currentSide) {
    case "left":
      xDirection = "right";
      x = maxRight;
      y = Math.random() * maxTop + minTop;
      break;
    case "right":
      xDirection = "left";
      x = minLeft;
      y = Math.random() * maxTop + minTop;
      break;
    case "top":
      yDirection = "bottom";
      x = Math.random() * minLeft + maxRight;
      y = minTop;
      break;
    case "bottom":
      yDirection = "top";
      x = Math.random() * minLeft + maxRight;
      y = maxTop;
      break;
    default:
      break;
  }
  return { x, y, xDirection, yDirection };
}; 
```

所以现在我们把这些信息传递给一个补间动画来更新状态。我们让字母在屏幕上移动，并调用一个`flipAnimation`函数，该函数将`animationType`从“x”改为“y ”,并在每个动画结束时重新播放动画。

```
moveAnimation = () => {
    this.animation = TweenLite.to(
      this.letterElement,
      this.state.animationTime,
      {
        x: this.state.x,
        y: this.state.y,
        ease: Linear.easeNone,
        onComplete: this.flipAnimation
      }
    );
  }; 
```

所以现在我们有字母在屏幕上移动，我们需要做的就是让它们在悬停时“回家”。

[![go home](img/2e5bd24663e14be272a510c3406fd4d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3SPEhHQh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6945xvhebanmi6j50dve.jpg)

我们从格林斯托克那里知道‘家’的位置是`x:0,y:0`。因此，当`App` home 状态设置为 true 时，我们调用一个函数将所有的`Letters`移动到 0，0，并给它一个更快的`animationTime`，以获得一个很酷的小效果。

```
returnToHome = () => {
    if (this.state.x !== 0) {
      this.setState({
        x: 0,
        y: 0,
        animationTime: 0.5,
        endAnimation: true
      });
    }
  }; 
```

剩下的唯一棘手的部分是在悬停结束时“重启”动画。我们可以检查我们的`this.props.isHome`是否等于假，而`this.state.endAnimation`是否等于真。这意味着用户不再悬停，但是组件还没有动画，所以我们称之为动画。

```
if (!this.props.isHome && this.state.endAnimation) {
      this.setState({
        endAnimation: false
      });
      this.flipAnimation();
    } 
```

嘣！现在我们有了一个还过得去的原型。我仍然需要添加调整大小功能和一些移动兼容性，但除此之外，它已经为世界做好了准备。

很明显我跳过了一些步骤，但是完整的代码可以在 Github 上找到。

集成 React 和 Greensock 非常简单，我希望能再做一次！