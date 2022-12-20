# VUEJS 监听来自子组件的生命周期挂钩

> 原文：<https://dev.to/jsbaguette/vuejs-listen-lifecycle-hook-from-the-child-component-2hp9>

这是我从 [Vuedose](https://vuedose.tips/) 那里学到的一个非常有用的技巧。

在某些情况下，我需要知道一个组件是何时从父组件创建、安装或更新的。

所以你已经尝试过这样的事情了

```
 <Child @mounted="doSomething"/>. 
```

Enter fullscreen mode Exit fullscreen mode

让我告诉你:这是行不通的。

相反，解决方案非常简单，只需监听带有生命周期钩子名称的事件，前缀为@hook:。

```
 <Child @hook:mounted="childMounted"/> 
```

Enter fullscreen mode Exit fullscreen mode

如果你不相信这个提示，请在这个[代码沙箱](https://codesandbox.io/s/18r05pkmn7)中检查你自己

感谢 [Vuedose](https://vuedose.tips/) 提供的这个提示。去看看他们的网站，签下时事通讯😄