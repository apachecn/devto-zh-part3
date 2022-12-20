# 将 SFDX 与沙盒一起使用

> 原文：<https://dev.to/brettmn/use-sfdx-with-a-sandbox-34i7>

[https://www.youtube.com/embed/4L_H2pDNqNI](https://www.youtube.com/embed/4L_H2pDNqNI)

你好，我是 WIPDeveloper.com 的布雷特。最近，有人问我如何使用 SalesforceDX Visual Studio 代码扩展来开发沙盒或非临时组织。SalesforceDX Visual Studio 代码团队已经发布了一个关于如何做到这一点的演练。但是，如果你按照指示，可能会有一些问题，所以我只是要做一个例子设置真正的快速，我会提供一个链接，下面的文件，其中描述。

## 针对现有组织工作

首先，你需要打开 Visual Studio 代码，打开命令 pallette，然后你使用命令`SFDX: Create Project with manifest`我第一次尝试时有点困惑，因为我还没有清单，我认为这意味着你必须在创建时就有清单。它实际上是在创建项目，同时也创建了清单，所以此时您不必担心这个问题。只需选择`Create Create Project with manifest`

给它起个名字。

我打算把它命名为 testOrg，然后选择一个位置。我会把它写进我的文件里。

请稍等，当它生成一个项目时，您可以看到其中一个文件夹中显示有一个产品`package.XML`。如果你只是要做一些基本的 Apex 开发，你不必定制这个。

它有 Apex 类、Apex 组件、Apex 页面、Apex 测试套件、Apex 触发器、Aura 和静态资源。如果您打算在 Salesforce 中进行一些开发，这些是您可能需要的基本东西。

现在，我们将通过打开命令调板并转到`SFDX: Authroize Org`来授权我们的艺术，然后选择类型，或者您将授权反对我将使用生产登录，因为我只有一个开发人员工作，没有沙箱。

我将使用 VScode。org 作为名称或别名。

现在，它打开了一个 web 浏览器，要求我登录我的 Salesforce 组织，当我登录时，我会这样做。

现在，它将询问是否允许这样做，我将回答是，因为我想通过 CLI 访问 org。

我之所以称它为 CLI，是因为它完成了 Visual Studio 代码插件通过 CLI 与 org 对话的所有工作

如果您查看这里的输出，您可以看到我们可以使该组织获得授权，如果需要，我们现在可以关闭浏览器

敬。下一步将是检索我们的源代码。

是的，从组织中检索来源。因此，在这里，我们右键单击包并从 org 中检索清单中的源代码。

现在它要做的是获取 org 中的任何内容，并将其放入`force-app`。

我不认为我在 org 里有任何东西，所以不会有太多可看的。

是的，是空的。所以没有找到结果

如果我至少有一个顶点类，这将会更有戏剧性。

说到顶级课程，我们可以去

现在创建一个 apex 类。我打算把它叫做`test`

我想为它做一个文件夹。

我要做一个`classes`文件夹。

现在一切都错了位置。因此，我将把 classes 文件夹移到 default，我将把我的

测试类并将它们移入类中。

现在我有了一些 Apex，可以将它部署到我的组织中。为此，我可以右键单击并选择在文件夹或文件本身中将此源部署到 Ord，右键单击此处的文件夹文件并选择部署此源。org 右键单击文件夹，并将此源部署到 org。

我相信那会一直向上。

所以我可以去这里选择

部署此来源组织。

你要小心的一件事是，如果有不止一个人在一个组织中工作，你可以覆盖其他人的更改。因为没有检查服务器上是否有变化，

我变得有点偏执。所以我使用的项目是带有 Visual Studio 代码扩展的 SalesforceDX

我一次只部署一个文件，因为我不想这样做

搞乱其他人

更好的工作流程。

如果你有更好的建议，请随时联系我，你可以在推特上找到我，

[@brettmn](https://dev.to/brettmn) ，或者发邮件【Brett@WIPDeveloper.com】T2。

目前，是什么将此资源部署到或将要部署到组织

看到这条像旋转器一样的线了吗？它说已经成功部署了。

之前，我们部署了

或者我们从 org 中检索了源代码，但是没有得到任何东西。

所以我要删除类文件夹。

把它从硬盘上删掉就行了。现在，如果我选择 package.xml 并检索这个源，则从 org 的清单中检索这个源。

它将把我们刚刚部署的类拉回来。需要记住的一点是，如果我们已经有了一个类，它将会覆盖它。所以要小心一点。

现在我们有了一个包含测试类的类文件夹

和

只是在这里添加一些拼写错误的东西。

现在我保存了它，但没有部署它。所以如果我从 org 中重新获取这个源，它会删除我的东西。

所以需要注意的是

我的一些东西现在不见了

但是

您可以用它来开始使用 SalesforceDX 扩展在沙盒中工作。

## 链接

[有 VS 代码的组织开发模型](https://forcedotcom.github.io/salesforcedx-vscode/articles/user-guide/org-development-model)

## 目前就这些。

记得报名参加 **[每周一次的单口相声！你可以得到我们关于 WIPDeveloper.com 的任何最新信息。](https://wipdeveloper.com/newsletter/)**

帖子[用沙盒使用 SFDX】最早出现在 WIPDeveloper.com](https://wipdeveloper.com/use-sfdx-with-a-sandbox/)的[上。](https://wipdeveloper.com)