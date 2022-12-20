# 你照顾你的工具，你的工具照顾你

> 原文：<https://dev.to/mokagio/take-care-of-your-tools-mn4>

在科幻小说《迪亚马特的愤怒》中，其中一个角色特蕾莎发现她的太空机械师朋友住在一个洞穴里，正在维修他的一个食物回收器。
当她指出他花很多时间把机器拆开再组装起来时，他回答道:

> “你照顾你的工具，你的工具照顾你。”

特蕾莎的朋友照顾他的食物回收器，因为他的生存依赖于它。作为软件开发人员，我们的生存依赖于我们对代码库和其中使用的工具的关心程度。

软件开发人员有许多工具。我们选择的 ide 和文本编辑器、我们正在使用的编程语言、我们正在构建的应用程序框架、我们用来外包解决我们领域之外的编码问题的第三方库、作为我们构建和部署过程的一部分运行的 linters 和 helpers。这个清单还可以继续下去。

## 如何打理你的软件工具

为了照顾我们的工具，我们不一定需要像提摩太一样把它们拆开来检查。维护过程中最重要的部分是处理警告，确保我们使用最新版本的工具，并移除任何不用的东西。

### 一看到警告就立即处理

无论您使用的是有编译器的语言，还是依赖 linters 和运行时监控的语言，在看到警告和违规后都要尽快解决。

想象一下，你做了一次血液测试，一些结果值上有一个警告标志。你会尽快去看医生以了解更多信息，还是会等到下周一，看看你是否能在计划中改变一些事情，以便找到时间去检查一下？

如果构建或部署过程是成功的，那么在构建或部署过程中出现警告可能是无害的，但是它们的出现是有原因的。在未来的版本中，一些警告实际上会变成错误。

任何数量的警告都大于零意味着当一个新的警告出现时，你不会注意到它。这可能是一个警告，最终导致你的产品出现严重故障。

### 保持您的工具最新

IDE、文本编辑器、插件或库的每个新版本都带来了性能增强，有时还带来了您不想错过的安全性改进。当一种编程语言的新版本发布时，你可以期待新的特性会使你的编码更容易。

举个例子，Swift 4 引入了 [`Codable`](https://developer.apple.com/documentation/foundation/archives_and_serialization/encoding_and_decoding_custom_types) ，这是一种对类型进行编码和解码的类型安全且优雅的方式，并提供了翻译 JSON 的默认实现。
这消除了使用第三方库来解码 API 响应的需要，消除了大量开销以及关于哪个库最适合使用的固执己见的讨论。

每次更新**后，阅读发行说明**。你永远不知道，你可能会发现有一个新的功能可以准确地解决你过去两天一直在处理的问题。

提示:对于 GitHub 上的开源工具，您可以订阅它们发布的 RSS 提要。您会发现它将`/releases.atom`添加到您项目的 URL 中。比如说[github.com/apple/swift/releases](https://github.com/apple/swift/releases.atom)。

### 删除未使用的代码

未使用的代码会堵塞你的软件，使一切都变慢。

如果你有一门可以编译的语言，那么你将会在那些永远不会运行的代码上浪费编译时间。如果你的测试覆盖了未使用的代码，那么你会让反馈循环毫无理由地变慢。
如果您有执行静态分析的工具，那么他们会花时间查看那些包含不会使用的代码的文件。

尽快摆脱死代码，以保持项目的精简和敏捷。

## 以后是再也没有了

这些建议的一个共同点是，你越早采取行动越好。

以后是永远不会。总会有比保管工具更重要的事情。如果你在力所能及的时候不采取行动，你最终会为此付出代价。

等待升级或清理也有隐性成本。如果我们同意像保持你的工具最新和删除不用的代码这样的活动对你的代码库是有益的，那么你等着对这些活动采取行动的时间越长，你的软件就越不可能尽善尽美。如果一个库的最新版本使它工作得更快，你等待升级的时间越长，你的软件就会比它需要的更慢。

最后，最好按照自己的时间表进行升级和维护，而不是在你赶着做其他事情的时候。没有什么比因为 upstream 需要比你现有的更高版本的组件，升级过程并不简单，或者因为你一直使用的 API 现在已经过时而无法发布错误修复更糟糕的了。

## 更新疲劳

不过，让我们实话实说。有时照看软件项目的工具可能是一份全职工作。
其实越来越多的公司都有基础架构、工具、开发者体验的角色，甚至团队。

如果你在一家小公司工作或者独自工作，保持你所有工具、框架和库的最新版本是一个挑战。

在这种情况下，你可能想引入操作程序，比如只升级到新的[次要版本](https://semver.org/)。
在你的`README.md`中写下这些规则，并表明你是在有意妥协，以避免花费过多的时间来升级依赖关系。

花大量时间照看您的工具也可能是一个信号，表明您有太多的工具，或者有些工具不够稳定。每个依赖都伴随着维护开销。
这种成本是否值得依赖带来的收益？
如果答案是否定的，那么你最好把它去掉，为它实现一个定制的解决方案。

* * *

如果你致力于构建一个长期的软件项目，一个从现在起六个月、一年、两年后你仍然喜欢工作的代码库，那么你需要投入时间来照看它和它的工具。

注意投资这个词。这不是骑自行车，也不是极客需要永远保持最新版本，因为它很闪亮。保持代码库及其工具的最佳状态是一个效率问题。
现在是时间投资，以后节省时间就会有回报。

“你照顾你的工具，你的工具也会照顾你。”

*这最初发布在[mokacoding.com](https://www.mokacoding.com/blog/take-care-of-your-tools/)上。*