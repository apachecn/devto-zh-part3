# 你一直在做错误的地图显示

> 原文：<https://dev.to/mcrowder65/you-ve-been-doing-mapdispatchtoprops-wrong-this-entire-time-582m>

这样做:

```
const mapDispatchToProps = {
  decrement: () => ({ type: "DECREMENT" }),
  increment: () => ({ type: "INCREMENT" })
}; 
```

Enter fullscreen mode Exit fullscreen mode

与其这样:

```
const mapDispatchToProps = dispatch => {
  return {
    decrement: () => dispatch({ type: "DECREMENT" }),
    increment: () => dispatch({ type: "INCREMENT" })
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

不同之处在于，我们依靠 react-redux 在 mapDispatchToProps 对象中的每个函数值上注入调度，而不是依靠 react-redux 在 mapDispatchToProps 函数中注入调度。

如果您没有有效地测试您的代码，那么这是一种增加代码覆盖率的简单方法，但它也将防止错误，因为这样您就不必担心转发额外的参数。

关于 github 的一个工作实例，请看这个回购:[https://github.com/mcrowder65/map-dispatch-to-props](https://github.com/mcrowder65/map-dispatch-to-props)