# 在 React 中高效呈现列表

> 原文：<https://dev.to/darthknoppix/efficiently-rendering-lists-in-react-1kh>

#### 使用 react-window 渲染 react 中的虚拟化列表

React 具有很好的渲染性能，但当涉及到大量数据时，像任何框架一样，它可能会陷入困境。当您面对用户可以滚动浏览的呈现到 DOM 的大型数据列表时，性能可能会受到影响。一种解决方案是使用虚拟化列表，只呈现用户在屏幕上看到的内容。当用户向下滚动时，项目会被新内容无缝替换。

我决定试用 react-window，这是非常流行的 react-virtualized 的作者提供的一个较新的库。关键的区别是这一个明显更小更轻。它的功能并不全，但它能在预算紧张的情况下完成任务。

### 一个反应窗口比较

让我们看看最终的结果，然后看看 react-window 是如何集成到项目中的。

[https://www.youtube.com/embed/AU8qeVlYy_4](https://www.youtube.com/embed/AU8qeVlYy_4)

该视频显示了一列中的 1000 个项目，其中包含一些使用 [Faker.js](https://github.com/marak/Faker.js/) 生成的虚拟数据。这些卡有意地使用动画、投影和变换来模拟更密集的渲染场景。帧速率显著下降，经常低于目标 60fps。使用 react-window 会带来更流畅的体验，快速滚动时帧速率会略有下降。

*   [演示可在此处](http://react-window-example.sethcorker.com)访问。
*   在 GitHub 上可以找到[库。](https://github.com/Darth-Knoppix/react-window-example)

### 为什么我们需要反应窗口？

DOM 节点需要被填充、动画化和渲染。层次结构中的节点越多，显示的内容就越密集。尽管现代浏览器在这方面非常高效，但在某些情况下，性能仍然会受到影响。与许多与性能相关的技巧一样，优化应该在必要时进行，而不是先发制人。如果你注意到巨大的数据列表会使你的速度变慢，那么也许 react-window 就是为你准备的。

### 反应窗口的一种情况

让我们来看一个可以进行一些虚拟化的 React 组件。