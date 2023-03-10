# 在 React Native 中使用 Plotly 绘制图表

> 原文：<https://dev.to/rynobax_7/using-plotly-to-draw-charts-in-react-native-58cc>

在开发 React 本地版本的 web 仪表板时，我努力寻找一个能够满足我需求的图表库。我试过的那些不够好，不够灵活，或者需要从博览会中退出。由于网络图表是使用 Plotly 构建的，我决定尝试让 plotly.js 在 React Native 中工作。

由于 plotly.js 依赖于许多浏览器 API，它在 React Native 中不能开箱即用。我想最简单的方法就是用网络视图。我做的第一件事是通过用一个脚本标签包含来自 CDN 的 plotly.js 来获得一个基本的图表渲染。这是可行的，但取决于用户是否在线。我想要离线支持，所以我决定尝试将 plotly.js 源代码与我的应用程序捆绑在一起，并以某种方式将其注入 WebView。

注入源代码是一个令人沮丧的过程，因为我遇到了很多问题，源代码中的某些字符没有被正确发送，以及发现 Android 和 iOS WebViews 之间的实现差异。最终，我选定的解决方案是对 plotly.js 源代码进行 Base64 编码，用 postMessage 将其发送到 WebView，并在 WebView 中解码和评估代码。

在为我的 React 组件添加了在图表上调用 restyle/relayout 函数的能力之后，我能够使用 Plotly 绘制交互式的动态图表，即使设备处于离线状态！初始化比其他库慢一点(图表绘制前 1-2 秒)，但是性能很棒，我可以访问 Plotly 所有的优秀特性。

这个组件对我来说非常有用，所以我决定开源它！ [react-native-plotly](https://github.com/rynobax/react-native-plotly) 让你在 React Native 中使用 plotly。你可以在这道[世博小吃](https://snack.expo.io/@rynobax/react-native-plotly-demo)上看到它的作用。