# 在 JavaScript 中模拟 Xmonad 第 4 部分

> 原文：<https://dev.to/luke/emulating-xmonad-in-javascript-part-4-45b6>

[![](img/093193ad8f4d9905d6fc297aa59ab29f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aaH7ukT---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r5ezkfecpm8of4lpjbsr.gif)

自从我上次发布以来，发生了很多变化。也就是说，我最终给了这个项目一个 [GitHub repo](https://github.com/lukebarnard1/nomad-term) ，供任何想跟踪我的进度甚至试驾的人使用。

## 短版

以下是更新的快速列表:

*   这个项目不再被称为“shmonad”，而是称为“游牧者”。这有两个原因:使用它的人几乎肯定是开发环境的流浪者，其次，当你去掉“x”时，它是 xmonad 的变位词。看到我做了什么吗？
*   正如我提到的，nomad 现在有了一个 [GitHub 页面](https://github.com/lukebarnard1/nomad-term)，尽管它没有正式发布——使用风险自负。
*   Nomad 现在支持背景和前景色！
*   子终端缓冲区和滚动边距的实现现在更加健壮了。

# 加长版

我最喜欢的实现终端的资源[https://docs . Microsoft . com/en-us/windows/console/console-virtual-terminal-sequences](https://dev.topublicised%20by%20Microsoft)使得这次更新成为可能。我一直在研究这个页面，为了更好地理解终端，也为了实现一个改进的 nomad 底层模型。

两个最大的增加是对格式化序列和滚动边距的支持。

## 格式化

nomad 现在支持前景色和背景色。这里最大的挑战是理解如何模拟 ANSI 终端格式状态，以及如何在插入文本时应用它。本质上这个问题可以按照这个图来划分:

```
 // Diagrams to show the insertion of format and
        // it's effect on existing formats, f and the resulting
        // formats, r.
        //
        // format         |---------|
        // f0:              |--|
        // r0:            |---------|
        //
        // f1: |------------------------------|
        // r1: |----------|---------|---------|
        //
        // f2:                   |--------------|
        // r2:            |---------|-----------|
        //
        // f3: |--------------|
        // r3: |----------|---------| 
```

上图显示了当插入一个新的文本字符串时，nomad 如何拆分底层格式状态。每行包含一个文本字符串，长度等于视口的宽度。每一行都有一系列格式状态，包括起始索引、长度和如何给文本着色的定义。

我选择的实现负责更新一行的格式序列的函数的方法是 TDD。这里创建的测试已经成为 nomad 的第一个测试，我希望添加更多的测试来覆盖绝大多数的终端特性。改天再谈这个吧！可以说，一个大的重构真的有助于使 nomad 的组件更易测试。

## 滚动页边距

一些程序(如 vim)需要正确实现*滚动余量*，在插入行、删除行以及上下滚动时，缓冲区的行在此范围内移动。以前，nomad 基于“滚动偏移量”工作——一个单一的标量值，表示缓冲区内的视口偏移量。这通常不是终端表示状态的方式，主要是我自己的估计。

ANSI 终端实际上使用视区的一个区域来表示哪些行在特定条件下滚动，哪些行不受影响。这对于基于 GUI 的程序中的标题栏和页脚很有用。

作为调试的一部分，我发现仔细查看 vim 的输出序列非常有用。这让我能够辨别维姆在某些互动中试图做什么，以及诺曼对此的反应是什么。

理想情况下，这可能是基于 TDD 的方法的另一个机会，但在这种情况下，nomad 的编写方式不允许这样做。我目前的想法是，终态的每个部分都可以被认为是独立的，因此很容易测试。

# 鳍

请随时留下评论和分享你的想法，如果你渴望更多这样的帖子，请关注我！:)

下次见，谢谢你的阅读。