# 将 MobX 与 React 挂钩一起使用

> 原文：<https://dev.to/ryands17/using-mobx-with-react-hooks-part---2-8ac>

这篇文章假设读者熟悉 MobX。

#### 测试是你开发工作流程的重要部分。

在我之前的文章中(如果你没有读过，请读一读)

[![ryands17](img/f6d17352ba331040f075759f6b3e00ff.png)](/ryands17) [## 将 MobX 与 React 挂钩一起使用

### Ryan Dsouza 1 月 5 日 192 分钟阅读

#mobx #react #hooks #javascript](/ryands17/using-mobx-with-react-hooks-52h5)

我已经展示了两个简单的例子，一个是如何通过`mobx-react-lite`包使用 MobX 定制钩子，另一个是如何通过内置的`useContext`钩子使用传统的 MobX 类存储。在这篇文章中，我将通过[Kent c . Dodds](https://kcd.im/)superb[react-testing-library](https://github.com/kentcdodds/react-testing-library)演示如何测试这些使用钩子的组件。请务必查看文档，了解如何在测试中呈现 React 组件。

为了简单起见，我使用了我以前的帖子中使用的相同的 CodeSandbox 示例。您还可以同时查看测试和输出。

对于第一个例子中的**，我们使用了`useObservable`钩子，这个组件很容易测试。**

[https://codesandbox.io/embed/8y05z5p4q8](https://codesandbox.io/embed/8y05z5p4q8)

如果您查看`index.test.js`文件，有两个测试，

1.  检查列表是否已呈现。
2.  测试 todos 切换是否会更改剩余的项目。

在第一个测试中，我们使用 Jest `expect`断言检查列表项和页脚文本的长度。

在第二个测试中，我们使用`fireEvent`方法在列表项上触发了一个点击事件，其中触发了`toggleTodo`方法来改变 todo 的`completed`参数。这反过来改变了我们的**页脚**组件的文本，我们可以使用 Jest 断言再次测试它。

对于**的第二个例子**，为了测试我们通过`useContext`在组件中使用的 MobX 类，我们需要创建一个虚拟组件并导入我们的存储，就像我们通常在任何其他组件中做的那样。

[https://codesandbox.io/embed/3kmkj56qm](https://codesandbox.io/embed/3kmkj56qm)

如果您查看`index.test.js`文件，就会创建一个虚拟组件，它使用`useContext`钩子来使用我们的 MobX 类存储。我们只需根据我们的 MobX 商店定义我们需要测试的最小 UI，并编写我们的测试。

测试与第一个示例相同，但这里唯一的变化是我们创建了一个虚拟组件来测试我们的 MobX 存储，以模拟我们的主要 React 组件。

若要查看测试的输出，可以打开编辑器底部的“测试”面板，CodeSandbox 会在其中自动运行测试。

请随意打开沙箱，摆弄那些测试和组件。感谢阅读！