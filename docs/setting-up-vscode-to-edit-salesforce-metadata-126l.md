# 设置 VSCode 以编辑 Salesforce 元数据

> 原文：<https://dev.to/katiekodes/setting-up-vscode-to-edit-salesforce-metadata-126l>

一位同事想要编辑 [Salesforce 流程](https://trailhead.salesforce.com/en/content/learn/modules/business_process_automation/flow)背后的 [XML](https://katiekodes.com/tags#xml) ，这样她就可以**快速复制/粘贴整个流程**、**复制/粘贴流程**中的变量，等等。

这一部分很简单，但我需要引导她设置她的计算机，以便与 Salesforce 讨论组织的**配置“元数据”**

这是我发给她的视窗操作说明。

*(抱歉这里还没有截图；我发给同事的截图里有机密信息，我还没有拍新的。然而，Salesforce 在这里截屏了很多硬东西[。)](https://forcedotcom.github.io/salesforcedx-vscode/articles/user-guide/org-development-model)*

* * *

## 无痛而简单

这个过程远没有我想象的那么糟糕。

在一年多的时间里，作为一名 Eclipse 和“Force.com IDE”用户，我认为我必须改变我向 Salesforce 推送代码的方式。

幸运的是，多亏了 Bonny Hinners，我了解到 Salesforce 正在推广的“SFDX [org 开发模型](https://forcedotcom.github.io/salesforcedx-vscode/articles/user-guide/org-development-model)”意味着“除了使用 VSCode 而不是 Eclipse，你所做的一切都不会改变”

超级！不要学习关于 SFDX 封装的新东西！不用担心以新的方式管理 Git 库！

Salesforce 对 Eclipse 的官方支持将在六个月后结束，因此我很高兴自己转而使用 VSCode。

*   *(编辑:我终于[从 Eclipse 迁移到 VSCode](https://katiekodes.com/eclipse-vscode-migrate-git/) 。)*

[![katiekodes image](img/91ff852c8568473209156ff5bd632209.png)](/katiekodes) [## 将我的 Salesforce 组织备份从 Eclipse 迁移到 VSCode，而不会搞乱 Git

### 凯蒂 11 月 7 日 193 分钟阅读

#salesforce #database #vscode #eclipse](/katiekodes/migrating-my-salesforce-org-backups-from-eclipse-to-vscode-without-messing-up-git-4n9)

也感谢[尼克·林德伯格](https://twitter.com/nickersuniverse)和[赞恩·特纳](https://twitter.com/zaynelt)如此有效和坚持不懈地宣传“面向管理员的 vs code”*(我得到了暗示！)*。

* * *

## 电脑管理员权限

请注意，如果您在您的计算机上没有“管理员”权限，您可能需要它们，或者每当安装程序提示您输入“管理员密码”时，您需要 IT 部门的人员来帮助您安装该软件

## 软件安装

根据 [Salesforce 文档](https://forcedotcom.github.io/salesforcedx-vscode/articles/getting-started/install)，您需要在您的计算机上正确安装 4 个软件:

1.  Java SE(“标准版”)JDK(“Java 开发人员工具包”)
    *   不要与更常见的 Java SE JRE(“Java 运行时环境”)相混淆。这个版本是针对编码人员的。难道*你现在*不心动吗？
2.  “Salesforce 命令行界面”或“Salesforce CLI”
3.  Microsoft Visual Studio 代码，简称“VSCode”
4.  Visual Studio 代码的“Salesforce 扩展”插件包

请注意，此流程的任何部分安装失败都可能会影响流程的下一部分。

通常，如果您可以选择是否让安装程序将东西添加到您的“**路径环境变量**，您会希望让它们这样做*(这使得像`java`和`sfdx`这样的代码字被您的计算机的命令行界面识别为有效命令，可以接受选项并做很酷的事情)*。

### 爪哇的 JDK

从 Oracle 下载安装程序[。](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

*   你想要第一个选项。
*   为你的电脑选择合适的安装程序*(优先选择 64 位而不是 32 位安装程序，除非出于某些奇怪的原因你需要 32 位)*。

### [T1】sales force 的 CLI](#salesforces-cli)

从 Salesforce 下载安装程序[。](https://developer.salesforce.com/docs/atlas.en-us.sfdx_setup.meta/sfdx_setup/sfdx_setup_install_cli.htm)

您应该能够通过打开 windows 命令提示符*(键盘上的 Windows 键，`cmd`，enter)* 并键入`sfdx –version`和回车来测试它是否工作。

*   如果你看到关于版本的回复，耶。键入`exit`并按回车键继续前进。
*   如果 Windows 抱怨`sfdx`不是命令，则 JDK 或 Salesforce CLI 安装出现问题。除非你解决了这个问题，否则当你试图在 Visual Studio 中做一些事情时，你不会走得很远。

### 微软的 Visual Studio 代码

从微软下载安装程序[。](https://code.visualstudio.com/)

这是一个“IDE”，或“集成开发环境”

在某些方面，你可以把 IDE 想象成一个增强的文本编辑器，就像 Notepad++一样。

在这种情况下，它是处理代表您的 Salesforce 组织配置方式的“元数据”文本文件的增强文本编辑器。

像 Chrome 一样，有很多 Salesforce 相关的插件可供其使用。

同样像 Chrome 一样，你真的不应该安装一个没有充分理由信任它的插件。

我们将从安装 Salesforce 在接下来的步骤中发布的组件开始。

### 用于 VSCode 的 Salesforce 插件

这将确保你在 VSCode 中有很多友好的按钮点击选项，而不是必须记住一百万个以“`sfdx`”开头的烦人命令。

一旦你安装了 Visual Studio 代码，在你的网络浏览器中访问[Visual Studio 的“市场”](https://marketplace.visualstudio.com/itemdetails?itemName=salesforce.salesforcedx-vscode)的这个页面，点击顶部的大“安装”按钮。

当您的浏览器询问您是否要用 Visual Studio 代码打开链接时，让它打开。

按照任何提示，但我认为你应该很快就可以了。

* * *

## 将 VSCode 连接到 Salesforce 组织

[这个官方文档](https://forcedotcom.github.io/salesforcedx-vscode/articles/user-guide/org-development-model)有下面大部分步骤的截图。

1.  点击 VSCode 左下角的**小设置齿轮**，点击**命令面板**
2.  开始键入“SFDX”并选择“ **SFDX:使用清单**创建项目”
    *   *( **不要**使用普通的“创建项目”，这在选项列表中可能会更靠前)*
3.  在下一个弹出窗口中，键入一个有意义的名字,比如“HEDA-Sandbox-Staging”
    *   *(我的同事正在连接一个名为“Staging”的沙箱，这个沙箱位于我们与 HEDA 一起从零开始建立的一个新组织中)*。
4.  在 Windows 资源管理器的“选择文件夹”弹出窗口中，创建/查找/导航到一个文件夹，该文件夹似乎是硬盘上的一个好位置，用于保存代表您将要下载的组织内容的代码副本。
    *   例如，`C:\Users\YOUR_USERNAME\Documents\Codebases\Salesforce Orgs\`
5.  单击“创建项目”按钮
6.  你会看到一个“欢迎”的东西，左边有一些文件夹导航，可以折叠在短语“HEDA-Sandbox-Staging”下，或者你想出的任何有意义的名称。
    *   此时，如果你要浏览你的硬盘，你会注意到一个名为`C:\Users\YOUR_USERNAME\Documents\Codebases\Salesforce Orgs\HEDA --- Sandbox - Staging\`或类似的文件夹。
7.  点击 VSCode 左下角的**小设置齿轮**，点击**命令面板**
8.  开始输入“auth”并选择“ **SFDX:授权一个组织**
9.  如果您正在连接沙盒，请选择“**沙盒**”。
10.  选择一个**好听的昵称**，比如“ **`heda_sandbox_staging`** ”，然后回车
11.  您的默认网络浏览器会将您带到 Salesforce **登录屏幕**
    *   *(希望你的默认浏览器没有设置为使用保存的密码自动登录到不同的组织。如果没有，放弃要求以您的身份授权“Salesforce CLI”的步骤，关闭 web 浏览器中的自动登录，然后从“**SFDX:Authorize a Org**”重试，我相信。)*
12.  **以您自己的身份登录**到您尝试连接的任何 Salesforce 组织。
13.  单击“**允许**”按钮，让“Salesforce CLI”代表您与 Salesforce 交互
14.  现在，您可以在该组织打开的情况下关闭浏览器标签

* * *

## 下载元数据文件

现在，您需要将代表您的 Salesforce 组织当前状态的称为“元数据”的文本文件下载到您的计算机上，以便轻松编辑其内容。

Salesforce 只允许您直接通过网站的开发人员控制台*(例如 Apex 触发器&类)*编辑您组织中“元数据”的一小部分*。*

所有其他“元数据”代码必须下载到您的计算机上，在那里编辑，如果您想直接编辑它，则重新上传到 Salesforce。

### 指定从 Salesforce 请求哪些文件

1.  回到 VSCode，在左侧，在您刚刚创建并命名的“项目”的导航下，展开名为“ **manifest** 的文件夹，双击“ **package.xml** ，在右侧 VSCode 的文本编辑器区域打开它。
2.  看一下文件。它充满了以“XML”模式格式化的文本。
    *   你知道如何阅读 XML 吗？[如果没有，请查看我的教程](https://dev.to/katiekodes/intro-to-xml-and-json-56co)！
    *   它大概有 30 行长，包含“`types`”标记之间的文本，其代码表示类似“从 Salesforce 下载所有 Apex 类到我的硬盘”、“从 Salesforce 下载所有自定义设置到我的硬盘”等内容。
3.  编辑文件。小心点。如果你紧张，请阅读我的 XML 教程。您要做的是在您需要的 XML 中只留下“`<type>...</type>`”标记集。
    *   删除`<type>...</type>`*你不关心的【标签集】(如果你没有对 Apex 触发器进行编程，你不需要查看它们的详细信息——这只会让下载花费更长的时间)*。
    *   在删除不需要的“`<type>...</type>`”标记集时，请确保不要意外删除 XML 中的“`<version>...</version>`”标记集。
    *   添加新的“`<type>...</type>`”标签集你*做*关心*(学习确切地键入什么是一种艺术，但如果你从一个例子开始，它通常对大多数事情来说是非常直观的)*，例如一个“`Flow`”，如果那是你试图编辑的。
    *   官方文件是[这里](https://developer.salesforce.com/docs/atlas.en-us.eclipse.meta/eclipse/ide_about_package.htm)
    *   这里的[是`package.xml`文件的示例内容的官方大杂烩](https://developer.salesforce.com/docs/atlas.en-us.api_meta.meta/api_meta/manifest_samples.htm)
4.  保存您的工作。*(Windows 中的“Ctrl+S”或文件- >保存)*
5.  您现在可以关闭`package.xml`的编辑器窗口。

例如，如果你只是想要流，你可以删除你的`package.xml`文件的全部内容，并用这个代码*替换它(也许升级版本号，如果你在我写这篇文章之后很久才读到这篇文章)* :

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<Package xmlns="http://soap.sforce.com/2006/04/metadata">
    <types>
        <members>*</members>
        <name>Flow</name>
    </types>
    <version>45.0</version>
</Package> 
```

`package.xml`就这些了！还不错，对吧？

*(如果您希望在对 Salesforce 的后续请求中获取更多或更少的文件，您可以随时重新编辑它。)*

### 向 Salesforce 索要文件

这被称为“检索”代码。

1.  右键单击左侧导航中的“ **`package.xml`** ，然后单击“ **SFDX:从组织**中检索清单中的来源”
2.  当右下角的小状态框显示你已经完成时，关闭左边的“Manifest”并展开文件夹“force-app”，然后“main”，然后“default”，然后“flows”*(假设你打算下载流)*。
3.  你看到很多文件吗？耶！
    *   (如果没有，虚拟检查:您的组织中有*流程或过程构建器，对吗？)*

* * *

## 编辑并重新上传配置文件

### 编辑电脑上的文件

1.  双击它，在 VSCode 文件编辑器中打开它。让它成为一个仍然不活跃的初稿，如果你打破它，你不会太在意。
2.  做一些相对简单的编辑。
    *   例如，复制并粘贴一个“`<variable>...</variable>`”标记集，并在嵌入的“`<name>...</name>`”标记集中键入一个新值。
3.  保存你的工作*(文件- >保存或者 Ctrl+S)* 。
4.  关闭刚刚编辑的文件的编辑器选项卡。

### 将文件上传回 Salesforce

这就是所谓的“部署”代码。

1.  回到左侧的导航菜单，右键单击刚刚编辑的文件，然后单击“ *SFDX: Deploy Source to Org*
    *   不幸的是，您不能用这种方式按住 ctrl 键单击多个文件并同时部署它们。实际上只有一个文件会被部署，即使它们在屏幕上都被选中了。
    *   部署整个文件夹的方法是右键单击文件夹并进行部署。(注意:如果其他人已经通过网络编辑了那些文件，这可能是危险的——你可能会覆盖他们的工作。)
    *   同样，您可以右键单击`package.xml`将*中提到的所有* XML 文件从您的计算机部署到 Salesforce。这有同样的“您可能正在部署旧代码”的警告。
2.  一旦右下方的 VSCode 状态弹出窗口显示您完成了…
3.  打开您的 Salesforce 组织，如果您正在编辑它，请退出您的流程，重新打开它，并查看魔法是否有效！你的资源中有新的变量吗？

* * *

## 随时更新电脑上的文件

现在在 web 界面中玩你的流程。

保存一些更改。

要确保在编辑表示流程的 XML 之前，您的计算机上有一个新的副本:

1.  打开 VSCode
2.  右键单击文件*(或者单击 package.xml for everything)* ，执行“检索”而不是“部署”

### 警告

注意:不要点击旁边的“部署”选项！

不幸的是，“检索”和“部署”紧挨着。

*   **问:**为什么部署会不安全？
*   **答:**如您所知，如果您试图从 Salesforce“检索”新代码，您计算机上的代码副本是过期的。
    *   意外点击“部署”而不是“检索”可能会覆盖您通过网络浏览器完成的艰苦工作，并且没有简单的方法来恢复。

* * *

*   如果你是一名管理员，我希望你能通过一点“代码，而不是点击”来提高自己的工作效率
*   如果您是开发人员，现在您终于可以不那么害怕 Salesforce 终止对 Eclipse 的支持了。

感谢你一路走过来！