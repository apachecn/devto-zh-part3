# 用 Material-UI 和 React Virtuoso 创建漂亮的虚拟化列表

> 原文：<https://dev.to/petyosi/creating-beautiful-virtualized-lists-with-material-ui-and-react-virtuoso-31i3>

如果您没有得到消息， [Material-UI v4 已发布](https://material-ui.com/blog/material-ui-v4-is-out/)。确实令人敬畏的公告；材料设计系统很神奇。以及一个成熟的、流行的、开源的 react 库来实现材料设计规范？我们生活在伟大的时代。

我对 [React Virtuoso](https://virtuoso.dev) 库的目标是提供“无铬”引擎组件，以先进的虚拟化行为补充和增强现有的 UI 库。0.6 版允许您用自己选择的组件替换组件结构。今天，我们将使用 Material-UI 列表组件作为 React Virtuoso 中的容器来构建一个具有材质外观的虚拟化列表。

最后的结果看起来是这样的:
[https://stackblitz.com/edit/github-hv734y?embed=1&view=preview&](https://stackblitz.com/edit/github-hv734y?embed=1&view=preview&)

准备好了吗？走吧。

## 第一步:Hello World

相对适度的一步——我们启动了一个新的 Stackblitz react 项目，并添加了初学者 Virtuoso 示例——一个包含 500 个`Item {index}`条目的列表。Virutoso 组件配置简单；你传递一些维度(你可以传递任何东西，包括`%`、`rem`等)，要渲染的项目总数，以及`item`渲染道具。组件会为您解决剩下的问题。

[https://stackblitz.com/edit/github-hh6j8f?embed=1&view=editor&file=src/index.js](https://stackblitz.com/edit/github-hh6j8f?embed=1&view=editor&file=src/index.js)

## 第二步:添加材料界面

让我们把这份清单做得时尚一点吧！默认情况下，Virtuoso 将其列表容器呈现为一个`div`，并将每个单独的项目呈现在一个`div`包装器中。我们通过指定`ListContainer`和`ItemContainer`属性，将它们与它们的 Material-UI 对应物进行交换。他们渲染`ul`和`li`为我们工作！

[https://stackblitz.com/edit/github-ybd8u6?embed=1&view=editor&file=src/index.js](https://stackblitz.com/edit/github-ybd8u6?embed=1&view=editor&file=src/index.js)

## 第三步:添加真(假)数据

显示`Item 40`很无聊，而且很可能不是你的项目所需要的。让我们展示真实的数据。或者至少看起来更有说服力。下一步，我们使用 [awesome faker 库](https://www.npmjs.com/package/faker)生成一组 500 条用户记录，并将它们显示在列表中。我们还通过给每个条目添加一个头像来使列表结构变得更加复杂。为了跟上趋势，我们通过将用户记录生成放在一个可重用的钩子中，将数据与表示分离。

[https://stackblitz.com/edit/github-4pvb43?embed=1&view=editor&file=src/data.js](https://stackblitz.com/edit/github-4pvb43?embed=1&view=editor&file=src/data.js)

## 最后一步——无休止的滚动

这是练习中最有趣的部分。一步加载 500 条记录并不是一个好主意。相反，我们从 50 条记录开始列表，当用户向下滚动时添加更多记录。我们在上一步中做的钩子派上了用场；我们进一步扩展它，以公开一个`loadMore`方法，该方法模拟到服务器的往返，并向现有数组追加更多记录。`loadMore`调用连接到 Virtuoso 的`endReached`回调属性。

我们还引入了另外两个小的改进:一个在用户滚动过快时显示`loading...`的页脚，以及一个控制加载更多行为的急切程度的`overscan`属性。【T2[https://stackblitz.com/edit/github-hv734y?embed=1&view=editor&file=src/index.js](https://stackblitz.com/edit/github-hv734y?embed=1&view=editor&file=src/index.js)

我们完了！它比预期的更短更简单吗？你还在寻找更多吗？如果是这样，去查看文档中的高级示例-除了上面的，它显示了粘性项目的分组，通过在滚动期间隐藏头像来优化滚动体验。