# 如何使用状态变量而不在循环内的不同组件之间共享

> 原文：<https://dev.to/indiscover/how-to-use-state-variables-without-sharing-among-different-components-inside-a-loop-54fc>

我在一个循环中有两个卡片组件，如下面的代码所示。在卡片中，我有一个文本字段和一个添加按钮。点击“添加”按钮后，文本字段中输入的值将被填入卡中。但是当我这样做的时候，另一张卡也显示了相同的值。这意味着，假设我在第一张卡的文本字段中输入了一个值，那么第二张卡中也会显示相同的值。我知道会发生这种情况，因为我在循环中使用了相同的状态变量，并且在循环中的其他卡组件之间共享。我找不到一个解决方案，如果我在一个卡组件的文本字段中输入一些东西，那么它将不会反映在循环中的其他组件上。

下面是代码:
[https://codesandbox.io/s/silly-taussig-kgbot](https://codesandbox.io/s/silly-taussig-kgbot)