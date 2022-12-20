# 上下文超级采摘鞋面！

> 原文：<https://dev.to/andershornor/context-the-super-picker-upper-3fke>

##### 如果一个复杂的应用程序有几层以上的组件，那么用 React 编码可能是一项乏味的打字工作。为了避免不必要的输入，增加一个新的 API 允许的上下文反应会有所帮助。

再解释一下，上下文允许人们通过深度嵌套的组件树传递信息，而不必通过树的每一层传递属性。通过在项目开始时花费少量的额外时间，可以避免不必要的混乱和在整个项目中打字。Context 的工作方式与 props 类似，在父组件中定义了他们希望在子组件中访问的变量。但是在上下文中，变量被定义在最高级别的组件中，然后可以通过任何子组件来访问，而不是必须确定要在父组件和子组件之间传递哪些变量。有几种方法可以建立和访问组件树的上下文，但是我在这篇博客中只介绍一种，Component.contextType 实现方法。

## 实现

为了展示上下文 API 的强大功能，想象一个深度嵌套的组件树，如下所示。
一个呈现顶级组件
[![App](img/e3f956ce64e83940a3c5edfca37b716e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9HSPiN0Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gd41pat72x9zbe6yjpri.png) 
的 app 一个呈现组件
[![HighestLevelCompenent](img/32f48b1490fca5e1037b88443626eb61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--opncG9Vi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xyixmphc8z91wp6vl2bp.png) 
的顶级组件，后者呈现另一个组件
[![MiddleLevelCompenent](img/68d1d4ad9a3db683eb058fd85be1191a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jVe6dg_P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8qyta90dps7flsxravs3.png) 
，后者呈现另一个组件
[![OtherMiddleLevelCompenent](img/95a00cfa1bde8ed62b6d415fcd3ddbac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rNdrx6ON--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1zdstj532t8qsiih8evo.png) 
，后者呈现一个输入
[![Bottom](img/1af8d3c2fa82af575688c0d8b2b90240.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8DnPsFrZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cijtx1i6cjymhidgby5a.png)

## 实现

现在，使用 context，您可以通过使用 react.createContext 在顶级组件中定义上下文，并避免必须传入 props。

[![Context](img/563ed402c66eb6e10ada52c18b285957.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p9UI0Ehj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ktcnb0uf5j72fretnouk.png)

所有加在一起看起来是这样的:

[![HighestLevelCompenent with Context](img/5baa51b4cd914e51eef06aa191797079.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r1kPSrUB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sns9ipl9nmsdo7dxp37e.png)

然后将上下文传递给孙子女，您希望用 ExampleContext 访问上下文的值。供应商三明治。

[![OtherMiddleLevelCompenent](img/3b763ab762707442ba608272ecd88ca0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XHX-gYRQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k3xo1ua8yvodtcfk4s99.png)

当您呈现底层组件时，您现在可以访问顶层组件中设置的上下文值

[![Bottom](img/5f5b2ac158e84862a47411de4950f28d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ob0YYKKg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8rvl2zj1ujazgu9zqy82.png)

谢谢你，妈妈！看这个网络应用程序！这么简单！
[![Input](img/85b0ac26ec080b58a6fdbf8972c87fae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IwPHHHWr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lqure21ey290bxof5xph.png)

## #上下文

为了说明这一点，我们举了一个简单的例子，展示了 react 上下文 API 的一个非常基本的实现。除了这个基本演示之外，还有其他方法可以实现上下文的相同功能以及上下文的其他功能。我鼓励你也看看 [react 文档](https://reactjs.org/docs/context.html#contextprovider)中上下文的其他功能。感谢阅读:)