# 使用 React 挂钩的绿色岩石动画

> 原文：<https://dev.to/billyjacoby/greensock-animations-using-react-hooks-5d1p>

这是一个关于如何使用 Greensock 和 React 钩子按需制作组件动画的简短教程。

在本教程中，我们将使用 create react app。

如果你想看一个快速演示，你可以先看看这里:

[https://billyjacoby.github.io/gsap-with-hooks/](https://billyjacoby.github.io/gsap-with-hooks/)

要开始创建新应用程序:

`create-react-app gsap-with-hooks`
T1】

在本教程中，我们需要的另一个依赖项是 GSAP。

`yarn add gsap`

启动开发服务器，以便我们可以看到我们的更改

`yarn start`

因为我们将在这里添加我们自己的动画，从`src/App.css`中删除动画 React 标志的线条