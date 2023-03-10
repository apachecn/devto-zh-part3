# Android——Explorando constraint layout(2)

> 原文：<https://dev.to/devpicon/android-explorando-constraintlayout-2-526m>

### Android—Explorando constraint layout(2)

#### 对元素进行尺寸标注和分布

[![](img/47a7b98813d0d96c92e5869d2b0e96f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sSMCAbk8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ag4C79VlwIMzJsfzs6DjVYw.png)

好吧，好吧！这是第二部分。在学习 constraintlayouts 时，您将看到始终有一些新的注释。

[Android—Explorando constraint layout(1)](https://dev.to/devpicon/android-explorando-constraintlayout-1-4dkl-temp-slug-7527726)

在我们开始讨论 constraintlayouts 内元素的大小调整(*视图大小*)之前，我们是否会提供一些数据？：

*   Google 在《Google I/o 2016》中介绍了 ConstraintLayout，也就是说，我们已经使用了几年以上的这一组件。
*   现在，我们已进入此组件的 2.0 版，我将在稍后的文章中提及一些新功能。
*   请记住，使用 ConstraintLayout 的目的是避免在萤幕上重叠元素或视图，从而缩短系统显示这些元素或视图所需的时间。

现在，让我们继续我们的…

#### 查看尺寸

<figure>[![](img/e23ecc6e92d1922ca7f24dd31a4c8276.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6DKLjVG1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0uoCkIWNHqs3LRpDbofz4Q.png) 

<figcaption>元素的大小调整有三种形式:环绕内容、固定大小和匹配约束</figcaption>

</figure>

使用视图时，通常有三种方法可以确定视图的大小或尺寸:

*   ***【环绕内容】*** —使我们能够根据内容调整视图大小。例如 Android:layout _ width = " wrap _ content "
*   ***【固定尺寸】*** —固定尺寸。例如 android:layout_width="128dp "
*   ***【匹配父项】*** —让视图占据父容器的大小。例如 Android:layout _ width = " match _ parent "

但是在处理约束布局内的元素时，要特别注意使用*【什么】？因为你可能会有意想不到的行为。取而代之的是，有一种通过得到类似结果(虽然更接近于被称为 ***【匹配约束】***的凹陷 ***【填充父】*** 来确定大小的方法。*

 **   ***【匹配约束】*** —我们使视图复盖容器的整个尺寸，或者复盖两个视图之间的可用尺寸；如果且仅当我们将视图两侧的连接点指向其父件(水平或垂直)或其他元素，并且视图大小也设定为 0dp 时。-是啊。为了实现与 android 相同的结果:layout_height="match_parent "，我们将使用以下属性集:Android:layout _ height = " 0dp " Android:layout _ constrainttop _ totototoopf = " parent " Android:layout _ constraintbottom _ tobotomof = " parent "

始终牢记使用 *ConstraintLayout* 的想法及其性质也是为了获得灵活性。

#### 尺寸比例

这种适用于 **ImageView** 的约束条件将使您能够指示希望保持映像的比例，如下图所示。

<figure>[![](img/eced997ca85b9bf0f06e0714925ef6d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a64T-JsG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ACSSgGex8mGp9SNDC8UcawA.png) 

<figcaption>维度比例</figcaption>

</figure>

#### 锁链

*链*的概念刚产生*约束*，即除了两端有连接外，还有两个要素，彼此之间有连接。您可以为要巢状的元素设定数种型式:

*   **包装** —使我们能够把元素全部作为一个整体来运作。
*   **扩散** —这是默认的链，在*约束*极端之间的可用空间内均匀分布元素。
*   **【spread _ inside】**—在这种情况下，极值元素将接近其外边界，其馀元素将分布在极值元素之间的空间中。
*   **加权** —后者基于我们对每个元素施加的权重，再加上*【match _ constraint】*将使我们能够分布元素的宽度或高度，以填充它们所在的空间。

<figure>[![](img/28924b3cd378a7ad54b1e146c3f1c98c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gZfCkOz2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGXxCwaUMlJeKziXpTUYMHw.png) 

<figcaption>连锁包装风格</figcaption>

</figure>

<figure>[![](img/9fa7d6c3bbc0249c3f27716f614d2188.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ujx2G2HM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A-t0IUnmm-wg5u7_ShHwPUA.png) 

<figcaption>扩散链样式</figcaption>

</figure>

<figure>[![](img/32a91f8604900f726488a2cb2ed38b61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ews6ItaS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AYibN5_ThbIFOfZ-RGEhC8A.png) 

<figcaption>内链风格</figcaption>

</figure>

#### 屏障

这个组件太棒了。设置一个，它将包含对可以移动它的元素的引用。在下面的示例中，首先注意 barrierdirection 属性，该属性将设置挡墙移动的方向(在此示例中为向终点)，然后注意 oconstrained _ referenced _ ids 属性，从而使挡墙连接到左侧的“*text view*”。最后，我设置了一个*约束*在第二*文字视图*的侧面朝向屏障。

<figure>[![](img/c246af10f5df3c8619579a345d06828e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aCrjAyy_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AL181QtCW4rpeeKYoYU6epQ.png) 

<figcaption>屏障横</figcaption>

</figure>

在此您可以看到当左侧的“T0”文本视图的内容发生变化时，我们的元素的行为方式。注意*文本视图*权利是如何调整其内容的。

[![](img/56390458d7cf71f4e4a4b2f8a72b94e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1_Zg8584--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AH6Ivnwvy2W1jR8jgpeOenQ.gif)

#### 圆形约束

最后，“T0”约束圆形将使我们能够根据另一个基本元素分布元素。为此，我们需要使用 layout _ constraintcircling 属性设置角度，使用 layout _ constraint circumusy 设置半径长度，并使用 layout _ contraintcircle 设置参照基准元素。

[![](img/0b8f425b819ac35a8b3fb4bbe28f1ccf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Un6RBpDA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AOEizJ6Rs5JUau1_xMxWE6Q.png)

#### 结论

到目前为止，我们有足够的元素来发挥和进一步改造我们的*布局*。至少在我目前的经验中，我在把我的*布局*由各种*相对布局*和*线性布局*转换成*约束布局*方面没有问题。如前所述，当内容发生动态变化时，我们显示器的计算时间会更短。

我将与大家分享我在其中构建示例的项目链接，以及一个“应用全部”演示。

[DevPicon/constraint-layout-lab](https://github.com/DevPicon/constraint-layout-lab)

此外，不久前我还为社区制作了一部《T1 实况串流》[【gdg Santo Domingo】](https://www.meetup.com/es-ES/GDG-Santo-Domingo/)，在其中我就本帖子和前《T4》的主题作了说明。

[https://www.youtube.com/embed/DNBGgQsDJeo](https://www.youtube.com/embed/DNBGgQsDJeo)

#### 参考文献

*   安卓喷气背包[https://developer.android.com/jetpack/](https://developer.android.com/jetpack/)
*   用 constraint layout[https://developer.android.com/training/constraint-layout/](https://developer.android.com/training/constraint-layout/)构建一个响应式 UI

* * **