# 在 HTML 元素上创建洞的 CSS 技巧

> 原文：<https://dev.to/wteja/css-trick-to-create-hole-on-html-element-1d9m>

大家好，在本教程中，我将向大家展示如何只用 CSS3 在图像背景上创建叠加孔。让我们看看下面的例子。

[![CSS3 Trick to Create Hole on HTML Element](img/19bca5a818272a5b071a810c83e7ba29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MSlxbeG0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6yewdhp9zhzixnydy417.png)

在 [CodePen](https://codepen.io) 上看到 [Weerayut Teja (@wteja)](https://codepen.io/wteja) 用纯 CSS3 在背景叠加上打洞的笔[。](https://codepen.io/wteja/pen/wYaqdw/)

好了，让我们开始吧！

默认情况下，没有使用 CSS3 在 HTML 元素上打孔的选项。但是我们可以让一些东西看起来和这个洞“相似”。通过创建圆形元素，使其使用透明的背景色，并添加颜色到其巨大的边界。这是它的解决方案(目前也是如此)。

首先，我们创建一个带有背景图像的盒子。我决定使用来自 https://placeimg.com 的虚拟图像作为例子。

```
<div class="container">
    <div class="box">
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

然后给**加上风格。使用 placeimg 虚拟图像显示背景图像。我将宽度和高度固定为 600 像素 X 400px 像素。**

我将位置设置为相对于的**，因为我打算在背景图片的顶部和中央打一个洞。**

关键是我们必须设置**溢出:隐藏；**因为我们将使用它来隐藏超过**的洞的边界。方框**

```
.box {
    /* Background */
    background-image: url(https://placeimg.com/640/480/nature);
    background-repeat: no-repeat; 
    background-size: cover; 
    background-position: center; 
    /* Position */
    position: relative; width: 600px; 
    height: 400px; 
    /* Box settings */ 
    overflow: hidden; 
    display: inline-block; 
} 
```

Enter fullscreen mode Exit fullscreen mode

然后在不添加额外 HTML 元素的情况下制作带孔的覆盖图。我决定用**:**后的伪选择器代替。

我添加了一些蓝色的背景色，并把它移动到中心位置。top:50%；左:50%；transform: translate(-50%，-50%)；使用这种技术，这个元素将与父元素的中间对齐。别忘了加上 z-index:2；以确保它将浮动在背景图像的顶部。

将**高度设置为 0** ，**宽度设置为 50%** ，同时将**垫底设置为 50%** 。所以它将是正方形，因为当我们设置填充为百分比时，它将引用父宽度。因此，如果我们将宽度和底部填充设置为相同的值，我们将得到正方形元素。我们不需要身高，因为我们只想以父母的身高作为参考。

然后我们通过添加**边界半径:50%** 使其为圆形

“洞”的关键覆盖，如果我让它透明，没有内容。但是设置了一个超过父元素边缘的大边框。让我们看看下面的 CSS 代码示例。我用**400 像素的边框宽度**来确保它超过**。盒子**边缘。我也给**添加了模糊效果。盒子**也有类**。模糊**使洞变得更软更嫩。

```
.box::before {
    content: "";
    display: block;
    /* Scale */
    width: 50%;
    padding-bottom: 50%;
    /* Position */
    position: absolute;
    top: 50%;
    left: 50%;
    z-index: 2;
    transform: translate(-50%, -50%);
    /* Border */
    border: solid 400px rgba(31, 44, 122, 0.5);
    border-radius: 50%;
}
.box.blur::before {
    filter: blur(20px);
} 
```

Enter fullscreen mode Exit fullscreen mode

让鼠标悬停时的 CSS 转换变得更有趣。通过增加鼠标上底部宽度和填充，并使用一些过渡设置使其更平滑。让我们看看下面的例子。

在 [CodePen](https://codepen.io) 上看到 [Weerayut Teja (@wteja)](https://codepen.io/wteja) 的笔[用纯 CSS3](https://codepen.io/wteja/pen/jePLZr/) 缩小球洞背景叠加。

我希望这个指南对你有用。我也希望在不久的将来，CSS 能够比我们这样做更容易地打实际的洞。如果你有更多推荐的解决方案，请在下面的评论框中发表，让其他开发者知道。

编码快乐！