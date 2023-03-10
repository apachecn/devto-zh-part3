# 钩状的

> 原文：<https://dev.to/jovidecroock/hooked-form-1625>

一个简短的介绍，嘿，我的名字是 Jovi，我是一家比利时公司的 web 和移动工程师，这是我第一次发布开发，鉴于即将发布的 React-hooks，我决定尝试一下。

我第一次听说钩子时，我很快意识到这是减少特定库所需的包大小以及提高代码整体可读性的好方法。当类被向下编译时，意味着一个开销，在这个时间点上，库的作者总是向下编译库以支持几乎所有的浏览器。本质上，我们可以远离这一点，但这是另一个时间的讨论。

这就是为什么我觉得有必要做两个库[一个带钩子的工具带](https://github.com/JoviDeCroock/React-angler)和一个[表单库](https://github.com/JoviDeCroock/Hooked-form)。

表单库将是本文中唯一讨论的，它遵循`Form` / `Field`方法。这种方法被像 [redux-form](https://redux-form.com/8.1.0/) 这样的流行图书馆所使用。

我对结果很满意，因为它最终是一个 2.8KB 的压缩包，比其他流行的包要小得多。

## 挂钩

该库公开了一组挂钩:

*   斯科菲尔德
*   使用错误
*   useFieldArray

这些钩子可以在你的组件中用来构造组件，当传递一个`fieldId`时，这些钩子将自动获取方法、值和元信息。

## 组件

该库还公开了一组组件，有人可能会问，当我们已经有能力自己制作这些组件时，为什么还要做出这样的选择。

对于组件，我选择了一个更优化的版本，这些组件会记住它们必须呈现的组件，并且只在它们的值、错误，...

所以每个钩子都有一个组件:

*   田
*   错误
*   FieldArray

但是还有一个，那就是`Form`组件。我选择将容器作为一个特设容器，因为其中使用了许多钩子来执行验证，...

## 举例

我在一个基本工作形式的代码沙箱中做了一个小例子:[https://codesandbox.io/s/k8mylo9lo](https://codesandbox.io/s/k8mylo9lo)

这个例子展示了 Field 和 FieldArray 的基本用法。关于钩子有趣的事情是，当你想覆盖现有的`Field`实现时，你可以通过抓取`useField`来自由地这么做。它将为您传递的字段返回值/方法，您可以自己实现它。

## 总结性的

我在我的一个[副业](https://github.com/worldpins/web/blob/master/src/modules/maps/create/Pin.tsx)中积极使用`hooked-form`，我对它的结果非常满意。

[您可以在文档中了解更多信息](https://jovidecroock.github.io/hooked-form/docs/components/form)

我希望你喜欢这篇文章，并欢迎对它的反馈！

如果你想了解更多关于表格和我的选择背后的理由，你可以在这里阅读更多:

[字段-表单方法](https://www.jovidecroock.com/forms/)
[钩型表单选择](https://www.jovidecroock.com/forms/hooked-form/)