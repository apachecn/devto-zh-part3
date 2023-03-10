# 如何从自定义的 UITableViewCells 中获取数据？

> 原文：<https://dev.to/evandeaubl/how-do-you-get-the-data-from-your-custom-uitableviewcells-3fbb>

在您需要处理表格之前，您不需要在 iOS 开发中走得很远。这意味着:UITableView，可能还有你自己定制的 UITableViewCells。一种常见的模式是在这些单元格中设置可编辑的控件，以便用户可以从表格本身更改数据。现在你面临一个问题:

如何将数据传输回 UIViewController，以便应用程序可以使用它？

您知道如何使用标准控件来实现这一点，如 UITextFields、UIButtons 等。但是你有一个 UITableView 和一堆 UITableViewCells。他们的行为方式并不完全相同:

*   它们是由 UITableView 动态构建的
*   它们的数量是可变的(通常)
*   从 UITableView 访问它们并不容易
*   你不能像对待“普通”ui 视图一样对待它们，比如在界面构建器中使用 outlets 把它们连接起来

但是把这些都撕掉，你的不可视单元格就不是特殊的雪花了，它们就像其他的一样。你应该完全那样对待他们。那么答案就变得简单了:当他们的控件需要发回状态时，你可以像 Apple 一样在 UIViews 中做同样的事情。使用委托。以下是方法。

* * *

[![Multiple choice app mockup](img/1f2df6b84a24ceac512637dc9bb0de48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Td-7ZXFq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.appsdissected.com/wp-content/uploads/2019/02/Screen-Shot-2019-02-11-at-12.17.56.png) 为了设置阶段，假设你正在编写一个多项选择测试应用程序，其中你有一个包含 UITableViewCells 的 UITableView，每个包含 3 个按钮来指示用户可以点击的答案。您需要接收该信息，以便能够在用户完成后确定答案是否正确。

TK 插入来自游乐场的快速截图？

首先，定义您的委托协议。你怎么定义是个人喜好问题。在这种情况下，我们可能不需要委托来理解 AnswerCellView 的 UI 是如何构造的，所以让我们只发送索引:

```
protocol AnswerCellViewDelegate {
    func answerCellTapped(answerCellIndex: Int, answerSelected: Int)
} 
```

现在将您的委托添加到您的自定义 UITableViewCell 类中:

```
class AnswerCellView: UITableViewCell {
    // ...

    weak var delegate: AnswerCellViewDelegate?

    // ...
} 
```

确保将委托变量定义为弱变量。通常，传入的委托属于外部 UIView 或 UIViewController。如果不在 UITableViewCell 中将委托定义为弱委托，可能会导致保留循环和内存泄漏。

接下来，您需要通过委托将按钮点击转换为调用，以传达状态更改(对于 UIButons，您应该在构建 AnswerViewCell 时使用 UIButton.addTarget 注册这些方法，或者如果您的单元 UI 在 XIB 中，则将它们连接到 outlets):

```
class AnswerViewCell: UITableViewCell {
    // ...

    func button1Tapped() {
        // index is a field set up on construction
        delegate?.answerCellTapped(index, 1)
    }

    func button2Tapped() {
        // index is a field set up on construction
        delegate?.answerCellTapped(index, 2)
    }

    func button3Tapped() {
        // index is a field set up on construction
        delegate?.answerCellTapped(index, 3)
    }

    // ...
} 
```

在本例中，我们将让 UITableView 充当委托，并从各个 AnswerViewCells 收集信息:

```
class AnswerCellTableView: UITableViewDataSource, AnswerCellViewDelegate {
    // ...

    func answerCellClicked(answerCell: AnswerCellView, answer: UIButton) {
        // do the work to keep track of which buttons have been clicked
    }

    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        var answerCell: AnswerCellView
        // construct your AnswerCellView, setting the delegate property on your cell to self
        // ...
        answerCell.delegate = self
        // ...
    }

    // ...
} 
```

您还可以让您的主 UIViewController 或视图模型对象实现委托，这取决于您的应用程序的架构。

当您需要从 AnswerCellTableView 收集该信息时，您可以提供一个访问器方法，UIViewController 可以使用该方法来获取选定的答案，或者如果您需要实时接收更新(例如提交到外部数据存储)，请将 UIViewController 作为委托，或者如果您希望保持适当的代码分离，请重复委托模式并让 UIViewController 为 AnswerCellTableView 提供一个委托对象。[是一路下来的代表们！](https://en.wikipedia.org/wiki/Turtles_all_the_way_down)

* * *

你喜欢这个建议吗？下一个关于[开发一个克隆应用程序对被雇佣为 iOS 开发者的好处](https://www.appsdissected.com/clone-app-learning-getting-hired/)的提示已经在等着你了。或者[注册，让每一条建议都直接进入你的收件箱。](https://www.appsdissected.com/newsletter/)

这篇文章最初发表在 [Apps 剖析。](https://www.appsdissected.com/uitableviewcell-data-delegates/)