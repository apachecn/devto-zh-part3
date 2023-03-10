# VS-Code |了解您的工具#1

> 原文：<https://dev.to/where_i_stuck/know-your-tools-vs-code-5ckd>

生产力每个人都想要。每个人都认为他们是。但是我们能改进吗？

我认为一个方法是了解我们的工具。他们能做什么？它们提供的功能是什么？我们不是把它们都用光了吗？我们没有充分利用它们的力量吗？

在这里，我分享了我使用的 VS-Code 的 5 个生产力特性。

无论您正在构建什么样的应用程序，或者遵循什么样的堆栈，都没有关系。每个开发人员都有自己喜欢的文本编辑器，或者至少在默认情况下他更喜欢使用一个。正如你从标题中猜到的，它是 VS-Code。

我想分享的不是如何安装它或者为什么它比其他的好，因为我们有大量的其他资源。我能提供的是我用 vs-code 提高生产力的经验。

> 我们会尽量远离老鼠。

让我们开始旅程吧

[![Starting a journey](img/3113e6c7a3facb8c6f9eddcc77e5012d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nhId2uM8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tdsq8cks88lud7l1fcv0.jpg)

## 1。集成终端

这个功能节省了我很多时间。如果你不使用它，你可能会发现自己一直在文本编辑器和终端之间切换。我使用我的 VS 代码进行 Angular 和 Rails 开发，对于 Angular 来说，这并不重要，但对于 Rails 来说，当我制作模型和使用 Rails 控制台时，它会有很大帮助。

这是什么？没什么，和你的终端一样。现在可以在文本编辑器中看到它，如图 2 所示。这里 VS-Code。

我建议如何使用它，制作一个键盘快捷键来切换终端和文件之间的焦点。下面是我的`keybinding.json`向你展示我是如何做到的:

```
[
// Toggle between terminal and editor focus
{ "key": "ctrl+`", "command": "workbench.action.terminal.focus"},
{ "key": "ctrl+`", "command": "workbench.action.focusActiveEditorGroup", "when": "terminalFocus"}
] 
```

我使用内置的终端来执行 git 提交，并在单独的终端中运行 app server(为此我使用 iTerm 2)。

再次特别指出，我在这里使用 git rebase、stash、pull 和 push 命令。对于我常用的命令，比如 commit(带有小消息)，add 和 diff VS code 提供了一个很好的方法。

多会话也是我 ssh 到 EC2 实例时使用的一个工具。您可以通过按右上角的小+号打开另一个会话。

[![VS code terminal](img/f4b52fe70003d7268f642d1e62090934.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YnmJf0kZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zv9ivr0d0n89znua47ot.png)

我这样做的原因是，当我从 iTerm 会话中 SSH 到我的 Rails 和 Angular 应用程序的 EC2 实例时，我很快就忘记了哪个终端是哪个实例。因此，现在我在 vs-code 终端中这样做，它让我清楚地记住了这一点。

## 2。Git 支持

在对 VS-Code 的 git 支持中，我最喜欢的是它如何显示 git diff 的输出。我们实际上并不在终端中编写命令。他们为此提供了一个 GUI。

单击左侧图标面板中的 git 分支徽标，然后单击您想要查看其 git diff 的文件。我在下面展示了一个例子。

[![Git diff photo](img/c1b09480c24f4eb4bb279d6369355e1d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8VzK-hvn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1te3e1cj7rnbpgc5psq6.png)

我只是在任何一个 Angular 项目中编辑了一个文件。你可以看到，它显示了删除的内容和添加的内容，即发生了什么变化，这是多么令人惊讶。这太棒了，对我来说，我认为比 git 在终端中显示输出更好。Git 工程师在制作`git diff`的特性上付出了大量的努力，我很尊重这一点，也很喜欢它，这只是让它变得更容易。

**去添加，去委员会**

我还使用 VS-Code GUI 功能在 staging 中添加文件，如果我有一个小的提交消息，就提交它们。

[![Git panel of VSCode](img/6550f3272b68e6d6eb383b8aafa171fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V-RJ9ilv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vlec4s4h3bgwace94c3p.png)

我们可以通过使用左边的面板来实现这一点。小+号用于将相应的文件添加到暂存状态，向后弯曲的箭头用于恢复更改，您可以看到文件名上方的输入字段用于提交消息。对于中等大小的提交消息，我使用集成终端和相同的旧 vim 方式。

我为什么用这个？因为与使用`git add <file path> command`添加文件相比，我可以非常快速地将文件添加到 staging 部分。在 angular 项目中写文件路径非常无聊，因为有很多子文件夹和子子文件夹，是的，即使使用`tab`。

**[Git Lens(围绕 git log 最好的东西)](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)**
它是 VS-Code extensions marketplace 中可用的插件/扩展。我只是喜欢它如何显示任何文件的历史，根据哪个用户在该文件中做了什么。

[![History of files by Gitlens](img/ccac4c032a1e1c48148488d87aed34ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4AQpbRlR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d3mz9ohpm8uqr67kff26.png)

上图是一个文件历史。当它被添加时，即 15 天前，谁添加了它，它是在什么时候被添加/更改的。提交哈希位于左侧面板的“文件历史记录”下。

它显示了一个文件的完整的 git 日志，没有写任何 git 命令。是不是很牛逼。此外，该功能还扩展到您拥有的所有远程下的所有 git 分支。我在图 6 中添加了一瞥。(虽然我只有一个遥控器)

[![History of branch](img/20ec4174c2de4b562124194356f11110.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zLsfYTty--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5r00go3hkdmuxu4jt7qu.png)

这些功能有助于快速使用 Git，并在 Git 日志和所有合并冲突中保持跟踪。

## 3。峰值清晰度

从事任何大型项目或在多个文件中分布大量代码的项目都会导致从一个文件跳到另一个文件的频率更高。

如果你只是想在其他文件中看到一些方法定义，然后返回到你的原始文件，这对于像我一样的你来说是一个很大的工作，那么这是一个节省。

[![Peak definition](img/63e10d5245df3c1a8d87cefdd2f278f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b4ZAbGYS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s25w3yeocaobew4784x9.png)

`right click + peak definition`或者说`press ⌥F12`，就是打开这种美的方法。它将向您显示定义，如果您愿意，您也可以在峰值文件中进行更改。在情况下，如果有多个关键字匹配的定义，它会显示所有这些，你可以选择正确的。但是我们打破了不碰鼠标的规矩。

它让我从频繁更改文件的头痛中解脱出来，而且看起来很酷。太酷了

我仍然需要打开深埋在文件夹树结构和大量文件夹中的文件。为此，我使用以下工具:

## 4。[指挥托盘](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)

这是所有新文本编辑器的共同特征。点击`Cmd + P`然后写下你想打开的文件名，根据匹配结果会出现一个下拉菜单，你可以从中选择。快速和简单的方法来访问文件，好的部分是，它还打开了文件夹中包含该文件在左面板，并集中在当前打开的文件。

您可以使用命令托盘做很多事情，比如运行内置任务或自定义任务，打开文件，以及 VS-Code 提供的任何类型的命令，您都可以从那里运行。对于如何做这个和那个点击这个[链接](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)。

## 5。调试器

我喜欢红宝石中的 [**byebug**](https://github.com/deivid-rodriguez/byebug) 宝石。Rails 中的调试是小菜一碟，感谢辛勤工作的工程师让我们的生活变得轻松。但是当谈到 javascript 时，事情就不那么不言自明了。如果你了解 Rails，你一定用过 byebug 或者[T5】pryT7】gem 进行调试。](https://github.com/pry/pry)

通过在任何时候停止你的服务器并查看当时的状态，这很有帮助。

在 JavaScript 中，当我需要调试时，我在任何地方都使用`console.log`。这是我在任何情况下处理 JS 的目的。然后是 VS-Code 的调试器。它通过在任何点停止代码并查看应用程序的状态来使体验相似。

我需要做的只是在我想要停止代码并运行调试器的行号旁边标记一个红点。

它需要配置一个文件名`launch.json`,这样它就可以知道目标服务器和运行应用程序的平台。对于我的 Angular 应用程序，我使用调试器的 chrome 插件，并将我的默认 localhost:4200 标记为要运行的服务器端口。按照此设置和使用调试器。

这是我在 VS-code 中使用的 5 个最好的特性，用来最大化我的生产力和简化我的开发过程。

在结束之前，VS-Code 还有一个非常有趣的特性，那就是[实时分享](https://code.visualstudio.com/blogs/2017/11/15/live-share)。如果你做了很多结对编程，这是非常惊人的。Live Share 使您的团队能够在相同的代码库上快速协作，而无需同步代码或配置相同的开发工具、设置或环境。

他们有一个很棒的视频来解释这一点，我没有用过这个功能，但我认为这是目前市场上最好的产品之一。

[![Congrats](img/976689d243f717e726fd26f4130e2e03.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P7s9Q-j8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e067ob7doljgevgb2w1w.png)

如果你坚持到了最后，那么恭喜你现在知道你的工具了。它的强大功能，现在你可以使用它的全部潜力，提高你的工作质量。

点击下面的评论区，如果我错过了任何精彩的功能和你对此的想法，请告诉我。