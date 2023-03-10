# 使用外部调试日志进行离线调试的照明云

> 原文：<https://dev.to/brettmn/illuminated-cloud-using-external-debug-log-for-offline-debugging-4od7>

[https://www.youtube.com/embed/dpmWAQA9_04](https://www.youtube.com/embed/dpmWAQA9_04)

你好，我是布雷特和 WIPDeveloper.com。我在 Salesforce 的日常开发中使用照明云。它有一个很棒的离线调试功能。

## 共享日志文件蓝调

但是前几天我遇到了一个问题，我不得不调试日志，因为我没有权限访问。我们正在将变更部署到生产环境中，而我没有生产访问权限。所以我收到了日志邮件。

## 调试通过邮件发送的日志文件

现在，这可能是一个问题，您必须坐在那里手动或通过照亮的云阅读日志，您可以设置它，以便您可以将日志文件粘贴到调试上下文中。所以我将向你们展示如何做到这一点，这样就变得更加明显了。

转到运行，编辑配置。在 Apex 脱机调试器下。

添加一个带小加号的新的

Apex 离线调试器。

现在我要说。

我要给它起个名字。

我认为这些日志级别都不重要。我们要做的是获取一个日志文件，并将其粘贴到日志正文中。

在日志文件的顶部，指定了日志级别。因此，您会看到 Apex 代码调试、Apex 配置信息、调用信息，但您不能在此处更改。所以我不相信这些设置会改变什么。

但是我们可以使用这个日志文件，这只是一个示例日志文件。

现在我可以点击 ok 了。因此，我们从中选择调试日志，或者在下拉外部日志中选择配置，然后按

小虫子让它开始工作，一步一步走过去

现在我们可以进行离线调试了。

我会在这里向您展示代码，但我没有时间来设置完整的示例环境，如果您获得具有适当日志级别的调试日志，如 Apex at finest，您可以看到哪些值被分配到何处，并对您无权访问的组织的问题进行故障排除。

或者您正在将变更集部署到生产环境中。因为代码从来没有被部署过，所以你不能在 org 环境中调出调试日志并一步一步地检查它。因为代码不在 org 中，但是您可以将它拉回到环境中，在那里您可以将它拉回到您要部署的环境的代码库中。在这种情况下，我是从 QA 环境部署的，我们可以一步一步来，因为它是相同的代码，看看问题在哪里。因此，它适用于排除变更、设置约会以及排除实际访问的故障。

## 现在就这样。

记得报名参加 **[每周一次的单口相声！你可以得到我们关于 WIPDeveloper.com 的任何最新信息。](https://wipdeveloper.com/newsletter/)**

利用外部调试日志进行离线调试的帖子[点亮云最早出现在 WIPDeveloper.com](https://wipdeveloper.com/illuminated-cloud-using-external-debug-log-for-offline-debugging/)[的](https://wipdeveloper.com)上。