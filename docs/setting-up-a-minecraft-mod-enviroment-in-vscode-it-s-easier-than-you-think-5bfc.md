# 在 VSCode 中设置《我的世界》mod 环境(比你想象的要简单！)

> 原文：<https://dev.to/drazisil/setting-up-a-minecraft-mod-enviroment-in-vscode-it-s-easier-than-you-think-5bfc>

嗨！这是我记录的旅程，以创建一个《我的世界》锻造模具。我创建了一个已经有一段时间了，我想看看我是否还记得事物是如何工作的。

此外，我想尝试使用 [VSCode](https://code.visualstudio.com/) 来做这件事，而不是通常推荐的 ide。让我们看看进展如何！

首先，我们来下载 Forge。文件可以在[http://files.minecraftforge.net/](http://files.minecraftforge.net/_)找到，我想我想要 MDK，但是[让我们看看文件是否告诉我们](https://mcforge.readthedocs.io/en/latest/gettingstarted/#from-zero-to-modding)。

是的，MDK。好了，下载 1.12.2 - 14.23.5.2836 的 MDK...

哦，在我们走得太远之前，我计划用 git 提交和这个 readme 来跟踪我的进度。这样，如果东西坏了或奇怪了，我可以用 git 来备份，你和我就可以有一个很好的历史来回顾。

好了，MDK 下载好了，我们解压吧。

文件说只复制`build.gradle`、`gradlew.bat`、`gradlew`和`gradle`目录。我将省略`gradlew.bat`，因为我是在 Linux 上开发的。

下一步是运行`gradlew setupDecompWorkspace`。因为这是 Linux，正确的命令是`./gradlew setupDecompWorkspace`，因为工作目录不在路径中。

好吧，看起来我没有在这台电脑上安装 java 编译器(或 JDK)。您对安装 JDK 的说明可能有所不同，因此我将省略这一部分。DuckDuckGo 是你的朋友。

OpenJDK 8 安装完毕并且`./gradlew setupDecompWorkspace`成功。

我们需要测试的两个命令是:

*   `./gradlew runClient`
*   `./gradlew runServer`

在此之前，我们需要一个模型。Forge 附带了一个样本 mod，但是说明书并没有告诉我们要复制它。让我们这样做，这样我们就有了一个起点，我们可以测试这个设置是否有效

从我们解压缩的 MDK 目录中复制了`src`目录。

`[19:59:27] [Client thread/INFO] [examplemod]: DIRT BLOCK >> minecraft:dirt`

太好了，成功了！此时，在我们提交之前，我们可能想要创建一个`. gitignore 并向其添加一些东西。

在我深入修改之前，我想知道我们也能正确地构建我们的模型。我将使用 [MultiMC](https://multimc.org/) ,因为它可以在所有 3 个主要操作系统上运行，并且可以很容易地使用您想要的版本和 mod 创建《我的世界》实例。

开始了`./gradlew build`

`[20:08:03] [Client thread/INFO] [examplemod]: DIRT BLOCK >> minecraft:dirt`

完美！至此，我们姑且称这个版本为 0.1.0

*这篇文章是我的[熊猫模型](https://github.com/drazisil/pandas#readme)的自述文件的一部分，但鉴于我不完成事情的习惯，我想我应该在这里分享这一部分，以防对其他人有所帮助。欢迎您的想法和反馈。*