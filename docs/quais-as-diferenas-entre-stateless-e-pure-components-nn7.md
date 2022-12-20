# 无状态与纯元件有何不同？

> 原文：<https://dev.to/viniciusersouza/quais-as-diferenas-entre-stateless-e-pure-components-nn7>

作为最初的开发者，我在旅途中首先遇到的一个问题是何时/为什么使用`Components`、`Pure Components`和`Stateless Components`。

在我的调查过程中我在网上做了很多研究直到我想出了一个可行可行的方法来理解它们的概念和用途之间的区别。

当我们使用前端开发时，一个主要问题是如何在浏览器限制内高效运行应用程序。这就是 T0 真正发光的地方。

与`Pure Components`合作时，我们正在改变`React`与`shouldComponentUpdate`功能合作的方式，因此，只有在变更时，此元件才会呼叫`render()`、nas `props`或`state`。

这是一个`Pure Component`和一个`Stateless Component`的主要区别。`Pure Component`使用布料后面的`shallowEqual`进行轻微比较，决定是否重新渲染零部件。

```
if (this._compositeType === CompositeTypes.PureClass) {
  shouldUpdate = !shallowEqual(prevProps, nextProps) || ! shallowEqual(inst.state, nextState);
} 
```

由于使用了“`shallowEqual`”，因此建议“`Pure Component`”不要具有子组件，因为对于在 JSX 中创建且具有子组件的元素，最浅比较的返回将始终是“`false`”。

一个`Stateless Component`也继承了试剂的生命周期功能，而一个`Stateless Component`则没有。

### 但这些特征在实践中有何不同？

想象一下配有多张卡片的屏幕。其中有`nome`、`idade`、`cidade`和`emprego`等各种各样的信息。此外，请注意，这些卡片以 10:10 的比例分页并显示在萤幕上。

这种情况非常适合于使用“`Pure Component`”这样保持相同的信息就不会重新呈现，从而大大提高了我们卡片列表的性能。

现在，`Stateless Components`在较小的元件上有其理想的应用，例如附加到显示资讯`admin`或`membro`的卡片上的标签。这是因为:

1.  它是一个非常小的‘t0’以至于真正需要组成成分。
2.  对于这样大小的`UI`来说，性能损失太小。
3.  此小组件中不需要生命周期功能。

### 结论

分析情况总是应用‘t0’或‘t1’的最佳步骤。但是，如果我们要创造一个黄金法则，

*   Pure Components 非常适合于提高性能和减少组件在其整个生命周期中将渲染的数量。
*   无状态组件具有更可读/更简单的代码，可为小型组件提供理想的功能，从而避免在许多小型文件中分离项目(可能导致完全混乱)。

我希望我的研究能像帮助我一样帮助我。

-再见！