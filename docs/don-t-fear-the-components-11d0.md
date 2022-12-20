# (不要害怕)组件

> 原文：<https://dev.to/juanigalan91/don-t-fear-the-components-11d0>

我已经使用 React 几年了，这些年来，我注意到有一个开发生命周期♻️，它倾向于为每个组件重复自己。我们在单个文件上创建一个简单的组件，不超过 30 - 40 行代码，您可以在其中定义它的工作方式、内部状态以及如何呈现给最终用户。您还可以创建一个测试文件，在其中测试您实现的逻辑，传递不同的属性并查看您的组件的行为。

到目前为止，一切看起来都很好！你的组件是可重用的，有它自己的测试，它负责一个简单的任务。但是，一个新的特性出现了，我们需要给它添加一些代码。我们添加那些变化，我们调整我们的风格和测试来匹配它们，瞧！我们的组件显示了这个新的很酷的特性😎😎我们仍然将它封装在单个组件内的单个文件中。

# 雪分量效果

然而，我们知道这并没有结束。新特性不断出现，一个曾经只有 40 行代码的组件变成了一个非常复杂的文件，有 500 多行代码负责多种职责，我们的测试和样式表也开始遭受同样的症状。我称之为 *snow 组件效应* ❄️❄️，我们不断向组件添加代码和特性，然后它就变成了这个怪物，在我们的整个发布中困扰着我们👻👻

那么如何才能把这种影响降到最低呢？为了发现这些情况，我们需要注意什么？我们如何重构我们的代码来避免 snow 组件的影响？

# 实际例子

我决定最好通过一系列代码示例来看看这些场景，这些代码示例说明了一个简单的组件如何快速升级，以及什么样的代码气味告诉我们“嘿，也许我们可以将它移到另一个组件中！”。这些代码有味道👃👃这并不意味着你做错了什么，它们只是一些指针，可以帮助你弄清楚**你正在做的事情对你当前的场景**是否有意义，并且可能发现有更好的方法！所有这一切背后的想法是代码对你和你的团队有意义，并且你所做的决定不会在将来束缚你。

因此，我创建了一个小网站，展示最新的 PS4 游戏，显示标题、图像和游戏本身的链接:

[![](img/26189a0d58c0af606f6bc8b6cc89146a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wrS8DXy9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1b2r7n29zf83j2ouh71n.png)

快速浏览一下组件，我们可以说它看起来足够简单了🤔🤔？我们的组件应该有一个游戏列表，每个游戏都有一个标题、一个图像源和要重定向的链接，我们将遍历这些游戏，并为每个游戏创建链接标签和图像标签: