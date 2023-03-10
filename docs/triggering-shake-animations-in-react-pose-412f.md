# 在反应姿势中触发摇动动画

> 原文：<https://dev.to/bryce/triggering-shake-animations-in-react-pose-412f>

[![Shakin' it](img/8358a5a41b1eec9372afe8e2dbd89444.png "But shakin' it's all I know!")](https://i.giphy.com/media/x2cXeTHrzvlYc/giphy.gif)

我找不到一个好的操作方法，所以想写一个。我正在开发一款游戏，而[反应姿态](https://popmotion.io/pose/)在创建不同组件状态之间的平滑动画方面非常棒。但是如果我想制作一个不改变状态的组件的动画呢？

[香草姿势](https://popmotion.io/pose/api/dom-pose/)天生支持这个，因为这与它的架构方式紧密结合:`poser.set('nameOfPose')`。另一方面，`react-pose`是围绕*状态*之间的转换构建的，例如`left`或`right`。那么我如何触发这个动画而不改变它的状态或位置呢？

答案在于几个关键的基本要素:

### 1。[应用开始/应用结束](https://popmotion.io/pose/api/react-config/#config-options-poses-applyatstart/applyatend)

> `applyAtStart`和`applyAtEnd`接受在动画开始或结束时应用的样式属性。

为允许临时移动的姿势提供内部记忆。在这个`shake`动画的例子中，这让我们从它的初始`x`位置移动元素来开始动画，然后再把它返回到相同的位置。

### 2。[波塞克](https://popmotion.io/pose/api/posed/#posed-props-posekey)

> 如果`poseKey`改变，它将强制已设定姿势的组件转换到当前姿势，即使它没有改变。

激发动画所需的触发器；它可以是任何状态变量，只要它在您希望动画出现时被更新。

### 3。[春天](https://popmotion.io/pose/api/react-config/#config-options-poses-transition-transition-definitions-spring)

真正摇动的转变:一旦离开静止位置，弹簧[就会前后摆动](https://en.wikipedia.org/wiki/Simple_harmonic_motion)。

将所有这些放在一起:

[https://codesandbox.io/embed/2zk9onk7wy](https://codesandbox.io/embed/2zk9onk7wy)

在每个按钮上点击`guesses`计数将递减，触发`poseKey`更新来强制`shake`姿势(即使它已经在那个姿势)。这使它向左和向后移动，但是由于弹簧的作用，它会在停止之前晃动一下。

现在你知道了！希望有人会发现这很有用，而不是去翻遍文档。请继续关注我正在制作的游戏，它将会用到这个！