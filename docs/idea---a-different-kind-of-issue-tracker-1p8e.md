# 想法:一种不同的问题跟踪器

> 原文：<https://dev.to/codemouse92/idea---a-different-kind-of-issue-tracker-1p8e>

我正在考虑带回我刚开始编程时创建的一个项目:*一个基于本地的问题跟踪器*。

我最初的想法是，我需要跟踪个人项目的任务，不想要配置或使用基于 web 的问题跟踪器的麻烦。凭借多年的经验，我已经修正了这个想法。

在任何情况下，我都可能会为自己构建这个，但在此之前，我想从社区获得一些反馈，以确定它是否对其他人有用。

## 想法

以下是我心目中的特色:

问题跟踪器的数据将以标准化的 JSON 格式保存。这样，它可以提交给一个存储库，创建一个网络化的问题跟踪器*而不需要*一个网络界面。

虽然我会构建一个优雅的问题跟踪器应用程序 GUI 来处理这个 JSON 文件，但是标准化意味着可以构建*其他的*。可以为 VSCode、Atom、PyCharm、IntelliJ、Vim、Emacs 等创建插件，允许每个开发人员从他们选择的 IDE 中使用问题跟踪器。

该系统的另一个优点是，当与 VCS 一起使用时，它是有效地分散的。问题跟踪器不会“停止工作”，它永远不需要在服务之间“迁移”,而且如果远程发生了什么事情，您还有大量的本地副本。

VCS 托管的问题跟踪器的另一个美丽的含义是，它可以在提交和关闭的问题之间创建一个隐含的连接，这种连接是双向的。例如，如果您运行一个`git bisect`并发现一个破坏了某些功能的提交，那么您*也会*发现这个破坏性变更所涉及的问题；现在可以重新打开或修改它。

当然，如果将问题数据存放在 VCS 上对您来说不太合适，那么您可以将文件存放在网络共享、云或任何地方！毕竟这只是一个 JSON 文件。

我会将[量化的任务管理](https://standards.mousepawmedia.com/en/latest/qtm.html)完全构建到这个问题跟踪器中，包括一些在现有跟踪器上不可能实现的高级功能。(很多个月前，QTM 实际上是在我的这个问题跟踪的最初版本中开始的。)

我为它创建的应用程序将是小型的、快速的、跨平台的。这里没有不必要的花里胡哨；只是一个干净，优雅，“拿下来”的界面。我也将创建一个 CLI 工具，为那些住在终端。

我也可能是制作 VSCode 插件的人，因为这是我一直使用的 IDE。

## 这是给谁的？

我认为这个问题跟踪器可能对以下方面有用...

*   个人项目，在网上设置一个问题追踪器只是一个麻烦或者是多余的；任何想创建一个`TODO.md`的人都可以用这个来代替。

*   私人项目，人们不希望支付或主持一个成熟的问题跟踪器，并且不能使用大多数免费选项，因为它们不是开源的。

*   学术项目，否则团队会滥用短信和电子邮件来跟踪问题。

*   黑客马拉松项目，在那里你需要一个问题跟踪器正在运行*，这样你就可以开始编码了！*

**   即席工作项目，你必须通过一大堆繁文缛节才能在公司服务器上找到问题跟踪器。

    *   任何花费大量时间在有限或没有互联网接入的情况下编码，但仍然希望看到问题跟踪器的人。

    *   任何不想让*离开 IDE 的人又多了一个理由*。* 

 *## 可能的缺点

我已经意识到这个想法的一些缺点:

首先，因为我们将使用 VCS，它将不是*实时*，并且冲突可能发生。一些个人和团队可能不在乎，因为这可以通过正确使用 VCS 来克服。

第二，它不允许非开发人员轻易报告问题，尤其是这样做需要外部工具和创建 PR。对于许多开源项目来说，这可能是一件负面的事情。

然而，对于那些因为用户而必须定期“清理”他们的问题跟踪器的团队来说，这可能也是一件*有用的*事情。phacility(Phabricator 背后的团队)有这个项目，他们已经开始允许用户向公共*论坛*发布问题，然后手动将有效的问题提交给正式的问题跟踪器。

如果有人创建了一个网络界面(讽刺的是！)具有特殊的`push`权限。

## 以前做过吗？

我能够找到两个类似的项目: [edu-xored/tracker](https://github.com/edu-xored/tracker) 和 [sanpii/git-issue](https://github.com/sanpii/git-issue) ，但是有三件事我建议他们(显然)不要:

1.  一种官方标准化的 JSON 格式，作为规范发布，因此可以构建其他前端。

2.  量化的任务管理集成，它本身提供了许多优势。

3.  一个(可选的)本地 GUI 前端。(`tracker`有 web 接口，都有 CLI 接口，但都没有应用前端。)

## 思想？

这是你能用的东西吗？我忽略了什么吗？请在评论中分享你的反馈！*