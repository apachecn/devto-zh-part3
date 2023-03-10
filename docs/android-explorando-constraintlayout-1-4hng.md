# Android——Explorando constraint layout(1)

> 原文：<https://dev.to/devpicon/android-explorando-constraintlayout-1-4hng>

### Android—Explorando constraint layout(1)

#### …El uso de a relative layout con esteroides

[![](img/47a7b98813d0d96c92e5869d2b0e96f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sSMCAbk8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ag4C79VlwIMzJsfzs6DjVYw.png)

作为 Android 开发人员，我在设计复杂的视图、布局和布局时总是遇到困难……随着时间的推移，屏幕及其行为变得越来越复杂。在新开发人员中，典型的做法是将一个布局嵌套到另一个布局中，直到您拥有一个由其他村庄组成的要素树，并且维护起来非常复杂。也不用说仅这一幅画面的*的表现，其数字是从云中拍摄出来的。*

 *一段时间前，Android 团队发布了***【constraintlayout】***)，这是一种构建复杂视图而又不会陷入相互嵌套许多视图的噩梦的组件。此组件支持最少支持 Android 2.3 (API 9)的应用程序，此外，它还是“[Android jetpack](https://developer.android.com/jetpack/)”的一部分，该组件集的使用是 Android 应用程序开发最佳实践的一部分。

本出版物首先将确认使用此组件的基本要素。

#### ConstraintLayout？约束？

为了界定什么是“T0”约束，我将采用 Android 设备提供的[文档](https://developer.android.com/training/constraint-layout/)中的定义:

> 每个约束表示与另一个视图、父布局或不可见引线的连接或对齐。每个约束均根据垂直轴或水平轴定义视图位置。因此，每个视图的每个轴必须至少有一个约束，但通常需要定义多个约束。

因此，我们必须以*约束*为锚点，作为具有来源和目的地的连接。

#### Un relative layout con esteroides？

当我们谈论武装观点时，很多开发者，特别是新手，都靠使用线性布局来做到这一点。也许是因为相对布局的使用对于许多开发人员来说是没有多大意义的，也许是因为它带来的选择很少，也许是因为不知道。但是，一旦我们从**constraintlayout**开始，然后回顾一下相关布局，我们就会看到我们可以做得更多。

#### 定位约束

我们将通过简单地放置某些元素来开始探索 ConstraintLayout 的特性。

[![](img/59071ee54478d468cefbec5542479b52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r4EV1upT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AmJ3apYowWrhh02KJDzikjg.png) 

<figcaption>屏幕右下角的两个按钮</figcaption>

在上图中，您可以使用 layout _ constraintbottom _ tobotomof = " parent "和 layout _ constraintend _ tondof = " parent "属性查看屏幕右下角的两个项目，使用 layout _ constraintend _ tondof = " parent "属性查看上一个项目左侧的两个项目请务必注意，在 button_two 来源和 button_one 目标之间会产生连接点。此外，每个元素都被指定了各自的边距，以便在元素之间留出一点间隔，这意味着如果没有边距，我们的元素将立即并排显示。

<figure>[![](img/d11c68c1a65c33b4e420d300fb7e6742.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MjeT2cC7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AKe1_1GbCVWJfete7pgDpow.png) 

<figcaption>位于屏幕正中央的元素</figcaption>

</figure>

在此示例中，我们将元素放置在屏幕的正中央，从而在元素与其父容器之间创建连接，该父容器由“*keyword*parent”表示。

到目前为止，它与我们可以用相对布局来做的非常相似，甚至可以将元素放在屏幕的中央。

#### 偏差

我记得我第一次读到 ***【偏折】*** 简直是迷路了。如果我们翻译出来，我们会发现其中有 ***【分散】*** ，因此我们可以推断是指将部件移至我们原来所在的某个地方或另一个地方。

让我们举个例子

<figure>[![](img/1b2c6f9d59e16bf6cea899ebc1ae3836.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UsvsG83r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AKFnW854Y5MZRqDKGsZw5hA.png) 

<figcaption>分散元素相对于其原始约束水平垂直 25%</figcaption>

</figure>

通过使用 layout _ constrainthorizontal _ bias 和 layout_constraintVertical_bias 属性，我们可以生成某种偏差，或者更准确地说，我们可以按相对百分比分散我们的元素。值得澄清的是，同时使用这两个属性不是强制性的，你是否应该使用？这仅取决于您希望元素偏离的方向，以及元素是水平还是垂直地设置了“*约束”(*constraints)。

#### 指引

*指南*是一个非常有用的组件，用于为您要对齐的多个元素创建参考线

<figure>[![](img/a5277f1ec9de405b05245da45d7bb76c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WoojisPf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ArrleADYp0i85Dgj-KZObuA.png) 

<figcaption>按钮根据 ***【准则】***</figcaption>

</figure>

元素排列

如本例所示，我们放置了一个元素*【导线】* ，其方向垂直，相对于父元素的左边界偏移 25%。

以这种方式配置我们的元素后，我们剩下的任务就是根据它固定我们的元素。值得一提的是，每个元素还具有与父容器的上边界和下边界的连接，并且垂直偏移分别为 25%和 75%。

#### 结论

虽然这是几篇文章的第一部分，但我们可以注意到**【constraintlayout】**在构建复杂屏幕方面的巨大潜力。在接下来的一篇文章中，我将稍微谈一谈使用该组件提供的“T2”约束调整元素大小的问题。

#### 参考文献

*   安卓喷气背包[https://developer.android.com/jetpack/](https://developer.android.com/jetpack/)
*   用 constraint layout[https://developer.android.com/training/constraint-layout/](https://developer.android.com/training/constraint-layout/)构建一个响应式 UI
*   用 constraint layout[https://www.youtube.com/watch?v=XamMbnzI5vE](https://www.youtube.com/watch?v=XamMbnzI5vE)构建接口
*   纽约 2016 年机器人展——约束布局，里里外外[https://www.youtube.com/watch?v=qwSs1uPRBiI](https://www.youtube.com/watch?v=qwSs1uPRBiI)

* * **