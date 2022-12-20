# 反应生命周期方法流程

> 原文：<https://dev.to/vish448/react-lifecycle-methods-flows-a61>

# react 生命周期方法执行的顺序。

**反应生命周期方法的执行流程**

**Constructor()**
它有助于设置组件的初始状态。

**Render()**
使用构造函数渲染初始状态

**在这两种方法反应之后，生命周期方法开始执行**

**componentidmount()**
渲染方法完成后。它寻找这个方法并安装组件。在这种方法中，您可以进行 API 调用，创建 Ajax 请求等。

**ComponentDidUpdate()**
每当组件状态改变或者组件收到更新时，这个方法就会运行。

**ComponentWillMount()**
这在 16.4 版本中已被弃用，并在 17 版本中被完全删除。

**ComponentWillUnmount()**
每当从 DOM 中删除一个组件时调用。也就是说，如果您使用 react 路由器并更改路由，它将调用此方法。

**你不需要物理绑定这些方法，因为 react 会为你绑定**