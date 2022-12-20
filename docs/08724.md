# 动画三角学:跟随光标

> 原文：<https://dev.to/stseagle/trigonometry-for-animation-following-the-cursor-406o>

人们通常对三角学感到厌烦，因为它会勾起人们对高中的不好的回忆。我真的很喜欢它，因为它让我在编程的时候可以做真正有趣的事情！动画可以是教授三角学的一个很好的方式，并展示它的概念在课堂之外是多么有用。

让我们从一个简单的交互式动画开始。比方说，你希望有一个箭头总是指向屏幕上的光标。听起来像是三角函数的工作！希望你还记得高中时的`SOHCAHTOA`，在那里你用三角形两边的长度来计算一个角的正弦、余弦或正切。在这种情况下，我们知道三角形的相邻边和对边！我们知道箭头的位置，我们可以使用 JavaScript 计算出光标相对于它的位置。三角形的对边和邻边是 x 和 y 的差，或者如果你想用花哨的微积分术语的话，是`dx`和`dy`。

[https://codepen.io/twhite96/embed/KEOzRL?height=600&default-tab=result&embed-version=2](https://codepen.io/twhite96/embed/KEOzRL?height=600&default-tab=result&embed-version=2)

你可以在上面的代码中看到这一点。如果你看一下 JavaScript，你会发现我也需要转换`touchmove`和`mousemove`事件的结果，因为动画元素的原点和页面的原点是不同的。

现在我们已经准备好三角形，所以我们可以旋转指针。我们记住了我们的`SOHCAHTOA`，所以我们可以用我们的`dy`和`dx`得到角度的正切。但是——我们不要角的正切！我们想要角度本身。为了得到它，我们需要反正切函数，这意味着正切的倒数。在 Javascript 中，需要使用 [`Math.atan2`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/atan2) 函数来计算角度。如果你用 Math.atan，会导致很多心痛。试试吧。

[https://codepen.io/twhite96/embed/MxNopj?height=600&default-tab=result&embed-version=2](https://codepen.io/twhite96/embed/MxNopj?height=600&default-tab=result&embed-version=2)

这给了我们角度，我们把它转换成角度，这样我们就完成了，对吗？不对。如果你仔细观察代码笔中的角度值，当我们在指针的正上方时，它告诉我们角度是-90 度。你会认为在那个位置，指针根本不应该旋转。有两种不同的方法可以解决这个问题。第一，我们可以重画指针，让它通常指向右边。第二，我们可以偏移角度。让我们做第二个选择，因为调整角度是一件经常出现的事情，这取决于你从设计师那里获得的资产。这很容易解决，只要在计算出的角度上加一个常数 90 度。然后我们使用 CSS 来旋转三角形，我们就完成了！

[https://codepen.io/twhite96/embed/MxNoPm?height=600&default-tab=result&embed-version=2](https://codepen.io/twhite96/embed/MxNoPm?height=600&default-tab=result&embed-version=2)

TADA！完美。这个概念在模拟两个物体相互碰撞时很方便，也是我最喜欢的——令人毛骨悚然的移动眼睛。