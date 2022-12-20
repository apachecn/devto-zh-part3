# 对本地动画输入做出反应

> 原文：<https://dev.to/saadbashar/react-native-animated-inputs-2o6e>

这是一个非常直接的探索，关于我如何在我的 react 原生形式中做动画输入。演示如下:

[![Demo](img/c6e10862e41f6dedafb9b2e5331c1da9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x3m98aTH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AH9SP1cPsRbf1V5iVEikh6w.gif)

因此，我基本上有三个文本输入，我需要一个接一个地动画。我用 react 原生动画模块来做这种动画。

在动画模块中，有一种叫做**交错**的方法，它有助于用**连续延迟**一个接一个地制作动画组件。我只需要将我的数组输入传递给这个方法来实现这个动画。完整的代码如下所示: