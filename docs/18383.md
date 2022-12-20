# React 中的四种响应类型

> 原文：<https://dev.to/metamn/four-types-of-responsiveness-in-react-1pma>

> 这段文字摘自 [MR-UI 故事书](http://metamn.io/mr-ui/?selectedKind=Basics%2FResponsiveness%20%E2%9C%93&selectedStory=Overview&full=0&addons=1&stories=1&panelRight=0&addonPanel=storybook-addon-background%2Fbackground-panel&background=beige)。故事书的一个好处是*强迫*你记录你的研究，而不仅仅是最终结果。以此为例。

有了 React 这个反应式/响应式框架，是时候重新思考网站的响应性了。

在大多数情况下，在大型网站上，响应性意味着网站正在使用媒体查询来适应设备屏幕大小。

自从 Frank Chimero 的第一篇文章以来，我们知道这还不够。为什么？有太多的设备，而且还会有更多的设备无法被媒体查询覆盖。

相反，我们应该让网站的每个组件都知道它的大小，并对变化做出相应的反应。这样，即使在未知和不确定的条件下，最终场地[也可以自动组装](http://metamn.io/beat/tomorrowww/)。

这个想法与 React 很相似:每个组件负责并管理自己的状态。对于普通的 CSS 元素也应该如此。一个通用协议的工作正在进行中，但是 W3C 和往常一样，进展非常缓慢，因此 CSS 元素查询还不是一个标准。

幸运的是，React 有大量的库和方法，可以让元素知道它们的维度并对变化做出反应。

我们有以下单独的场景，必要时可以组合在一起:

1.  **全局状态—** 元素知道彼此的状态，并且可以集体地对变化采取行动。
2.  **对度量作出反应—** 元素知道它的度量，并能对变化作出反应。
3.  **元素查询—** 元素的样式可以执行元素查询，以对样式内部的变化做出反应。
4.  **媒体查询—** 元素的样式可以执行传统的媒体查询，并在设备屏幕尺寸改变时做出反应。

### 资源

*   [反应措施](https://github.com/souporserious/react-measure)
*   [元素查询可用技术列表，2018 年 12 月](https://github.com/ZeeCoder/container-query)
*   [作为设计者的设备](http://metamn.io/beat/tomorrowww/)
*   Frank Chimero 的原创想法