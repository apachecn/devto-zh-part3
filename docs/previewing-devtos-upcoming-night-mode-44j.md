# 预览 dev.to 即将推出的夜间模式

> 原文：<https://dev.to/somedood/previewing-devtos-upcoming-night-mode-44j>

# [![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) 功能要求:夜间模式 #134](https://github.com/thepracticaldev/dev.to/issues/134) 

[![ghost avatar](img/29e48482b715470240aa6c57e15c1745.png)](https://github.com/ghost) **[ghost](https://github.com/ghost)** posted on [<time datetime="2018-01-10T11:09:43Z">Jan 10, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/134)

# 特征请求

如果有一个“夜间模式”，可以将网站的颜色切换到在某些情况下看起来更好的深色版本，那将是非常棒的。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/134)

夜间模式一直是该网站的一个长期要求的功能。对于像我这样的人来说，深色主题比浅色主题更好，因为它们给眼睛带来的压力更小，尤其是在黑暗的环境中(因此得名)。这也让我看起来时髦又前卫。夜间模式的实现已经积压了很长一段时间，直到上个月 [@rkichenama](https://dev.to/rkichenama) 提交了[的拉请求](https://github.com/thepracticaldev/dev.to/pull/1104)才最终启动。从那时起，他一直与 dev.to 团队密切合作，完善即将到来的夜间模式的 CSS 样式。

不幸的是，CSS 样式还没有最终确定。贡献者们一直在不断努力。*(大声喊到 [@rkichenama](https://dev.to/rkichenama) ！)*在目前的状态下，大部分款式都只是原款式的反转色。个人资料图片也是灰度的。但这仍然是一个开始！有总比没有好。我很感激在这个功能上所做的努力。*(再次喊出 [@rkichenama](https://dev.to/rkichenama) ！)*

然而，这并不意味着我们现在不能测试它。是的，你没听错。我们现在就可以在这里测试“测试版”的夜间模式。而且激活起来也不是太难！🎉

# 如何激活夜间模式？

截至写这篇文章时，还没有办法通过设置激活夜间模式。测试它的唯一方法是用 [DevTools](https://developers.google.com/web/tools/chrome-devtools/) 手动改变`<body>`标签的类别。

有很多方法可以做到这一点。您可以使用 DevTools 的[元素面板手动将`night-theme`类添加到`<body>`标签中。](https://developers.google.com/web/tools/chrome-devtools/css/#classes)

您还可以使用 DevTools 的[控制台面板，用 JavaScript 以编程方式将`night-theme`类添加到`<body>`标签中。就个人而言，我更喜欢这种方式，因为这实际上是您必须输入到控制台中的一行代码。](https://developers.google.com/web/tools/chrome-devtools/console/get-started) 

```
document.body.className = 'night-theme'; 
```

Enter fullscreen mode Exit fullscreen mode

要退出夜间模式，只需重新加载页面。

瞧吧！起初可能会令人吃惊，但至少我们现在可以测试它了。别忘了在[这个拉动请求](https://github.com/thepracticaldev/dev.to/pull/1104)中给出建议和**建设性的**反馈。*(最后一次喊出 [@rkichenama](https://dev.to/rkichenama) ！)*或者如果可以的话，自己回顾一下代码，做出自己的修改。任何贡献都非常受欢迎。

让我们帮助实现夜间模式！